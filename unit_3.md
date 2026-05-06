# 📝 PES University - Cloud Computing Past Exam Questions & Solutions
## Collection from QP2-QP17 (Dec 2021 - May 2024)

---

## 📑 TABLE OF CONTENTS

1. [Lustre File System Architecture](#lustre)
2. [GlusterFS Architecture](#glusterfs)
3. [CAP Theorem](#cap)
4. [Replication (Leader-Based & Leaderless)](#replication)
5. [Rebalancing Partitions](#rebalancing)
6. [Consistency Models](#consistency)
7. [Request Routing](#request-routing)
8. [Object Storage (Amazon S3)](#s3)
9. [OpenStack Storage](#openstack)
10. [Block Storage & iSCSI](#block-storage)

---

# 1. LUSTRE FILE SYSTEM ARCHITECTURE {#lustre}

## Question (QP10, QP12, QP2, QP3, QP16)
**Explain Lustre file system architecture with a neat diagram.**

### Answer:

**Lustre Overview:**
Lustre is a distributed file system designed for high-performance, large-scale parallel file storage. It's commonly used in HPC (High-Performance Computing) environments for managing massive datasets with high throughput requirements.

### Lustre Architecture Diagram:

```
                    LUSTRE CLIENT
                   (POSIX Interface)
                    /    |    \
                   /     |     \
                  /      |      \
            QUERY MDT   QUERY MDT  QUERY MDT
                /        |        \
               /         |         \
    ┌─────────────────────────────────────┐
    │   Metadata Server (MDS)             │
    │   ┌──────────────────────────────┐  │
    │   │  Metadata Target (MDT)       │  │
    │   │ (File structure, permissions)│  │
    │   └──────────────────────────────┘  │
    └─────────────────────────────────────┘
               SINGLE POINT
            (Centralized metadata)
                    |
                    |
    ┌───────────────┼───────────────┐
    |               |               |
┌────────┐      ┌────────┐      ┌────────┐
│  OSS1  │      │  OSS2  │      │  OSS3  │
│ ┌────┐ │      │ ┌────┐ │      │ ┌────┐ │
│ │OST1│ │      │ │OST2│ │      │ │OST3│ │
│ └────┘ │      │ └────┘ │      │ └────┘ │
└────────┘      └────────┘      └────────┘
 (Data Store)    (Data Store)    (Data Store)


┌─────────────────────────────────────┐
│         PHYSICAL DISKS              │
│    (Actual file data stored)        │
└─────────────────────────────────────┘
```

### Key Components:

#### 1. **Lustre Client**
- Mounts Lustre filesystem
- Accesses files using POSIX interface (like Unix)
- Communicates with both MDT (for metadata) and OSTs (for data)

#### 2. **Metadata Server (MDS) + Metadata Target (MDT)**
- **Single centralized MDT** (bottleneck risk)
- Manages:
  - File hierarchy (/data/dataset1/file.bin)
  - Permissions and timestamps
  - File-to-OST mapping (Layout Object - LOV)
  - Lock management for concurrent access
- Uses lock-based synchronization

#### 3. **Object Storage Server (OSS) + Object Storage Targets (OSTs)**
- Stores actual file data in chunks
- Multiple OSTs enable **parallel I/O**
- Clients read directly from OSTs
- Can scale horizontally (add more OSTs)

#### 4. **Layout Object (LOV)**
- Maps file offsets and sizes to specific OSTs
- Example: File 1GB split across 4 OSTs:
  - Bytes 0-256MB → OST1
  - Bytes 256-512MB → OST2
  - Bytes 512-768MB → OST3
  - Bytes 768-1024MB → OST4

### How Lustre Works:

```
1. CLIENT REQUEST: "Read /data/big_file.bin (offset=0, size=100MB)"
   ↓
2. QUERY MDT: Get metadata & LOV mapping
   MDT responds: "This file's first 100MB is on OST1"
   ↓
3. PARALLEL READ: Read directly from OST1, OST2, OST3 in parallel
   ↓
4. DATA AGGREGATION: Combine chunks into single file
   ↓
5. RETURN TO CLIENT: Complete file data
```

### Characteristics:

| Aspect | Details |
|--------|---------|
| **Best for** | Large files (GB/TB), high throughput HPC |
| **Metadata** | Centralized (MDT) - bottleneck for many small files |
| **Data** | Distributed across OSTs - parallel I/O |
| **Sync** | Lock-based (strong consistency) |
| **Scalability** | Limited by MDT for metadata-heavy ops |
| **Use case** | Genome sequencing, climate modeling, scientific computing |

### Strengths:
✅ Excellent throughput for large files  
✅ Parallel data I/O (multiple OSTs)  
✅ Suitable for HPC workloads  
✅ POSIX-compliant access  

### Weaknesses:
❌ Centralized MDT bottleneck  
❌ Poor performance for many small files  
❌ Complex setup and management  
❌ No natural multi-site replication  

---

# 2. GLUSTERFS ARCHITECTURE {#glusterfs}

## Questions (QP14, QP4, QP7)
**Explain GlusterFS file system architecture with a neat diagram. How is it different from Lustre?**

### Answer:

**GlusterFS Overview:**
GlusterFS is a distributed file system designed for cloud storage environments. Unlike Lustre's centralized metadata, GlusterFS uses **distributed metadata**, making it more scalable and fault-tolerant.

### GlusterFS Architecture Diagram:

```
    ┌─────────────┬─────────────┬─────────────┐
    │  GLUSTER    │  GLUSTER    │  GLUSTER    │
    │  CLIENT A   │  CLIENT B   │  CLIENT C   │
    └──────┬──────┴──────┬──────┴──────┬──────┘
           │             │             │
      (Mount via NFS, SMB, or native client)
           │             │             │
    ┌──────┴──────┬──────┴──────┬──────┴──────┐
    │             │             │             │
┌──────────┐ ┌──────────┐ ┌──────────┐
│ Brick 1  │ │ Brick 2  │ │ Brick 3  │
│(Node1)   │ │(Node2)   │ │(Node3)   │
│┌────────┐│ │┌────────┐│ │┌────────┐│
││Storage ││ ││Storage ││ ││Storage ││
││(ext4)  ││ ││(ext4)  ││ ││(ext4)  ││
│└────────┘│ │└────────┘│ │└────────┘│
└──────────┘ └──────────┘ └──────────┘
   + Server     + Server     + Server
   + DAS/SAN    + DAS/SAN    + DAS/SAN
   + CPU/RAM    + CPU/RAM    + CPU/RAM

┌─────────────────────────────────────┐
│      TRANSLATORS (Filters)          │
├─────────────────────────────────────┤
│ • AFR: Automatic File Replication   │
│ • I/O Scheduler: Load balancing     │
│ • Performance: Caching              │
│ • Protocol: FUSE, NFS, SMB          │
└─────────────────────────────────────┘
        ↓
┌─────────────────────────────────────┐
│    INFINIBAND or TCP/IP             │
│    (Interconnect between bricks)    │
└─────────────────────────────────────┘
```

### Key Components:

#### 1. **Storage Brick**
```
Brick = Server + Storage + Filesystem

Example: node1.company.com:/mnt/brick1
  - node1.company.com: Physical server
  - /mnt/brick1: Mount point
  - Storage: Can be DAS (direct) or SAN (network)
  - Filesystem: ext3, ext4, XFS
```

#### 2. **Distributed Metadata**
- NO central metadata server
- Each brick maintains metadata for its data
- Metadata distributed across all bricks
- Query any brick, get complete metadata

#### 3. **Translators (Filters)**
Implement functionality through modular translators:

**AFR Translator (Automatic File Replication):**
```
Write to File:
  Writes to Brick1 AND Brick2 (replication)
  If Brick1 fails → File still available on Brick2
  
Similar to RAID 1 mirroring
```

**I/O Scheduler Translator:**
- Distributes I/O load across bricks
- Load balancing for read operations

**Performance Translator:**
- Client-side caching
- Reduces network requests

**Protocol Translator:**
- FUSE: Native client mounting
- NFS: Network File System
- SMB: Windows file sharing
- REST: HTTP access

#### 4. **Interconnect**
- Bricks communicate via Infiniband (fast) or TCP/IP
- Each brick independently operational
- Can tolerate node failures

### GlusterFS vs Lustre Comparison:

| Feature | GlusterFS | Lustre |
|---------|-----------|--------|
| **Metadata** | Distributed | Centralized (MDT) |
| **Single Point of Failure** | No SPOF | Has SPOF (MDT) |
| **Small Files Performance** | Good | Poor (MDT bottleneck) |
| **Large Files Performance** | Good | Excellent (optimized) |
| **Scalability** | Better (distributed) | Limited (MDT) |
| **Setup Complexity** | Moderate | High |
| **Storage Unit** | Bricks (server+storage) | OSTs (data only) |
| **Replication** | Native (AFR translator) | Not native |
| **Client Requirement** | Optional (use NFS/SMB) | Required (Lustre client) |
| **Use Case** | Cloud storage, general purpose | HPC, scientific computing |
| **Cost** | Lower (commodity hardware) | Higher (specialized) |
| **Lock Mechanism** | Quorum-based | Lock-based |

### How GlusterFS Works:

```
CLIENT REQUEST: "Write /data/file.txt"
    ↓
HASH FUNCTION: hash(file.txt) → Brick mapping
    ↓
DISTRIBUTED LOOKUP: Query any brick for metadata
    ↓
WRITE OPERATION: Write to Brick1 AND Brick2 (AFR)
    ↓
ACKNOWLEDGMENT: Return success once replicated
```

### GlusterFS Characteristics:

| Aspect | Details |
|--------|---------|
| **Architecture** | Peer-to-peer, no master |
| **Data Distribution** | Hash-based placement |
| **Replication** | Configurable (1x, 2x, 3x via AFR) |
| **Scalability** | Horizontal (add bricks) |
| **Fault Tolerance** | High (distributed metadata) |
| **Access Method** | Native, NFS, SMB, HTTP |
| **Typical Deployment** | 3+ bricks for replication |

### When to Use GlusterFS:
✅ Cloud storage environments  
✅ Multi-site deployments  
✅ Environments requiring high availability  
✅ Variable workload (small + large files)  
✅ Need for horizontal scaling  

---

# 3. CAP THEOREM {#cap}

## Questions (QP10, QP2, QP3, QP4, QP5, QP14, QP17)
**Briefly explain CAP theorem. How would you choose a database for your application based on CAP theorem? Discuss 3 desirable properties and practical implications.**

### Answer:

### CAP Theorem Statement:

**In a distributed system, you can guarantee at most 2 of 3 properties simultaneously:**

```
┌─────────────────────────────────────────┐
│           CAP THEOREM                   │
├─────────────────────────────────────────┤
│  C: Consistency                         │
│     (All nodes see latest value)        │
│                                         │
│  A: Availability                        │
│     (System always responds)            │
│                                         │
│  P: Partition Tolerance                 │
│     (Works despite network failures)    │
└─────────────────────────────────────────┘

Can pick at most 2 → 3 combinations:

1. CP: Consistency + Partition Tolerance
   (Sacrifice Availability)
   
2. AP: Availability + Partition Tolerance
   (Sacrifice Consistency)
   
3. CA: Consistency + Availability
   (Sacrifice Partition Tolerance)
```

### Why P is Mandatory in Cloud:

**Partition Tolerance = System works despite network partitions**

Network partitions are **inevitable** in cloud computing:
- Network failures happen
- Distributed systems are across multiple datacenters
- Firewalls, switches can fail

**Therefore: P cannot be sacrificed in cloud systems**

**Choice is between:**
- **CP:** Consistency over Availability
- **AP:** Availability over Consistency

### 3 Desirable Properties & Trade-offs:

#### 1. CONSISTENCY (C)
```
Definition: All clients see the same data at the same time.

Guarantee: Once write committed, all subsequent reads see new value.

Trade-off:
  If partition occurs:
    - CP system: Returns error (unavailable but consistent)
    - Example: User A writes → User B must wait for confirmation
    
Practical Implication:
  ✓ Safe for critical operations (banking, healthcare)
  ✗ Higher latency (wait for synchronization)
  ✗ Reduced availability (partial system goes offline)
```

#### 2. AVAILABILITY (A)
```
Definition: System always responds to requests.

Guarantee: Every request gets a response (success or failure).

Trade-off:
  If partition occurs:
    - AP system: Returns possibly stale data (available but inconsistent)
    - Example: User A and B in separated partitions see different data
    
Practical Implication:
  ✓ Low latency (immediate response)
  ✓ High availability (99.9%+ uptime)
  ✗ Risk of serving stale data
  ✗ Complex conflict resolution
```

#### 3. PARTITION TOLERANCE (P)
```
Definition: System works despite network partitions.

Guarantee: Continues operating when nodes cannot communicate.

Trade-off:
  Unavoidable in distributed systems:
    - Must partition nodes into independent groups
    - Each group operates independently during partition
    - Reconciliation when partition heals
    
Practical Implication:
  ✓ Necessary for distributed systems
  ✗ Forces choice between C and A
  ✗ Requires conflict resolution mechanism
```

### CAP Choices by Database Type:

#### CP Systems (Consistency + Partition Tolerance)
```
Sacrifice: Availability

Databases:
  • ZooKeeper (Apache)
  • HBase (Hadoop)
  • Google BigTable
  • PostgreSQL (single-leader, synchronous replication)

Behavior During Partition:
  Partition 1 (has quorum): Processes normally
  Partition 2 (no quorum): Returns "UNAVAILABLE" error

Characteristics:
  ✓ Strong consistency
  ✓ Data never stale
  ✗ Unavailable if quorum lost
  ✗ Lower throughput

Use Cases:
  • Financial transactions (money must not be lost)
  • Medical records (correctness critical)
  • Government databases (audit trail critical)

Example Scenario:
  Banking transfer: Account A ↔ Account B
  CP ensures: Both decrease and increase happen, or neither
  If partition: Transaction waits/fails (safe but unavailable)
```

#### AP Systems (Availability + Partition Tolerance)
```
Sacrifice: Consistency

Databases:
  • Cassandra (NoSQL)
  • CouchDB (Document)
  • Riak (Key-value)
  • DynamoDB (Amazon)
  • MongoDB (eventual consistency mode)

Behavior During Partition:
  Partition 1: Serves all requests (may be stale)
  Partition 2: Serves all requests (may be stale)
  After merge: Reconciliation happens

Characteristics:
  ✓ Always available
  ✓ High performance
  ✓ Horizontal scalability
  ✗ Eventual consistency (data eventually converges)
  ✗ Complex conflict resolution

Use Cases:
  • Social media feeds (staleness acceptable)
  • User profiles (eventual consistency ok)
  • Web caches (miss acceptable)
  • Real-time analytics (approximate ok)

Example Scenario:
  Social media like/comment count:
  AP system: Like counter shows different values on different servers
  Eventually: All servers agree on final count (takes seconds/minutes)
```

#### CA Systems (Consistency + Availability)
```
Sacrifice: Partition Tolerance

Databases:
  • Traditional SQL (PostgreSQL, MySQL)
  • Oracle
  • Microsoft SQL Server

Limitation:
  Only work in LAN (Local Area Network)
  Assume network is reliable (rare in cloud)

Behavior:
  If partition occurs: BOTH fail
  Cannot partition data (no partition tolerance)

Use Cases:
  • Single-datacenter deployments
  • Private networks (not cloud)
  • Traditional enterprise (on-premise)

Modern Reality:
  Most clouds use CP or AP, not CA
```

### How to Choose Database Based on CAP:

#### Decision Tree:

```
1. Can tolerate temporary unavailability?
   
   YES → Choose CP (Consistency + Partition)
   ├─ Need strong consistency
   ├─ Data correctness is critical
   ├─ Banking, healthcare, legal
   └─ Databases: ZooKeeper, HBase, PostgreSQL (sync)
   
   NO → Choose AP (Availability + Partition)
   ├─ Need 99.9% uptime always
   ├─ Staleness is acceptable
   ├─ Social media, caches, feeds
   └─ Databases: Cassandra, DynamoDB, CouchDB

2. Secondary consideration: Scale requirement
   
   Small scale (few GB)
   ├─ CP or AP both ok
   └─ Performance difference minimal
   
   Large scale (TB+)
   ├─ AP preferred (better scaling)
   └─ CP may have scalability issues
```

### Practical Application Examples:

#### Example 1: E-commerce Checkout System

**Requirements:**
- Customer clicks "Buy Now"
- Must process payment immediately
- Cannot lose order
- Inventory must be accurate

**CAP Choice:** **CP (Consistency)**

**Why:** Correctness > Availability. Money/inventory cannot be wrong.

**Database:** PostgreSQL with synchronous replication OR HBase

**Trade-off:** Accepts brief checkout timeouts during failures

---

#### Example 2: Social Media Feed

**Requirements:**
- Users always see their feed (even during outage)
- Staleness of 1-2 seconds acceptable
- Must handle millions of concurrent users
- Geographic distribution (multi-datacenter)

**CAP Choice:** **AP (Availability)**

**Why:** Uptime > Consistency. Users accept outdated likes/comments briefly.

**Database:** Cassandra, DynamoDB, or CouchDB

**Trade-off:** Occasional conflicts resolved by Last-Write-Wins (LWW)

---

#### Example 3: Mobile App Offline Mode

**Requirements:**
- Works offline locally
- Syncs when reconnected
- Different versions may exist on multiple devices

**CAP Choice:** **AP (Availability)**

**Why:** Must work offline (availability). Sync conflicts resolved later.

**Conflict Resolution Strategy:**
- App stores latest version number
- On sync: Merge changes, last-write-wins
- Show conflict to user if critical

---

### Summary: Choosing CAP

| Scenario | CAP | Database | Example |
|----------|-----|----------|---------|
| Money, medical, legal | CP | PostgreSQL, HBase | Banking |
| Social, web, mobile | AP | Cassandra, DynamoDB | Twitter, Facebook |
| Real-time gaming | AP | DynamoDB | Game leaderboards |
| Enterprise single DC | CA | PostgreSQL | Internal tools |

**Key Insight:** In cloud, P is mandatory → **Always choose between CP and AP based on your application needs.**

---

# 4. REPLICATION (LEADER-BASED & LEADERLESS) {#replication}

## Questions (QP10, QP3, QP12, QP6, QP17)
**What is replication in a distributed environment? Explain leader-based and leaderless replication techniques.**

### Answer:

### Replication Overview:

**Definition:** Replication is the process of maintaining multiple copies of data across different nodes/servers in a distributed system.

**Why Replication is Needed:**

```
1. Availability
   - If one node fails, others have copy
   - System continues operating
   
2. Fault Tolerance
   - Survive disk failures
   - Survive node crashes
   
3. Read Performance
   - Multiple copies can serve reads in parallel
   - Reduce latency (read from nearest node)
   
4. Low Latency
   - Place copies close to users geographically
   - Reduce network distance
```

### Types of Replication Based on Leaders:

```
Number of Leaders:

1. Single Leader (Master-Slave)
   └─ One master, multiple slaves
   
2. Multi-Leader (Master-Master)
   └─ Multiple masters, each accepting writes
   
3. Leaderless
   └─ All nodes equal, all accept writes
```

---

## LEADER-BASED REPLICATION

### Architecture:

```
                    LEADER (Master)
                  Handles: Reads + Writes
                       |
                       |
                   Replication Log
                   (Statement or WAL)
                       |
        ┌──────────────┼──────────────┐
        |              |              |
    FOLLOWER1      FOLLOWER2      FOLLOWER3
    (Slave)        (Slave)        (Slave)
    Read-only      Read-only      Read-only
    (Applies       (Applies       (Applies
     changes)      changes)       changes)
```

### Components:

#### 1. **Leader (Master/Primary)**
- Accepts all write requests from clients
- Executes transactions
- Generates replication log
- Broadcasts changes to followers

#### 2. **Followers (Slaves/Secondaries)**
- Receive replication log from leader
- Apply changes in **same order** (critical for consistency)
- **Read-only** (cannot accept writes directly)
- Optional: Can serve read requests

#### 3. **Replication Log**
- Records all writes in order
- Options:
  1. Statement-based: SQL statement (simple)
  2. WAL Shipping: Write-ahead log (storage-engine specific)
  3. CDC: Logical log (portable)
  4. Trigger-based: Custom logic (complex)

### How Leader-Based Replication Works:

```
EXAMPLE: Update user name in database

1. CLIENT REQUEST
   Client: "UPDATE users SET name='Alice' WHERE id=1"
   
2. LEADER EXECUTES
   Leader: Executes query
   Result: User 1's name changed to 'Alice'
   
3. GENERATE REPLICATION LOG
   Log entry: "id=1, old_name=Bob, new_name=Alice, timestamp=123456"
   
4. BROADCAST TO FOLLOWERS
   Leader sends log to Follower1, Follower2, Follower3
   
5. FOLLOWERS APPLY (In Order!)
   Follower1: Apply same change → id=1 name='Alice'
   Follower2: Apply same change → id=1 name='Alice'
   Follower3: Apply same change → id=1 name='Alice'
   
6. ACKNOWLEDGMENT
   Followers: Send "DONE" to leader
   
7. CLIENT RESPONSE
   Leader: Returns "UPDATE successful"
```

### Synchronous vs Asynchronous Replication:

#### SYNCHRONOUS Replication:

```
┌─────────────────────────────────────────┐
│ Leader                                  │
│                                         │
│ Write Data                              │
│   ↓                                     │
│ Send to Followers (WAIT)                │
│   ↓                                     │
│ Follower1: "ACK"                        │
│ Follower2: "ACK"                        │
│ Follower3: "ACK"                        │
│   ↓                                     │
│ Acknowledge Client: "Write successful"  │
└─────────────────────────────────────────┘

Latency: Higher (wait for all followers)
Safety: Higher (all replicas updated)
Availability: Lower (slow followers block)
```

**Trade-off:** Strong consistency but higher latency

#### ASYNCHRONOUS Replication:

```
┌─────────────────────────────────────────┐
│ Leader                                  │
│                                         │
│ Write Data                              │
│   ↓                                     │
│ Acknowledge Client IMMEDIATELY         │
│ (Don't wait for followers)              │
│   ↓                                     │
│ Send to Followers (Background)          │
│   ↓                                     │
│ Followers eventually apply              │
│ (May take seconds/minutes)              │
└─────────────────────────────────────────┘

Latency: Low (immediate response)
Safety: Lower (data may be lost if leader crashes)
Availability: Higher (don't wait)
```

**Trade-off:** Low latency but risk of data loss

### Handling Failures:

#### Follower Failure (Catch-Up Recovery):
```
Follower crashes and recovers:
  - Last applied log position: entry #100
  - Leader current position: entry #150
  - Catch-up: Request entries 101-150
  - Apply in order
  - Back in sync
```

#### Leader Failure (Failover):
```
Automatic Failover Process:

1. DETECTION
   Followers: "No heartbeat from leader for 30 seconds"
   → Leader is dead
   
2. ELECTION
   Followers vote to elect new leader
   → Choose follower with most recent data
   
3. RECONFIGURATION
   All followers point to new leader
   Old leader was: Leader → New follower
   
4. RISKS
   Split brain: Both leaders active → conflicts
   Data loss: If new leader has less data than failed leader
```

### Leader-Based Replication Trade-offs:

| Aspect | Pro | Con |
|--------|-----|-----|
| **Setup** | Simple | Requires failover logic |
| **Consistency** | Strong (all see same order) | Eventual (lag before sync) |
| **Latency** | Low (leader nearby) | Varies with followers |
| **Scalability** | Limited by leader throughput | Leader is bottleneck |
| **Failover** | Automatic or manual | Risk of data loss |

**Use cases:** Traditional databases (PostgreSQL, MySQL), relational systems

---

## LEADERLESS REPLICATION

### Architecture:

```
All nodes are EQUAL - no master/slave distinction

CLIENT writes to multiple nodes simultaneously

    ┌──────────────┐
    │   CLIENT     │
    └──────────────┘
    /      |       \
   /       |        \
NODE1    NODE2    NODE3
(Write)  (Write)  (Write)
(Read)   (Read)   (Read)
(Equal)  (Equal)  (Equal)
```

### Key Concept: Quorum

**Write Quorum (w):**
- Number of nodes that must acknowledge write
- Write considered successful if w nodes confirm
- Example: w = 2 (out of 3 nodes)
  - Write to Node1 ✓
  - Write to Node2 ✓
  - Write to Node3 pending...
  - Success! (2 out of 3)

**Read Quorum (r):**
- Number of nodes queried for read
- Return most recent value seen
- Example: r = 2 (out of 3 nodes)
  - Read from Node1: value=10, version=5
  - Read from Node2: value=10, version=5
  - Result: 10 (confirmed by majority)

**Quorum Condition:** w + r > n

Example for n=3:
```
n=3 nodes

Option 1: w=2, r=2
  Write quorum: 2 nodes must confirm
  Read quorum: 2 nodes must be queried
  2 + 2 > 3 ✓ Overlap guaranteed

Option 2: w=1, r=3
  Write quorum: 1 node (risky)
  Read quorum: all 3 nodes
  1 + 3 > 4 ✓ But not efficient

Option 3: w=3, r=1
  Write quorum: all nodes
  Read quorum: 1 node (risky)
  3 + 1 > 3 ✓ But writes slow

Standard: w = r = (n+1)/2
  For n=3: w=2, r=2
  For n=5: w=3, r=3
```

### How Leaderless Replication Works:

```
SCENARIO: 3 nodes, w=2, r=2

WRITE REQUEST (Client wants to write value=100)
┌────────────────────────────────────────────────┐
│ Client sends write to Node1, Node2, Node3      │
├────────────────────────────────────────────────┤
│ Node1: Accepts, stores (version=1)      ✓      │
│ Node2: Accepts, stores (version=1)      ✓      │
│ Node3: Slow/offline, doesn't respond    ✗      │
├────────────────────────────────────────────────┤
│ Client: 2 acks received (w=2 satisfied)        │
│ → "Write successful"                           │
└────────────────────────────────────────────────┘

READ REQUEST (Client wants latest value)
┌────────────────────────────────────────────────┐
│ Client reads from Node1, Node2, Node3          │
├────────────────────────────────────────────────┤
│ Node1: Returns value=100, version=1     ✓      │
│ Node2: Returns value=100, version=1     ✓      │
│ Node3: Returns old value=50, version=0  ✓      │
├────────────────────────────────────────────────┤
│ Client: Pick highest version (1) → value=100  │
│ → "Read value=100"                            │
└────────────────────────────────────────────────┘
```

### Handling Stale Reads:

**Problem:** Node3 didn't get the write (was offline)

**Solution 1: Read Repair**
```
On read:
  Client reads from 3 nodes
  Gets value=100 (v1) from Node1, Node2
  Gets value=50 (v0) from Node3
  
  Detects: Node3 is stale
  Action: Write value=100 (v1) to Node3
  
  Next read: All nodes have latest
```

**Solution 2: Anti-Entropy (Gossip Protocol)**
```
Nodes periodically compare data:
  Node1: "I have entries 1-100"
  Node2: "I have entries 1-95"
  
  Identify: Node2 missing 96-100
  Action: Node1 sends missing entries
  
  Eventually: All nodes converge
```

### Handling Concurrent Writes:

**Problem:** Two clients write different values simultaneously

```
CONCURRENT WRITE CONFLICT:

Time 1:
  Client A: Write value=100 (to Node1, Node2)
  Client B: Write value=200 (to Node2, Node3)
  
Result:
  Node1: value=100 (A won)
  Node2: value=200 (B won, later time)
  Node3: value=200 (B won)
  
Conflict: What is the true value?
```

**Solutions:**

1. **Last-Write-Wins (LWW)**
   - Client adds timestamp
   - Higher timestamp wins
   - Risk: Lose one write

2. **Happens-Before**
   - Track causality
   - If A's write caused B's write, respect order

3. **Concurrent Handling**
   - Store both versions
   - Return to application
   - App resolves (e.g., merge logic)

### Version Vectors:

```
Track causality with version numbers

Format: [Node1_version, Node2_version, Node3_version]

Write by Node1: version = [1, 0, 0]
Write by Node2: version = [0, 1, 0]
Write by Node1 again: version = [2, 0, 0]

Concurrent (no causality):
  A: [1, 1, 0]
  B: [1, 0, 1]
  → Neither < other → concurrent conflict

Sequential (causality exists):
  A: [1, 0, 0]
  B: [1, 1, 0]
  → A < B → B happened after A
```

### Leaderless Replication Examples:

**Real-world systems using leaderless:**
- Amazon Dynamo
- Cassandra
- Riak

### Leaderless Trade-offs:

| Aspect | Pro | Con |
|--------|-----|-----|
| **Single Point of Failure** | None (all equal) | Harder to monitor |
| **Scalability** | High (distributed) | Complex conflict resolution |
| **Consistency** | Eventual | Replication lag |
| **Write Performance** | Fast (quorum) | Risk of conflicts |
| **Read Performance** | Parallel | Need quorum read |
| **Latency** | Low | Staleness possible |

**Use cases:** Cloud databases (DynamoDB), NoSQL (Cassandra), mobile apps

---

# 5. REBALANCING PARTITIONS {#rebalancing}

## Questions (QP10, QP2, QP3, QP4, QP6, QP17)
**What is rebalancing of partitions? Why is it necessary? Explain dynamic partitioning and partitioning proportional to nodes.**

### Answer:

### Rebalancing Definition:

**Rebalancing:** Process of redistributing data across nodes when cluster topology changes.

```
NEED FOR REBALANCING:

1. Node Addition
   - New node joins cluster
   - Must get share of data
   
2. Node Removal
   - Node fails or leaves
   - Its data redistributed
   
3. Load Increase
   - More queries than before
   - Need more nodes
   
4. Hotspot (Uneven Load)
   - Some partitions overloaded
   - Rebalance to distribute load
```

### Why Rebalancing is Necessary:

```
WITHOUT REBALANCING:

Database grows from 1TB to 10TB
  Original: 1 node
  Needed: 10 nodes
  
  Problem: Oldest node holds ALL 10TB
  → Single bottleneck
  → Queries slow
  → Reads still hit same node
  
Solution: Rebalance
  - Distribute 10TB across 10 nodes
  - 1TB per node
  - Parallel queries (10x faster)
```

### Rebalancing Goals:

1. **Fair load distribution**
   - Each partition gets similar size
   - Query load distributed

2. **No downtime**
   - Data transfers in background
   - Application keeps running

3. **Minimal data movement**
   - Only move necessary data
   - Fast rebalancing

4. **Optimal cost**
   - Avoid unnecessary moves
   - Preserve cache hit rates

---

## FOUR REBALANCING STRATEGIES:

### Strategy 1: Hash Mod N (Avoid This!)

```
Partitioning:
  Partition = hash(key) mod N
  Where N = number of nodes

Example: N = 10 nodes
  key1: hash(key1) % 10 = 3 → Node 3
  key2: hash(key2) % 10 = 7 → Node 7
  key3: hash(key3) % 10 = 3 → Node 3

Add new node: N = 11
  key1: hash(key1) % 11 = 5 → Node 5 (MOVED!)
  key2: hash(key2) % 11 = 3 → Node 3 (MOVED!)
  key3: hash(key3) % 11 = 8 → Node 8 (MOVED!)

RESULT: ~90% of keys must move
```

**Characteristics:**
- ❌ Most expensive
- ❌ Almost all keys remapped
- ❌ Cache invalidation massive
- ❌ Network congestion

**Verdict:** AVOID for dynamic clusters

---

### Strategy 2: Fixed Number of Partitions

```
PRECOMPUTE partition count at startup:
  Create 1000 partitions upfront (fixed)
  Add nodes dynamically
  Partition assignment changes, but partition count stays same

EXAMPLE: 3 nodes initially

Initial Assignment:
  Partitions 1-333:   Node1
  Partitions 334-666: Node2
  Partitions 667-1000: Node3

Add 4th node:
  Partitions 1-100:   Node1
  Partitions 101-250: Node2
  Partitions 251-400: Node3
  Partitions 401-500: Node4
  Partitions 501-1000: remaining
  (Rebalance: steal partitions from each)

Process:
  1. Mark "stolen" partitions
  2. During data transfer: partition serves old owner
  3. After transfer: partition serves new owner
  4. No downtime during transition
```

**Characteristics:**

| Aspect | Detail |
|--------|--------|
| **Partition Count** | Fixed from start |
| **Data Movement** | Partition-level (not key-level) |
| **Setup** | Requires upfront planning |
| **Adding Nodes** | Steal partitions from existing nodes |
| **Performance** | Efficient (partition-level moves) |
| **Scalability** | Good for predictable growth |

**Example Sizing:**
```
Expected max nodes: 100
Expected max data: 10TB

Partition count = (10TB / typical partition size) * buffer factor
                = (10TB / 100MB) * 1.5
                = 150,000 partitions

Each node with 100GB will have ~15 partitions
```

**Trade-offs:**
- ✅ Simple to implement
- ✅ Efficient rebalancing
- ✅ Minimal data movement
- ❌ Requires upfront sizing
- ❌ Fixed partition count (difficult to adjust)

**When to use:** Predictable growth, known max nodes

---

### Strategy 3: Dynamic Partitioning

```
START: Single partition
  Partition 1: All data (0% to 100%)

AS DATA GROWS:
  When Partition 1 reaches size_limit:
    Split into 2: [0-50%), [50-100%)
    
  If Partition [0-50%) gets big:
    Split into 4: [0-25%), [25-50%)

RESULT: Tree of partitions

Visual:
          [All data]
              |
        (Size > limit?)
         /           \
    [0-50%)         [50-100%)
    /    \          /      \
[0-25%) [25-50%) [50-75%) [75-100%)


AS DATA SHRINKS:
  If Partition [0-25%) becomes tiny:
    Merge with [25-50%)
```

**Characteristics:**

| Aspect | Detail |
|--------|--------|
| **Start** | Single partition |
| **Growth** | Auto-split when threshold reached |
| **Shrinking** | Auto-merge when too small |
| **Partition Count** | Dynamic, adjusts to data volume |
| **Adaptability** | Excellent (responds to volume) |
| **Initial Overhead** | Single partition (inefficient initially) |

**Algorithm:**
```
1. New partition starts empty
2. Data accumulates
3. When size > MAX_SIZE:
   - Split into 2 equal parts
   - Both have half the data
   - Rebalance between nodes
4. When size < MIN_SIZE:
   - Merge with adjacent partition
   - Redistribute combined data
```

**Advantages:**
- ✅ Automatic adjustment to data volume
- ✅ No upfront sizing needed
- ✅ Adaptable to workload changes
- ✅ Minimal over-provisioning

**Disadvantages:**
- ❌ Complex implementation
- ❌ Initially inefficient (single partition)
- ❌ Split/merge overhead
- ❌ Risk of imbalance if thresholds wrong

**When to use:** Variable data volume, unpredictable growth

---

### Strategy 4: Partitioning Proportional to Nodes

```
FIXED PARTITIONS PER NODE:
  Each node gets fixed number of partitions
  Add node → Stealpartitions from existing nodes

EXAMPLE: 3 nodes, 30 partitions

Initial:
  Each node gets 10 partitions
  Node1: Partitions 1-10
  Node2: Partitions 11-20
  Node3: Partitions 21-30

Add 4th node:
  Total nodes: 4
  Partitions per node: 30 / 4 = 7.5 ≈ 8
  Rebalance to 8 per node:
  
  New assignment:
    Node1: 8 partitions (choose 8 of 10)
    Node2: 8 partitions (choose 8 of 10)
    Node3: 8 partitions (choose 8 of 10)
    Node4: 8 partitions (steal from others)

Process:
  From Node1: Steal 2 partitions → give to Node4
  From Node2: Steal 2 partitions → give to Node4
  From Node3: Steal 2 partitions → give to Node4
  
  Result: 8 per node, balanced
```

**Characteristics:**

| Aspect | Detail |
|--------|--------|
| **Ratio** | Constant: partitions_per_node = total_partitions / node_count |
| **Adding Nodes** | Auto-steal partitions to rebalance |
| **Removing Nodes** | Their partitions distributed to others |
| **Scaling** | Linear (add N nodes → add N * (partitions_per_node)) |
| **Balance** | Maintains proportional distribution |

**Formula:**
```
Total Partitions = P (fixed)
Number of Nodes = N (variable)
Partitions per Node = P / N (always proportional)

Add 1 node: N → N+1
  Per-node changes: P/N → P/(N+1) (slightly less)
  Automatically rebalances
```

**Algorithm:**
```
Add new node X:

1. Compute new partition count per node:
   old_per_node = P / N
   new_per_node = P / (N+1)
   
2. Calculate partitions to steal:
   steal_per_existing = old_per_node - new_per_node
   steal_total = N * steal_per_existing
   
3. Select random partitions from existing nodes
   Give to new node X

4. New node X gets: steal_total partitions
```

**Example Calculation:**
```
P = 1000 partitions, N = 3 nodes

Old state: 1000/3 ≈ 333 per node
New state: 1000/4 = 250 per node

Each existing node gives: 333 - 250 = 83 partitions
Total stolen: 3 * 83 = 249 ≈ 250 for Node4

After: Each node has 250 partitions
```

**Advantages:**
- ✅ Automatic rebalancing
- ✅ Proportional distribution
- ✅ Works for any cluster size
- ✅ Simple proportional logic

**Disadvantages:**
- ❌ Requires fixed total partitions
- ❌ Random selection may cause imbalance initially
- ❌ Can spread hot partitions

**When to use:** Elastic clusters, cloud environments, dynamic scaling

---

## COMPARISON OF REBALANCING STRATEGIES:

| Strategy | Efficiency | Complexity | Use Case |
|----------|-----------|-----------|----------|
| Hash Mod N | ❌ Very low (~90% remapped) | Low | Avoid! |
| Fixed Partitions | ✅ High (partition-level) | Medium | Known growth |
| Dynamic | ✅ High (auto-split) | High | Variable volume |
| Proportional | ✅ High (proportional) | Medium | Cloud elastic |

---

# 6. CONSISTENCY MODELS {#consistency}

## Questions (QP2, QP7, QP15)
**What is a consistency model? Explain strict, sequential, causal, and PRAM consistency.**

### Answer:

### Consistency Model Definition:

**Consistency Model:** Formal guarantee about the order in which clients see writes to shared data across multiple nodes in a distributed system.

```
Question: When write happens on one node,
when do other nodes see it?

Answer: Depends on consistency model.
```

### Five Consistency Models (Weakest to Strongest):

#### 1. EVENTUAL CONSISTENCY (Weakest)

```
Definition:
  All replicas eventually have the same value.
  No guarantee on WHEN.

Example:
  User likes photo → Write to cache → Broadcast to replicas
  After 1 second: 50% see like
  After 5 seconds: 90% see like
  After 10 seconds: 100% see like

Guarantee: Eventually consistent, but timing unknown

Timeline:
  T=0: Write on Node1
  T=1: Node2 sees it (maybe)
  T=3: Node3 sees it (maybe)
  T=5: All see it (eventually)
```

**Characteristics:**
- ✅ Highest performance (no waiting)
- ✅ High availability
- ❌ Reads can return stale data
- ❌ No ordering guarantee

**Use cases:** Social media likes, web caches

---

#### 2. PRAM CONSISTENCY (Pipelined RAM)

```
Definition:
  Single process writes appear in order.
  Different processes' writes can be in any order.

Guarantee Per Process:
  Process A writes: 1, then 2, then 3
  All observers see: 1, 2, 3 (in this order)

Guarantee Across Processes:
  Process A writes: X
  Process B writes: Y
  Order can be: X,Y or Y,X (depending on observer)

Example:

Process A (Writer):
  write(x, 1) → send to all
  write(x, 2) → send to all
  write(x, 3) → send to all

Observer 1:
  sees: 1, 2, 3 (Process A's order preserved)

Observer 2:
  sees: 1, 2, 3 (Process A's order preserved)

Different observer:
  sees Process B's writes in order too
  but A's and B's order might differ
```

**Characteristics:**
- ✅ Better than eventual
- ✅ Single-process order guaranteed
- ❌ Cross-process order undefined
- ❌ Confusing semantics

**Real-world:** Rare, older systems

---

#### 3. CAUSAL CONSISTENCY

```
Definition:
  If write A causes write B (A happened before B),
  all observers see A before B.
  
  Non-causal writes can appear in any order.

Causality Detection:
  B depends on A if:
    - A writes value X
    - B reads X and writes based on it
    - B causally depends on A

Example:

Causal Scenario:
  Post: "Sunny day!" (A)
    |
  Comment: "Beautiful weather!" (B, depends on A)
    |
  Like comment (C, depends on B)

All must see: A → B → C (causal order preserved)

Non-Causal:
  Like1 on post (different post)
  Like2 on different post
  Order can be Like1, Like2 or Like2, Like1

Graph:
  Post A
  /    \
 |      Comment B
  \     /
   Like C
   
Causal chain: A → B → C (preserved)
Independent: Like1, Like2 (any order)
```

**Characteristics:**
- ✅ Preserves causality
- ✅ Allows concurrency for non-causal writes
- ✅ Intuitive semantics
- ❌ Complex to implement (track dependencies)

**Use cases:** Collaborative editing, social networks, messaging

---

#### 4. SEQUENTIAL CONSISTENCY (Lamport)

```
Definition:
  All processes see all writes in SAME total order.
  Order is consistent globally but may not match real time.

Key: Deterministic total order (but non-deterministic)

Example:

Real-time Events:
  T=0: Process A writes X=1
  T=1: Process B writes Y=1
  T=2: Process C reads X
  T=3: Process C reads Y

Sequential Consistency Possible Orders:
  A writes, then B writes: X,Y ✓
  B writes, then A writes: Y,X ✓
  
Both valid! But each observer sees same order.

All 3 processes see SAME order globally:
  Either: A before B (then read X,Y in that order)
  Or: B before A (then read Y,X in that order)

Graph:
  
Option 1: A → B (A happens before B globally)
  All processes see: write A, then write B
  
Option 2: B → A (B happens before A globally)
  All processes see: write B, then write A
```

**Characteristics:**
- ✅ Deterministic ordering
- ✅ Strong consistency
- ❌ Not real-time (order may differ from wall-clock time)
- ❌ Complex to implement

**Use cases:** Some NoSQL systems, memory models

---

#### 5. LINEARIZABLE CONSISTENCY (Strongest)

```
Definition:
  All processes see ALL writes in SAME total order
  AND order matches real-time causality.

Key differences from Sequential:
  Sequential: Deterministic but may ignore real-time
  Linearizable: Deterministic AND respects real-time

Example:

Real-time Events:
  T=0: Client1 writes X=1
  T=1: Returns to client
  T=2: Client2 reads X
       (Must see X=1, not old value)

Guarantee: "Atomic" - once any read sees new value,
           all subsequent reads must see it or newer

Atomic Operations:
  Compare-and-Set (CAS):
    if (current == expected) {
      set to new_value
      return true
    } else {
      return false
    }
  
This requires linearizability!

Graph:
  Write X=1
      |
      ├─→ Time passes (everyone waits)
      |
  Read X (must see 1, not old value)
      |
  Read X (must see 1, confirmed)
```

**Real-world Example:**
```
Banking Transfer: A → B

Linearizable:
  T=0: Check: A balance = $100
  T=1: Deduct $50 from A (A now $50)
  T=2: User checks A (MUST see $50, not $100)
  T=3: Add $50 to B
  T=4: User checks B (MUST see updated)

Not Linearizable (problem):
  T=0: Deduct $50 from A
  T=1: Add $50 to B
  T=2: User checks A (sees old $100!) ← WRONG
  
This violates linearizability.
```

**Characteristics:**
- ✅ Strongest guarantee
- ✅ Atomic operations work correctly
- ✅ Real-time ordering preserved
- ❌ Highest overhead
- ❌ Hardest to implement
- ❌ Reduces performance

**Use cases:** Critical systems (banking, healthcare)

---

### COMPARISON TABLE:

| Model | Order | Real-time | Strength | Complexity |
|-------|-------|-----------|----------|-----------|
| **Eventual** | No | No | Weakest | Low |
| **PRAM** | Per-process | No | Weak | Medium |
| **Causal** | Causally-related | No | Strong | High |
| **Sequential** | Total | No | Strong | High |
| **Linearizable** | Total | YES | Strongest | Very High |

### ORDERING HIERARCHY:

```
Linearizable (Strongest)
         ↑
    Sequential
         ↑
      Causal
         ↑
       PRAM
         ↑
    Eventual (Weakest)
```

### Real-World Consistency Levels in Databases:

```
PostgreSQL (default): Serializable (near-linearizable)
MySQL (default): Read Committed (weaker)
Cassandra (default): Eventual
MongoDB: Configurable (eventual to strong)
DynamoDB: Item-level consistency (not guaranteed global)
Memcached: Eventual
Redis: Strong (single-node-like)
```

---

# 7. REQUEST ROUTING {#request-routing}

## Question (QP16)
**Explain all 4 types of request routing with diagram.**

### Answer:

**Request Routing Definition:**
When a client wants to access data, it needs to know which node has that data. Request routing decides how to direct the request to the correct node.

---

## FOUR ROUTING APPROACHES:

### Approach 1: Any Node Can Route

```
              CLIENT
                |
                |
          (Send request to ANY node)
                |
        ┌───────┴───────┐
        |               |
    NODE 1          NODE 2
  (Router)         (Router)
    |                 |
    ├─ "Is this       ├─ "This key
    │  partition      │  belongs to
    │  on me?"        │  Node 3!"
    │ "No"            │  (knows partition map)
    │                 │
    └─────────────────│
                      │ (Forwards request)
                      |
                  NODE 3
              (Has the data)
                  |
              Returns data
                  |
            ← BACK TO CLIENT
```

**Process:**
1. Client picks ANY node (random or round-robin)
2. Node checks: "Do I have this partition?"
3. If yes: Process request
4. If no: Look up partition map, forward to correct node
5. Correct node processes, returns through requesting node

**Characteristics:**

| Aspect | Detail |
|--------|--------|
| **Client Logic** | Simple (any node) |
| **Node Logic** | Must know partition map |
| **Hop Count** | 2 hops if forwarding |
| **Load Balance** | Requests spread to all nodes |
| **Implementation** | Medium complexity |

**Diagram:**
```
CLIENT → ANY NODE → FORWARD TO CORRECT → RESPONSE
  |         ↓              ↓                 ↑
  └─────────┴──────────────┴─────────────────┘
          May take 2 hops
```

**Use cases:** Cassandra, Riak (leaderless systems)

---

### Approach 2: Routing Tier (Query Router)

```
                CLIENT1
                  |
            (Send request to
             Routing Tier)
                  |
        ┌─────────┼─────────┐
        |         |         |
    CLIENT2    CLIENT3   CLIENT4
        |         |         |
        └─────────┼─────────┘
                  |
        ┌─────────────────────┐
        │  ROUTING TIER       │
        │ (Load Balancer)     │
        │                     │
        │ Knows partition map │
        │ → Partition Aware   │
        │ → DOES NOT handle   │
        │   requests          │
        └─────────────────────┘
         /      |      |      \
        /       |      |       \
    NODE1   NODE2   NODE3   NODE4
   (Worker)(Worker)(Worker)(Worker)
    |        |        |        |
    └────────┼────────┼────────┘
             |
          DATA LAYER
```

**Process:**
1. All clients send requests to Routing Tier
2. Routing Tier knows partition map
3. Routing Tier looks up: "Partition X is on Node2"
4. Routing Tier forwards to Node2
5. Node2 processes, returns directly to client OR via router
6. Client gets response

**Characteristics:**

| Aspect | Detail |
|--------|--------|
| **Client Logic** | Knows router address (simple) |
| **Router Logic** | Complex (knows full partition map) |
| **Router Load** | All requests pass through |
| **Router Bottleneck** | Can become bottleneck |
| **Hop Count** | At least 1 hop (via router) |
| **Partition Aware** | Router knows where data is |
| **Request Handling** | Router DOES NOT execute queries |

**Key Point:** Routing Tier is NOT query processor, just director

**Diagram:**
```
    CLIENT
      |
      ↓ (knows where data is)
  ROUTING TIER
      | (directs)
      ↓
    NODE (executes)
      | (responds)
      ↑
    CLIENT
```

**Use cases:** PostgreSQL with pgBouncer, MySQL with ProxySQL

---

### Approach 3: Client-Aware (Smart Client)

```
                CLIENT
              (Smart!)
             /   |   \
            /    |    \
           /     |     \
      Knows   partition   Can connect
      about     map      directly to
    topology           correct node
       |                   |
       |       ┌───────────┤
       |       |           |
       |    Caches map     |
       |    Updates on     |
       |    cluster changes|
       |                   |
       └───────┬───────────┘
               |
        (DIRECT CONNECTION)
               |
        ┌──────┴──────┬──────┬──────┐
        |      |      |      |      |
      NODE1 NODE2  NODE3  NODE4  NODE5
       (Worker)


CLIENT:
  partition_map = {
    partition 1: Node1,
    partition 2: Node2,
    partition 3: Node3,
    ...
  }
  
  On request:
    partition = hash(key) % num_partitions
    node = partition_map[partition]
    connect directly to node
    execute query
    get response
```

**Process:**
1. Client downloads partition map at startup
2. Client caches the map locally
3. For each request:
   - Client computes: partition = hash(key)
   - Client looks up: node = partition_map[partition]
   - Client connects directly to that node
   - No intermediate routing
4. On cluster change: Client updates map (via gossip or polling)

**Characteristics:**

| Aspect | Detail |
|--------|--------|
| **Client Logic** | Complex (knows partition map) |
| **Client Overhead** | Caches partition map |
| **Connection** | Direct to target node |
| **Hops** | Single hop (direct) |
| **Latency** | Lowest (direct path) |
| **Bottleneck** | None (no central routing) |
| **Update Mechanism** | Cluster notifies clients of changes |

**Partition Map Update:**
```
Cluster change (node fails, node joins):
  → Cluster broadcasts: "Partition map updated"
  → All clients download new map
  → Future requests use new map
  
Risk: Stale map (client sees outdated info)
  → Client redirected: "Try Node5"
  → Client updates map
```

**Diagram:**
```
        CLIENT (knows where)
           |
           | (direct connection)
           ↓
        NODE (has data)
           | (response)
           ↑
        CLIENT
```

**Use cases:** DynamoDB SDK, Cassandra drivers, Couchbase

---

### Approach 4: ZooKeeper (External Coordination Service)

```
                CLIENT
                  |
            (Which node has data?)
                  |
        ┌─────────────────────┐
        │   ZOOKEEPER         │
        │ (Configuration      │
        │  Service)           │
        │                     │
        │ Authoritative source│
        │ for partition map   │
        │                     │
        │ Notifies clients &  │
        │ routers of changes  │
        └─────────────────────┘
         /              |       \
        /               |        \
    CLIENT            ROUTING   OTHER
   (Updates)          TIER   SERVICES
    |                 (Updates)
    |                    |
    └────────┬───────────┘
             |
        ┌─────┴──────┬────────┬────────┐
        |      |     |        |        |
      NODE1 NODE2  NODE3   NODE4   NODE5
      (Worker)


ZOOKEEPER RESPONSIBILITIES:
  1. Stores partition → node mapping
  2. Detects node failures
  3. Notifies clients/routers of changes
  4. Provides authoritative view
  5. Handles consensus (if 2 clients disagree)
```

**Process:**
1. Client queries: "Where is partition X?"
2. ZooKeeper responds: "Partition X is on Node2"
3. Client connects to Node2
4. Node2 fails
5. ZooKeeper detects failure, updates map
6. ZooKeeper notifies all clients/routers
7. Clients/routers use updated map

**Characteristics:**

| Aspect | Detail |
|--------|--------|
| **Authority** | ZooKeeper is source of truth |
| **Single Point** | Yes (ZooKeeper itself) |
| **Consistency** | Strong (centralized) |
| **Reliability** | ZooKeeper itself replicated |
| **Latency** | Lookup latency to ZooKeeper |
| **Failover** | If ZooKeeper fails, system blocked |
| **Update Mechanism** | Pub-Sub notification |

**ZooKeeper Internal:**
```
ZooKeeper (typically 3-5 replicas):
  ├─ Leader
  └─ Followers

Client writes: Sent to leader
Leader: Broadcasts to followers
Once majority confirms: Change is durable
Clients notified via watch
```

**Who Uses ZooKeeper:**
- Apache Kafka (old, now KRaft)
- Apache HBase
- Apache SolrCloud

**Diagram:**
```
    CLIENT
      |
      ↓ (query authority)
  ZOOKEEPER
      | (tells where data is)
      ↓
    NODE (executes)
      | (response)
      ↑
    CLIENT
```

---

## COMPARISON OF ALL FOUR APPROACHES:

| Aspect | Approach 1 | Approach 2 | Approach 3 | Approach 4 |
|--------|-----------|-----------|-----------|-----------|
| **Name** | Any Node | Routing Tier | Client-Aware | ZooKeeper |
| **Client Logic** | Simple | Simple | Complex | Medium |
| **Hops** | 2 (if forward) | 2+ | 1 | 2 |
| **Latency** | Medium | High | **Lowest** | Medium |
| **Bottleneck** | Requested node | Routing tier | **None** | ZooKeeper |
| **Scalability** | Good | Limited | **Best** | Good |
| **Failure Handling** | Requests retry | Router fails → blocked | Client retries | ZK fails → blocked |
| **Partition Awareness** | Yes | Yes | Yes | Yes |
| **Complexity** | Medium | High | High | High |
| **Load Balance** | Equal | Equal | Unequal* | Equal |

*Unequal: Depends on key distribution

---

## VISUAL COMPARISON:

```
APPROACH 1: Any Node
CLIENT → Node1 → (check map) → Node2 ✓
         └─ 2 hops

APPROACH 2: Routing Tier
CLIENT → Router → Node2 ✓
         └─ Routing tier bottleneck

APPROACH 3: Client-Aware
CLIENT → (knows) → Node2 directly ✓
         └─ Most efficient

APPROACH 4: ZooKeeper
CLIENT → ZooKeeper → (consults) → Node2 ✓
         └─ Authoritative source
```

---

# 8. AMAZON S3 (OBJECT STORAGE) {#s3}

## Question (QP5)
**Illustrate the working of Amazon Simple Storage Service with respect to object storage.**

### Answer:

### Amazon S3 Overview:

**S3 = Simple Storage Service**
- Highly scalable object storage service
- Designed for massive-scale data storage
- Used for backups, archives, data lakes, media storage
- Accessible via REST API, AWS Console, CLI, SDKs

### S3 Core Concepts:

#### 1. **Bucket**
- Top-level container (like a folder)
- Unique globally across all AWS (unique name)
- Regional (stored in specific AWS region)
- Maximum 100 buckets per account (can request increase)
- Permissions controlled at bucket level

```
Bucket name: company-backups
  └─ No collisions across all AWS customers
  └─ Immutable once created
  └─ Cannot rename
  └─ Regional (e.g., us-east-1, eu-west-1)
```

#### 2. **Object**
- Individual file/data stored in bucket
- Maximum 5TB per object
- Identified by key (path-like name)
- Immutable (cannot modify, must replace)
- Versioning allows keeping history

```
Object components:
  Key: documents/report-2024.pdf
  Size: 2.5 MB
  Metadata: Content-Type, Date, Custom tags
  Data: Binary content
  Version ID: (if versioning enabled)
```

#### 3. **Key**
- Unique identifier for object within bucket
- Flat namespace (no directory hierarchy)
- Can use path-like structure: prefix/subprefix/filename
- No collisions (each key unique within bucket)

```
Bucket: my-photos
  Keys:
    - vacation/beach.jpg
    - vacation/mountain.jpg
    - family/christmas.jpg
    - 2024/january/photo1.jpg

No actual directories, but path structure understood by applications
```

### S3 Architecture:

```
┌──────────────────────────────────────────────────┐
│           AWS S3 (Global Service)                │
├──────────────────────────────────────────────────┤
│                                                  │
│  REGION 1 (us-east-1)        REGION 2 (eu-west)│
│  ┌──────────────┐            ┌──────────────┐   │
│  │  Bucket1     │            │  Bucket2     │   │
│  │              │            │              │   │
│  │ Objects:     │            │ Objects:     │   │
│  │ ├─ file1.pdf │            │ ├─ data.csv  │   │
│  │ ├─ file2.jpg │            │ └─ logs.txt  │   │
│  │ └─ file3.bin │            └──────────────┘   │
│  └──────────────┘                               │
│       |                                          │
│       └─ Replicated across                       │
│          Availability Zones                      │
│          (AZ1, AZ2, AZ3)                        │
│                                                  │
└──────────────────────────────────────────────────┘
```

### S3 Operations:

#### 1. **PUT (Upload/Create Object)**
```
HTTP request:
  PUT /bucket-name/key HTTP/1.1
  Host: s3.amazonaws.com
  
  [Binary data in body]

Response:
  200 OK
  ETag: "abc123def456"
  x-amz-version-id: "v1"

SDK Example (Python):
  s3_client.put_object(
    Bucket='my-bucket',
    Key='documents/report.pdf',
    Body=open('report.pdf', 'rb'),
    Metadata={'author': 'John'},
    ContentType='application/pdf'
  )
```

#### 2. **GET (Download/Retrieve Object)**
```
HTTP request:
  GET /bucket-name/key HTTP/1.1
  Host: s3.amazonaws.com
  Range: bytes=0-999 (partial)

Response:
  200 OK
  Content-Length: 1000
  ETag: "abc123def456"
  Last-Modified: 2024-01-15
  
  [Binary data in body]

SDK Example:
  response = s3_client.get_object(
    Bucket='my-bucket',
    Key='documents/report.pdf'
  )
  file_data = response['Body'].read()
```

#### 3. **DELETE (Remove Object)**
```
HTTP request:
  DELETE /bucket-name/key HTTP/1.1
  Host: s3.amazonaws.com

Response:
  204 No Content

SDK Example:
  s3_client.delete_object(
    Bucket='my-bucket',
    Key='documents/report.pdf'
  )
```

#### 4. **HEAD (Get Metadata)**
```
HTTP request:
  HEAD /bucket-name/key HTTP/1.1
  Host: s3.amazonaws.com

Response:
  200 OK
  Content-Length: 1000
  Content-Type: application/pdf
  Last-Modified: 2024-01-15
  
  (No body, just headers)
```

### S3 URLs:

```
VIRTUAL-HOSTED STYLE (Preferred):
  https://bucket-name.s3.amazonaws.com/key
  Example: https://my-photos.s3.amazonaws.com/vacation/beach.jpg

PATH STYLE (Legacy):
  https://s3.amazonaws.com/bucket-name/key
  Example: https://s3.amazonaws.com/my-photos/vacation/beach.jpg

REGIONAL ENDPOINT:
  https://bucket-name.s3.region.amazonaws.com/key
  Example: https://my-photos.s3.eu-west-1.amazonaws.com/vacation/beach.jpg
```

### S3 Data Protection Mechanisms:

#### 1. **Replication (Standard)**
```
Default for S3 Standard storage class:
  
DATA STORED:
  AZ1: Replica 1
  AZ2: Replica 2
  AZ3: Replica 3
  
DURABILITY: 11 nines (99.999999999%)
SURVIVABILITY: Can lose 2 AZs and still recover

Process:
  1. Client uploads object
  2. S3 stores in primary AZ
  3. Automatically replicates to 2 other AZs
  4. Returned to client (3 copies now exist)
```

#### 2. **RRS (Reduced Redundancy Storage)**
```
Fewer replicas, lower cost:
  
STORAGE:
  AZ1: Replica 1
  AZ2: Replica 2
  
DURABILITY: 4 nines (99.99%)
SURVIVABILITY: Can lose 1 AZ

USE CASES:
  - Thumbnail images (regenerable from source)
  - Temporary caches
  - Non-critical logs
  
TRADE-OFF:
  Lower cost, lower durability
```

#### 3. **Regions & Cross-Region Replication**
```
DEFAULT: Objects stored in selected region only

OPTION: Enable Cross-Region Replication (CRR)

Setup:
  Source bucket (us-east-1)
    ↓ (Automatically replicated)
  Destination bucket (eu-west-1)
  
Behavior:
  - New objects automatically replicated
  - Existing objects not replicated
  - Replication happens asynchronously
  
USE CASES:
  - Geographic redundancy (survive region failure)
  - Low-latency access (serve from nearby region)
  - Compliance (copy data to specific region)
  - Disaster recovery
```

#### 4. **Versioning**
```
DEFAULT: Off (single version per key)

ENABLE: Put bucket to versioning state

Behavior:
  Upload file.txt (v1)
  Upload file.txt (v2)
  Upload file.txt (v3)
  
Result:
  - All 3 versions stored
  - Latest accessible by default
  - Older versions accessible by version ID
  
DELETE (with versioning):
  - Object marked as deleted
  - Older versions still accessible
  - Can restore by deleting "delete marker"

STORAGE:
  Charged for ALL versions (including deleted)
  Careful: Storage cost can grow
  
USE CASES:
  - Data protection (accidental deletion)
  - Compliance (audit trail)
  - Recovery (restore old version)
```

#### 5. **Multi-Part Upload (Parallel)**
```
PROBLEM:
  5GB file over slow connection
  Single upload fails partway
  Must restart entire upload (wasteful)

SOLUTION: Multi-Part Upload

Process:
  1. Initiate upload
  2. Split file into parts (e.g., 100MB each)
  3. Upload parts in parallel
  4. If part fails, retry only that part
  5. Complete upload (assemble parts)

Benefits:
  - Retry only failed parts (not entire file)
  - Parallel upload (speed up)
  - Resume capability
  - Efficient for large files

Example:
  5GB file → 50 parts (100MB each)
  Upload parts 1-40 successfully
  Part 41 fails
  → Retry only part 41
  → No need to re-upload parts 1-40
  → Save 4GB of re-transmission
```

#### 6. **Audit Logs (MustStore in Different Bucket!)**
```
CRITICAL FOR COMPLIANCE:
  Track all access to S3 bucket

Server Access Logging:
  - All HTTP requests logged
  - Who accessed what, when
  - Must store in DIFFERENT bucket!

Reason:
  - Audit logs must be tamper-proof
  - If bucket compromised, logs still safe
  - Compliance requirement
  
Setup:
  Bucket "production-data"
    └─ Server Access Logging enabled
       └─ Logs sent to bucket "audit-logs"
  
Log contents:
  - Requester ID
  - Operation (GET, PUT, DELETE)
  - Key accessed
  - Timestamp
  - Response status
```

### S3 Reliability & Availability:

```
SLA (Service Level Agreement):
  - 99.99% availability for S3 Standard
  - Durability: 11 nines per object per year
  
MEANING:
  - 99.99% uptime = ~45 minutes downtime/year
  - 11 nines durability = 1 chance in 100 billion of data loss
  
DESIGN:
  - Distributed across multiple AZs
  - Automatic replication
  - Redundant hardware
  - Self-healing (failed drives auto-replaced)
```

### S3 Use Cases:

```
1. BACKUPS & ARCHIVES
   - Corporate databases daily backup
   - Long-term storage (years)
   - Cost-effective (Glacier for old data)

2. BIG DATA & ANALYTICS
   - Store raw data (TB/PB scale)
   - Process with Hadoop, Spark
   - Output results back to S3

3. CONTENT DELIVERY
   - Store large files (videos, images)
   - CloudFront CDN distributes
   - Low-latency access globally

4. DATA LAKES
   - Centralize all data
   - Multiple teams analyze same data
   - Audit trail of access

5. APPLICATION STORAGE
   - Mobile apps store user files
   - Cloud applications store data
   - Stateless app design (data in S3)

6. LOG STORAGE
   - Application logs
   - Server logs
   - Compliance logging
```

### S3 Access Control:

```
AUTHENTICATION:
  - AWS Access Keys (programmatic)
  - AWS IAM roles (services)
  - Temporary credentials (STS)

AUTHORIZATION:
  - Bucket policies (JSON)
  - ACLs (older, deprecated)
  - IAM policies
  - Presigned URLs (temporary access)

ENCRYPTION:
  - Server-side (S3 encrypts at rest)
  - Client-side (encrypt before upload)
  - TLS for transport (HTTPS)

VERSIONING & PROTECTION:
  - Object Lock (WORM - Write Once, Read Many)
  - MFA Delete (require MFA to delete)
```

---

# 9. OPENSTACK STORAGE {#openstack}

## Question (QP13)
**What are the 3 main types of storage used in OpenStack? Highlight key differences. Choose storage for databases, unstructured data like music/video, large datasets, and RAID volumes.**

### Answer:

### OpenStack Storage Overview:

OpenStack provides three main storage services, each serving different purposes:

```
┌───────────────────────────────────────────────┐
│        OPENSTACK STORAGE TYPES                │
├───────────────────────────────────────────────┤
│                                               │
│ 1. CINDER: Block Storage                     │
│    (Virtual disks for instances)             │
│                                               │
│ 2. SWIFT: Object Storage                     │
│    (S3-like massive-scale storage)           │
│                                               │
│ 3. MANILA: Shared File Storage               │
│    (NFS/SMB file shares)                     │
│                                               │
└───────────────────────────────────────────────┘
```

---

## TYPE 1: CINDER (Block Storage)

### What is Cinder:
- Provides persistent block storage for instances
- Volumes attached like virtual disks
- Can be formatted with filesystems (ext4, NTFS)
- Snapshots for backup
- Encryption supported

### Characteristics:

```
BLOCK-LEVEL ACCESS:
  Partitions by address, not name
  Raw disk interface
  Can format with any filesystem

ATTACHMENT:
  Attached to instances
  Appears as /dev/vdb (or similar)
  Can be formatted and mounted
  
DURABILITY:
  RAID protection
  Snapshots for backup
  Can survive disk failures
```

### Architecture:

```
    ┌──────────────────┐
    │   NOVA INSTANCE  │
    │  (Running VM)    │
    └────────┬─────────┘
             │
        mounts
             │
             ↓
    ┌─────────────────────┐
    │  CINDER VOLUME      │
    │  (Virtual Disk)     │
    │                     │
    │  /dev/vdb           │
    │  (appears as device)│
    └────────┬────────────┘
             │
      backed by
             │
             ↓
    ┌──────────────────────────┐
    │  STORAGE BACKEND         │
    │  (LVM / iSCSI / Ceph)    │
    │  (SAN / Array)           │
    └──────────────────────────┘
             |
             └─ Physical Disks
```

### Use Cases:

```
✓ DATABASES
  - MySQL, PostgreSQL instances
  - Need persistent, fast storage
  - Survive VM crash
  
✓ APPLICATIONS
  - Logging
  - Temporary working space
  - State persistence
  
✓ RAID VOLUMES
  - Redundancy required
  - Can use RAID backend
  - High availability
```

### Cinder Features:

| Feature | Detail |
|---------|--------|
| **Capacity** | 1GB to 1TB+ per volume |
| **Speed** | Medium (SAN backend) |
| **Latency** | Milliseconds |
| **Replicas** | Not native (use RAID backend) |
| **Snapshots** | Yes (point-in-time) |
| **Encryption** | Yes (AES-256) |
| **Multi-attach** | Limited (usually single VM) |

---

## TYPE 2: SWIFT (Object Storage)

### What is Swift:
- OpenStack object storage (similar to Amazon S3)
- Massive-scale distributed storage
- Flat namespace, REST APIs
- Designed for billions of objects
- Automatic replication

### Characteristics:

```
OBJECT-LEVEL ACCESS:
  REST APIs (GET, PUT, DELETE)
  Flat namespace (no directories)
  HTTP/HTTPS URLs for objects
  
DISTRIBUTED:
  Across multiple nodes
  Automatic partitioning
  Self-healing
  
SCALABILITY:
  Designed for 100s of TB to PB scale
  Linear scaling with nodes
  No central bottleneck
```

### Architecture:

```
┌──────────────────┐
│  CLIENT          │
│ (Applications)   │
└────────┬─────────┘
         │
    HTTP/REST
         │
         ↓
┌────────────────────────────┐
│   SWIFT PROXY (Gateway)    │
│  - Authentication          │
│  - Load balancing          │
│  - Request routing         │
└────────┬───────────────────┘
         │
    ┌────┼────┐
    |    |    |
    ↓    ↓    ↓
┌────────────────┐
│ STORAGE NODES  │
│ (Rings)        │
│ - Account      │
│ - Container    │
│ - Object       │
└────────────────┘
    |    |    |
    └────┼────┘
         |
    ┌────┴────┐
    ↓         ↓
  Disk1    Disk2
```

### Swift Concepts:

```
Account:      User/tenant (authentication)
Container:    Like bucket (groups objects)
Object:       Individual file/data

URL Structure:
  http://swift.example.com/v1/account/container/object
  
Example:
  http://swift.example.com/v1/user123/photos/vacation.jpg
    └─ v1: API version
    └─ user123: Account (tenant)
    └─ photos: Container
    └─ vacation.jpg: Object (key)
```

### Use Cases:

```
✓ UNSTRUCTURED DATA (Music, Video, Images)
  - Photos, videos stored as objects
  - Easy HTTP retrieval
  - Massive scale (billions of files)
  - CDN integration for distribution
  
✓ MEDIA STORAGE & DELIVERY
  - Video streaming platform
  - Photo sharing service
  - Audio files
  
✓ ARCHIVES & BACKUPS
  - Long-term retention
  - Compliance archiving
  - Cost-effective at scale
  
✓ DATA LAKE
  - Centralized data store
  - Multiple teams access same data
  - Cost-effective for unstructured data
```

### Swift Features:

| Feature | Detail |
|---------|--------|
| **Capacity** | PB+ scale |
| **Throughput** | High (parallel) |
| **Latency** | Higher than block (HTTP) |
| **Replicas** | Configurable (default 3) |
| **Snapshots** | Via versioning |
| **Encryption** | SSL/TLS transport |
| **Consistency** | Eventual |

---

## TYPE 3: MANILA (Shared File Storage)

### What is Manila:
- OpenStack shared file storage service
- Provides NFS/SMB shares (file-level access)
- Similar to NAS (Network Attached Storage)
- Multiple instances mount same share
- Traditional file access model

### Characteristics:

```
FILE-LEVEL ACCESS:
  NFS (Network File System) - Unix/Linux
  SMB (Server Message Block) - Windows
  Shared by multiple instances
  
MOUNT:
  Instances mount share
  Appears as network filesystem
  Access via standard file operations
```

### Architecture:

```
    ┌─────────────────────────┐
    │    INSTANCE A           │
    │ (Linux/Windows)         │
    └──────────┬──────────────┘
               │
              mount
      /mnt/shared/data
               │
    ┌──────────┴──────────┐
    │                     │
    ↓                     ↓
┌──────────────┐    ┌──────────────┐
│  INSTANCE B  │    │  INSTANCE C  │
│              │    │              │
└──────────────┘    └──────────────┘
    (both mount same /mnt/shared/data)
        │         │         │
        └─────────┼─────────┘
                  │
          (NFS/SMB network)
                  │
                  ↓
        ┌──────────────────┐
        │ MANILA SHARE     │
        │ (File Storage)   │
        └──────────────────┘
                  │
         backed by
                  │
        ┌──────────────────┐
        │  BACKEND         │
        │  (NFS Server /   │
        │   Ceph FS /      │
        │   HDFS)          │
        └──────────────────┘
```

### Use Cases:

```
✓ SHARED DATABASES
  - MySQL cluster (shared logs)
  - PostgreSQL WAL directory
  - Shared configuration
  
✓ MULTI-INSTANCE APPLICATIONS
  - Web server farms (share content)
  - Application servers (shared config)
  - Log consolidation
  
✓ COLLABORATIVE STORAGE
  - Team file sharing
  - Document repositories
  - Development file shares
```

### Manila Features:

| Feature | Detail |
|---------|--------|
| **Access** | File-level (NFS, SMB) |
| **Sharing** | Multiple instances |
| **Capacity** | GB to TB per share |
| **Latency** | Medium |
| **Replicas** | Depends on backend |
| **Snapshots** | Yes (FS snapshots) |
| **Encryption** | Transport (TLS) |

---

## COMPARISON TABLE:

| Aspect | Cinder | Swift | Manila |
|--------|--------|-------|--------|
| **Access Type** | Block | Object | File |
| **Interface** | Device (/dev/vdb) | REST HTTP | NFS/SMB |
| **Namespace** | N/A | Flat (URI) | Hierarchical (paths) |
| **Scale** | Medium (TB) | Massive (PB) | Medium (TB) |
| **Speed** | Fast (block) | Medium (HTTP) | Medium (NFS) |
| **Sharing** | Single VM | Global | Multiple VMs |
| **Use Case** | Databases, OS disk | Archives, media | File sharing |
| **Latency** | Milliseconds | 10-100ms | Milliseconds |
| **Typical Backend** | SAN, LVM | Distributed nodes | NFS, Ceph |

---

## CHOOSING STORAGE BY REQUIREMENT:

### 1. **Databases (MySQL, PostgreSQL, Oracle)**

**CHOICE: CINDER (Block Storage)**

**Reasons:**
- Persistent, reliable storage
- Low-latency block access required
- ACID transactions need reliable I/O
- Can use RAID backend for redundancy
- Point-in-time snapshots for backups

**Configuration:**
```
- Cinder volume size: 100GB-1TB
- Backend: SAN with RAID 10 or Ceph
- Snapshots: Daily/hourly
- Replication: RAID redundancy
- Location: Same AZ for low latency
```

---

### 2. **Unstructured Data (Music, Video, Images)**

**CHOICE: SWIFT (Object Storage)**

**Reasons:**
- Massive-scale storage (PB+)
- Easy HTTP URLs for downloads
- Automatic replication (3+ copies)
- Cost-effective at scale
- Natural CDN integration

**Configuration:**
```
- Container per project/user
- Swift default 3 replicas
- Access via REST API
- CDN enabled for distribution
- Versioning for retention
```

---

### 3. **Large Datasets (Data Lakes, Analytics)**

**CHOICE: SWIFT (Object Storage)**

**Reasons:**
- Designed for massive data
- Hadoop/Spark can read directly
- Distributed computation
- Retention policies available
- Cost-efficient storage

**Configuration:**
```
- Datasets in containers
- Parallel reads (multiple instances)
- Spark/Hadoop jobs read directly
- Replication: 3 copies minimum
- Tiering: Move old to cheaper storage
```

**Alternative: MANILA** if multiple analytics servers share access

---

### 4. **RAID Volumes (High Availability)**

**CHOICE: CINDER (Block Storage)**

**Reasons:**
- RAID backend provides redundancy
- Can survive disk failures
- Critical data protection
- Consistent performance

**Configuration:**
```
- Cinder backend: RAID 10
- Volume size: Depends on data
- Snapshots: Frequent backups
- Cross-AZ replication: For DR
- Multi-attach (limited): For clustering
```

---

## COMPARISON BY SCENARIO:

```
SCENARIO 1: E-commerce Platform
  Product Database: CINDER (reliability)
  User uploads (images): SWIFT (scale)
  Product catalog files: MANILA (shared)

SCENARIO 2: Video Streaming Platform
  Video files: SWIFT (massive scale, HTTP delivery)
  Metadata DB: CINDER (consistent)
  Logs storage: SWIFT (archival)

SCENARIO 3: Data Analytics
  Input datasets: SWIFT (huge scale)
  Working storage: CINDER (fast)
  Results output: SWIFT (archival)

SCENARIO 4: Financial Services
  Core database: CINDER (RAID, reliable)
  Transaction logs: CINDER (reliable)
  Archive data: SWIFT (long-term)
  Audit logs: MANILA (compliance)
```

---

# 10. BLOCK STORAGE & iSCSI {#block-storage}

## Questions (QP13)
**What is genuinely unique and disruptive about Cinder in the cloud space? If you are responsible for capacity planning for volume block storage in OpenStack cloud, what will be your approach? Your network contains three servers. You need Server1 to provide iSCSI storage for Server2 and Server3. What should you do on Server1?**

### Answer:

---

## WHAT'S UNIQUE ABOUT CINDER:

### Cinder's Disruptive Innovation:

```
TRADITIONAL APPROACH:
  Instance = Storage tied together
    Each VM gets local disk
    If VM fails → data in local disk lost
    Hard to migrate between servers
    Not recoverable

CLOUD APPROACH (CINDER):
  Instance ≠ Storage (SEPARATED)
    VM compute ≠ persistent storage
    Storage is independent service
    Survives VM failure/crash
    Can migrate VM to different host
    Storage stays available
```

### Key Unique Features:

#### 1. **Storage Independence**
```
Before Cloud:
  Server → Storage attached tightly
  
With Cinder:
  Compute (Nova) ← separate ← Storage (Cinder)
  
Result:
  ✓ Compute fails, storage survives
  ✓ Upgrade compute, keep storage
  ✓ Move VM between hosts, storage accessible
  ✓ Snapshot storage while VM running
  ✓ Attach storage to different VM
```

#### 2. **Rapid Provisioning**
```
Traditional SAN:
  1. Request storage
  2. Purchase hardware
  3. Install/configure (weeks)
  4. Provision (days)

Cinder:
  1. API call to create volume
  2. Instant allocation (seconds)
  3. Attach to instance (seconds)
  4. Use immediately
```

#### 3. **Multi-Backend Support**
```
Cinder backends (plug-in):
  - LVM (Linux)
  - Ceph (distributed)
  - SolidFire (SSD)
  - Pure Storage (enterprise array)
  - EMC, NetApp, Dell, HP arrays
  
Organizations can:
  - Use existing arrays
  - Mix backends
  - Migrate between backends
  - Choose best backend per workload
```

#### 4. **API-Driven Operations**
```
All operations programmable:
  - Create volume
  - Delete volume
  - Attach to instance
  - Create snapshot
  - Revert to snapshot
  - Resize volume
  - Encrypt volume

No manual SAN management needed!
Automation-friendly.
```

#### 5. **Multi-Tenancy**
```
Traditional:
  Enterprise manages storage
  One admin, one viewpoint

Cinder:
  Multiple tenants (projects)
  Each tenant has own volumes
  Quota enforcement
  Isolation and security
  Tenant pays for usage
```

#### 6. **Snapshots & Backups**
```
Feature:
  Create point-in-time snapshot
  While instance running
  Not disruptive
  Can restore/clone from snapshot

Use Cases:
  - Backup before upgrade
  - Clone VM
  - Test scenarios
  - Disaster recovery
```

### Why This is Disruptive:

```
IMPACT ON CLOUD OPERATIONS:

1. ELASTICITY
   Easy scale: Create volume → Attach → Use
   
2. AUTOMATION
   Infrastructure as code
   Programmable storage provisioning
   
3. COST EFFICIENCY
   Pay for actual usage
   No waste on pre-allocated storage
   
4. RELIABILITY
   Snapshots enable fast recovery
   Separate compute/storage
   
5. FLEXIBILITY
   Multi-backend support
   Plug-in architecture
   Choose best-fit storage per workload
   
6. SIMPLIFIED MANAGEMENT
   Cloud operators don't manage SAN directly
   Cinder abstracts details
   API handles complexity
```

---

## CAPACITY PLANNING FOR CINDER BLOCK STORAGE:

### Approach:

#### STEP 1: Understand Workload Characteristics

```
GATHER REQUIREMENTS:

1. Expected Volume Count
   - How many VMs? (e.g., 1000)
   - Volume per VM? (e.g., 2 volumes average)
   - Total volumes: 2000

2. Volume Size Distribution
   - Small (10GB): 30%
   - Medium (100GB): 50%
   - Large (1TB): 20%
   
3. Performance Requirements
   - IOPS needed
   - Throughput (MB/s)
   - Latency tolerance

4. Availability Requirements
   - Redundancy level (RAID, replicas)
   - RTO/RPO (Recovery Time/Point Objective)
   - Backup frequency

5. Growth Projection
   - Current: 10TB
   - Year 1: 50TB
   - Year 3: 200TB
```

#### STEP 2: Calculate Physical Storage

```
FORMULA:

Total Physical = (Volume Capacity Needed) × (Redundancy Factor) 
                 × (Overhead Factor)
                 
Where:

Redundancy Factor:
  - RAID 0 (no redundancy): 1.0x
  - RAID 1 (mirroring): 2.0x
  - RAID 5 (parity): 1.33x
  - RAID 6 (dual parity): 1.50x
  - RAID 10: 2.0x
  
Overhead Factor (filesystem, snapshots, metadata):
  - ~1.15x (15% overhead)

Example Calculation:
  
Logical capacity needed: 500TB
Redundancy (RAID 5): 1.33x
Overhead: 1.15x

Physical disk = 500 × 1.33 × 1.15 = 766TB
```

#### STEP 3: Select Backend Storage

```
MATCH WORKLOAD TO BACKEND:

For Performance-Critical (Database):
  ✓ SSD-based array (Pure Storage, SolidFire)
  ✓ RAID 10 configuration
  ✓ Local backend (same AZ)
  
For Cost-Sensitive (Archives):
  ✓ HDD-based RAID 6
  ✓ Ceph distributed storage
  ✓ Tiering to cheaper storage

For Mixed Workload:
  ✓ Multi-backend Cinder setup
  ✓ Different volume types
  ✓ Workload-specific backend selection
```

#### STEP 4: Plan for Growth & Redundancy

```
CAPACITY PLANNING FORMULA:

Useful Capacity = Physical Disk Capacity × 0.85

(Keep 15% free for maintenance, rebuilds)

Example:
  Physical disks: 1000TB
  Useful capacity: 1000 × 0.85 = 850TB
  
  Available for volumes: 850TB
  Snapshots & growth: 100TB
  Reserved: 100TB
  
  Allocatable to users: 650TB
```

#### STEP 5: Monitor & Adjust

```
MONITORING:

KPIs to track:
  - Used vs Available capacity
  - IOPS per volume type
  - Latency percentiles (p50, p99)
  - Snapshot growth
  - Growth rate (TB/month)

Alerting Thresholds:
  - Capacity >80%: Plan upgrade
  - Capacity >90%: Urgent upgrade needed
  - Latency p99 >50ms: Performance issue
  
Auto-scaling:
  - Growth trend analysis
  - Project when will hit 80% mark
  - Pre-provision next array before needed
```

#### STEP 6: Design HA Architecture

```
REDUNDANCY ACROSS AZ:

Zone1:
  SAN Array 1 (500TB)
  Cinder backend 1
  Controllers

Zone2:
  SAN Array 2 (500TB)
  Cinder backend 2
  Controllers

Zone3:
  Replication/Mirror
  Backup destination

Result:
  ✓ Survive AZ failure
  ✓ Balanced load
  ✓ Geographic redundancy
  ✓ Disaster recovery
```

### Capacity Planning Example:

```
SCENARIO: Startup with 1000 VMs expected in year 1

REQUIREMENTS:
  Average volume: 50GB
  Total logical: 50TB
  RAID 5: 1.33x
  Overhead: 1.15x
  
CALCULATION:
  Physical needed: 50 × 1.33 × 1.15 = 76.5TB
  Useful capacity (85%): 76.5 × 0.85 = 65TB
  
ALLOCATION:
  Active volumes: 50TB
  Snapshots/growth: 10TB
  Reserved: 5TB

BACKEND:
  2× SAN arrays, 40TB each
  RAID 5 configuration
  Cross-AZ replication

MONITORING:
  Daily: Check 50-60TB usage mark
  Growth rate: ~2TB/month
  At month 12: Plan purchase of next array
```

---

## SETTING UP iSCSI STORAGE ON SERVER1:

### Scenario:
Server1 provides iSCSI storage for Server2 and Server3

### What is iSCSI:
```
iSCSI (Internet SCSI):
  - SCSI commands over network (Ethernet)
  - Appears as local disk to remote servers
  - Server1 = iSCSI target (storage provider)
  - Server2, Server3 = iSCSI initiators (clients)
  
Network Block Device:
  Block storage accessed over LAN
  ```

### Architecture:

```
         Network (Ethernet)
         /      |      \
        /       |       \
       |        |        |
    Server2   Server1   Server3
  (Initiator) (Target)  (Initiator)
     │          │          │
     │          │          │
  iSCSI      Storage     iSCSI
  Client     Provider    Client
     └────────┼─────────┘
              │
         (iSCSI protocol)
```

### Steps to Setup on Server1:

#### Step 1: Install iSCSI Target Software

```bash
# Ubuntu/Debian
sudo apt-get install tgt

# RHEL/CentOS
sudo yum install scsi-target-utils
```

#### Step 2: Create Storage LUN (Logical Unit Number)

```bash
# Create a LUN backed by a file or partition

# Option A: Using a file (test environment)
sudo dd if=/dev/zero of=/var/lib/iscsi_disks/disk1.img bs=1M count=10240
# Creates 10GB virtual disk

# Option B: Using a partition (production)
# /dev/sdb1 (or physical partition)
```

#### Step 3: Configure iSCSI Target

```bash
# Edit target configuration
sudo vi /etc/tgt/targets.conf

# Add configuration:
<target iqn.2024-01.example.com:storage.disk1>
    <backing-store /var/lib/iscsi_disks/disk1.img>
        # LUN 1 backed by disk1.img
    </backing-store>
    
    # Allow Server2 and Server3 to connect
    <initiator-address 192.168.1.10>  # Server2
    </initiator-address>
    
    <initiator-address 192.168.1.11>  # Server3
    </initiator-address>
</target>

# IQN Format:
# iqn.YYYY-MM.domain.com:storage.name
# iqn.2024-01.example.com:storage.disk1
```

#### Step 4: Start iSCSI Target Service

```bash
# Start service
sudo systemctl start tgt
sudo systemctl enable tgt

# Verify
sudo systemctl status tgt

# Show targets
sudo tgtadm --mode target --op show
```

#### Step 5: Configure Firewall

```bash
# Allow iSCSI port (3260)
sudo ufw allow 3260

# Or with iptables:
sudo iptables -A INPUT -p tcp --dport 3260 -j ACCEPT
sudo iptables -A INPUT -p udp --dport 3260 -j ACCEPT
```

#### Step 6: Network Configuration (Server1)

```bash
# Ensure Server1 has static IP
sudo vi /etc/network/interfaces

auto eth0
iface eth0 inet static
    address 192.168.1.9
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 8.8.4.4
```

### Steps to Connect from Server2/Server3:

#### On Server2 (192.168.1.10):

```bash
# Install iSCSI initiator
sudo apt-get install open-iscsi

# Start iSCSI service
sudo systemctl start iscsid
sudo systemctl enable iscsid

# Discover targets on Server1
sudo iscsiadm -m discovery -t st -p 192.168.1.9

# Output:
# 192.168.1.9:3260,1 iqn.2024-01.example.com:storage.disk1

# Log in to target
sudo iscsiadm -m node --targetname iqn.2024-01.example.com:storage.disk1 \
    -p 192.168.1.9 --login

# Verify connection
sudo iscsiadm -m session -o show

# Find new device
lsblk
# Should see new device (e.g., /dev/sdb)

# Format and mount
sudo mkfs.ext4 /dev/sdb
sudo mkdir /mnt/iscsi_storage
sudo mount /dev/sdb /mnt/iscsi_storage

# Make permanent
echo "/dev/sdb /mnt/iscsi_storage ext4 defaults,_netdev 0 0" | \
    sudo tee -a /etc/fstab
```

#### On Server3 (192.168.1.11):
(Repeat same steps as Server2)

### Result:

```
Server1 (192.168.1.9): iSCSI Target
  └─ Provides: iqn.2024-01.example.com:storage.disk1 (10GB LUN)
  
Server2 (192.168.1.10): iSCSI Initiator
  └─ Mounts: /mnt/iscsi_storage
  └─ Connected to Server1 storage
  
Server3 (192.168.1.11): iSCSI Initiator
  └─ Mounts: /mnt/iscsi_storage
  └─ Connected to Server1 storage
  
Result:
  Both Server2 and Server3 can access same storage
  Block-level access over network
  Appears as local disk on each server
  
CAVEAT:
  Concurrent writes can corrupt data!
  Need shared filesystem (GFS2, OCFS2) or access coordination
```

### iSCSI in Production (OpenStack Cinder):

```
For OpenStack:
  - cinder-volume service manages iSCSI LUNs
  - Instances attach via iSCSI automatically
  - Target lifecycle managed by Cinder
  - Instance sees device /dev/vdb, /dev/vdc, etc.
  
Configuration:
  cinder.conf:
    volume_backend_name = iscsi_backend
    volume_driver = cinder.volume.drivers.iscsi.ISCSIDriver
    iscsi_ip_address = 192.168.1.9
    iscsi_port = 3260
```

---

# QUICK REFERENCE: PAST EXAM TOPICS

## Most Repeated Questions:

1. **Lustre Architecture** (QP10, QP12, QP2, QP3, QP16)
   → Always explain MDT (single), OSTs (distributed), and LOV mapping

2. **CAP Theorem** (QP10, QP2, QP3, QP4, QP5, QP14, QP17)
   → State 3 properties, explain CP vs AP vs CA, how to choose

3. **Replication Types** (QP10, QP3, QP12, QP6, QP17)
   → Leader-based (sync/async), Leaderless (quorum w+r>n)

4. **Rebalancing Partitions** (QP10, QP2, QP3, QP4, QP6, QP17)
   → 4 strategies, why necessary, dynamic vs proportional

5. **Consistency Models** (QP2, QP7, QP15)
   → Order: eventual < PRAM < causal < sequential < linearizable

6. **GlusterFS vs Lustre** (QP14, QP4, QP7)
   → Distributed vs centralized metadata, replication, scalability

7. **Request Routing** (QP16)
   → 4 approaches, diagrams, pros/cons

8. **OpenStack Storage** (QP13)
   → Cinder (block), Swift (object), Manila (file), use cases

---

**END OF DOCUMENT**

All questions from QP2-QP17 (Dec 2021 - May 2024) answered with detailed explanations, diagrams, and real-world examples.

Total Coverage:
- Lustre & GlusterFS: Detailed architecture with diagrams
- CAP Theorem: All 3 properties explained, practical choices
- Replication: Leader-based and leaderless with examples
- Rebalancing: 4 strategies compared
- Consistency Models: 5 models with ordering
- Request Routing: 4 approaches with diagrams
- S3 Object Storage: Complete working explanation
- OpenStack Storage: Cinder, Swift, Manila comparison
- iSCSI & Block Storage: Setup and configuration

