# PES University - Cloud Computing ISA Exam Preparation
## Unit 3: Storage and Distributed Systems
### Total: 100 MCQs + 20 Four-Mark Questions

---

# PART 1: 100 MCQs

## INTRODUCTION TO STORAGE (Questions 1-12)

**1.** A company needs to store data from sensors that cannot be categorized into tables or rows. Which type of data are they dealing with?
A. Structured data
B. Unstructured data
C. Semi-structured data
D. Metadata

**2.** Which of the following is NOT a characteristic of file storage?
A. Organized in directories and subdirectories
B. Can be accessed via SMB/CIFS protocols
C. Provides low latency block access
D. Accessed through file systems like ext4 or NTFS

**3.** True or False: Object storage allows direct modification of portions of an object without replacing the entire object.
A. True, objects support partial updates
B. False, objects must be replaced entirely
C. True, but only for metadata
D. False, objects cannot be stored

**4.** A bank needs storage that can handle thousands of simultaneous read/write operations with minimal latency. Which storage type is most suitable?
A. File storage
B. Block storage
C. Object storage
D. Archive storage

**5.** In DAS (Direct Attached Storage), what is the main limitation?
A. Cannot be used for performance-critical applications
B. Data cannot be shared among multiple servers
C. Requires expensive network infrastructure
D. All data is lost when the computer restarts

**6.** Which statement about NAS (Network Attached Storage) is correct?
A. It requires Fibre Channel connectivity
B. It is connected via Ethernet and provides file-level access
C. It can only be used within a single server
D. It provides block-level access like SAN

**7.** A company implements RAID 5 for their data center. A single disk fails. What happens?
A. All data is permanently lost
B. Data can be reconstructed from parity information
C. The entire RAID array must be replaced
D. Only the data on that disk is lost

**8.** Disk latency comprises three components. Which is NOT one of them?
A. Seek Time
B. Rotational Latency
C. Transfer Time
D. Encryption Latency

**9.** A disk has seek time of 5ms, rotational latency of 4ms, and transfer time of 2ms. What is the total disk latency?
A. 9ms
B. 11ms
C. 15ms
D. 20ms

**10.** Which storage characteristic is most important for a video streaming application?
A. Cost
B. Speed and Scalability
C. Reliability
D. Management simplicity

**11.** True or False: SAN provides file-level access through NFS protocol.
A. True, SAN operates at file level
B. False, SAN operates at block level
C. True, but only for Windows systems
D. False, SAN cannot provide any access

**12.** A startup needs affordable storage that can be accessed from multiple servers over a network. The data is in medical images (unstructured). Which should they choose?
A. DAS
B. Block Storage via SAN
C. NAS with file-level access
D. Local storage on each server

---

## CLOUD STORAGE AND ENABLERS (Questions 13-24)

**13.** Which of the following is NOT one of the 8 characteristics of cloud storage?
A. Low TCO (Total Cost of Ownership)
B. Unlimited Scalability
C. Guaranteed Zero Latency
D. Disaster Recovery capabilities

**14.** In the 4-layer cloud storage architecture, which layer is responsible for user access?
A. Data Storage
B. Data Management
C. User Access Layer
D. Data Service Layer

**15.** A SaaS company provides cloud storage with multitenancy. Multiple customers share the same physical infrastructure but see isolated views. Which characteristic enables this?
A. Elasticity
B. Multitenancy with data isolation
C. On-Demand provisioning
D. Universal Access

**16.** Which statement best describes Thin Provisioning?
A. Storage capacity allocated equals physical storage available
B. Logical storage appears larger than physical storage; allocated on-demand
C. All storage is pre-allocated to prevent bottlenecks
D. Storage is provisioned in fixed blocks only

**17.** True or False: NFS (Network File System) provides block-level access to storage.
A. True, NFS uses block protocol
B. False, NFS provides file-level access
C. True, but only over local networks
D. False, NFS is only for Windows

**18.** In LVM (Logical Volume Manager), what is the sequence of abstraction layers?
A. Logical Volume → Volume Group → Physical Volume → Physical Disk
B. Physical Disk → Physical Volume → Volume Group → Logical Volume
C. Physical Disk → Logical Volume → Physical Volume → Volume Group
D. Volume Group → Physical Disk → Physical Volume → Logical Volume

**19.** A data center administrator wants to allocate more storage to a critical application without disrupting service. Which LVM feature enables this?
A. Snapshots
B. Mirroring
C. Logical volume extension
D. Physical disk replacement

**20.** Which of the following is a function of the nfsd daemon in NFS?
A. Schedule data backups
B. Handle client file system requests on the server side
C. Encrypt all network traffic
D. Monitor disk health

**21.** True or False: Thin provisioning always saves money compared to thick provisioning.
A. True, it always reduces costs
B. False, savings depend on actual usage patterns and SLA requirements
C. True, but introduces performance issues
D. False, it has no cost difference

**22.** A company implements cloud storage with elasticity and on-demand provisioning. During peak hours, their application scales from 100 GB to 500 GB storage usage. When traffic reduces, it scales back to 100 GB. Which characteristics are demonstrated?
A. Elasticity and unlimited scalability
B. Low TCO and disaster recovery
C. Multitenancy and universal access
D. Data durability and elasticity

**23.** In a cloud storage system, 'Universal Access' means:
A. Anyone without authentication can access all data
B. Data can be accessed using different protocols (HTTP, S3 API, etc.) from anywhere
C. All users have equal permissions
D. The system works on all devices without modification

**24.** Which NFS component allows a storage device to be attached to a client's file system?
A. NFS daemon
B. mount command
C. File handle
D. File descriptor

---

## STORAGE VIRTUALIZATION (Questions 25-39)

**25.** Storage virtualization abstracts physical storage into logical entities. Which of these is a logical entity in storage virtualization?
A. Physical disk
B. Virtual volume
C. Disk controller
D. Network interface

**26.** A company uses Lustre for storing large scientific datasets (terabytes of files). Why is Lustre a good choice?
A. Better for small files with frequent metadata operations
B. Excellent for large files with high throughput requirements
C. Provides stronger consistency guarantees than alternatives
D. Has the lowest total cost of ownership

**27.** In Lustre architecture, what is the role of MDT (Metadata Target)?
A. Stores actual file data
B. Manages metadata and file structure; typically centralized (single point)
C. Provides access to objects
D. Handles network communication

**28.** True or False: Lustre uses Object Storage Targets (OSTs) to store actual file data in a distributed manner.
A. False, OSTs only store metadata
B. True, OSTs store data; OSS manages them
C. False, Lustre stores everything on MDT
D. True, but OSTs are centralized

**29.** A developer is troubleshooting a Lustre system where metadata operations on small files are very slow, but large file I/O is fast. What is the most likely issue?
A. Network bandwidth is insufficient
B. OST is failing
C. MDT is becoming a bottleneck (centralized metadata)
D. Clients are not using POSIX interface

**30.** In GlusterFS, what is an "AFR Translator"?
A. Application File Router
B. Automatic File Replication
C. Advanced File Recovery
D. Asynchronous File Reader

**31.** Which statement accurately describes the key difference between Lustre and GlusterFS?
A. Lustre has centralized metadata (MDT), GlusterFS has distributed metadata
B. Lustre is faster than GlusterFS for all workloads
C. GlusterFS uses OSTs, Lustre uses bricks
D. Lustre does not support replication

**32.** In GlusterFS, what are "storage bricks"?
A. Physical disks only
B. Logical partitions on a single server
C. Server + attached storage (DAS/SAN) + filesystem (ext3/ext4)
D. Virtual storage containers

**33.** True or False: GlusterFS requires a central client software that must be installed on all machines accessing the storage.
A. True, central client is mandatory
B. False, GlusterFS client is optional; native protocols can be used
C. True, but only for metadata operations
D. False, GlusterFS has no clients

**34.** A company uses GlusterFS with AFR (Automatic File Replication) enabled. One brick fails. What happens?
A. All data is lost
B. Data becomes unavailable until the brick is restored
C. Data remains accessible from replica on another brick
D. The system automatically creates a new brick

**35.** In block-level virtualization, which approach stores RAID or LUN configuration in the storage device firmware?
A. Host-based (LVM)
B. Storage-device-based
C. Network-based (SAN)
D. Application-based

**36.** A company implements host-based block virtualization using LVM. What is the main advantage?
A. Works across multiple servers automatically
B. Lower latency, operations managed by host OS
C. No single point of failure
D. Simplest to manage in distributed environments

**37.** True or False: Fibre Channel SAN provides the most scalable block-level virtualization solution.
A. False, iSCSI is more scalable
B. True, SAN is switch-based and highly scalable
C. True, but only for small networks
D. False, SAN has no scalability

**38.** In a network-based SAN with in-band architecture, what path does I/O traffic take?
A. Directly from client to storage device
B. Through the SAN appliance (all I/O flows through it)
C. Only metadata through appliance, data through direct connection
D. Through multiple appliances in parallel

**39.** A company implements out-of-band SAN architecture. Which statement is correct?
A. Only metadata goes through the appliance; I/O data goes directly (requires client agents)
B. All traffic must go through the appliance
C. No appliance is needed
D. The appliance manages data, not metadata

---

## OBJECT STORAGE (Questions 40-51)

**40.** Which statement best defines object storage?
A. Data organized in files and folders
B. Data organized in blocks accessed by address
C. Data stored as objects with flat namespace, accessed by URI using REST APIs
D. Data stored temporarily for cache purposes

**41.** True or False: In object storage, two objects can have the same key in the same namespace.
A. True, keys can be duplicate
B. False, flat namespace prevents name collisions (all keys must be unique)
C. True, but differentiated by metadata
D. False, but duplicates can exist in different namespaces

**42.** A developer is building an application using Amazon S3. She wants to store objects with this URL format: http://bucketname.s3.amazonaws.com/myimage.jpg. Which of these is the key?
A. bucketname
B. myimage.jpg
C. s3.amazonaws.com
D. s3

**43.** An organization needs data to survive any 2 disk failures in a 10-node object storage system. Which approach is most cost-effective?
A. Replication (3 copies on all objects)
B. Replication (2 copies, limited to specific critical objects)
C. Erasure Coding (break data into fragments + redundant pieces across nodes)
D. Keeping only 1 copy

**44.** True or False: Amazon S3 RRS (Reduced Redundancy Storage) is designed for critical, mission-critical data.
A. True, RRS is for critical data
B. False, RRS survives only 1 failure and is for non-critical data
C. True, but with lower durability
D. False, RRS cannot store any data

**45.** A developer uploads a large 5 GB video file to S3 using multi-part upload. During upload, one part fails. What must they do?
A. Restart the entire upload from the beginning
B. Retry only the failed part; successful parts remain
C. Delete the object and reupload manually
D. Contact AWS support to recover

**46.** In Amazon S3, where are audit logs for bucket access stored?
A. In the same bucket by default
B. In a DIFFERENT bucket (critical for compliance)
C. In CloudWatch only
D. Cannot be stored, only viewed

**47.** Which of the following is NOT an S3 object operation?
A. GET
B. PUT
C. DELETE
D. MODIFY

**48.** In OpenStack Swift, what is a "partition" in the context of data management?
A. A physical disk division
B. The core of replication; reassigned when a node is added
C. A container holding multiple objects
D. A network segment

**49.** True or False: OpenStack Swift's ring structure maps container names to physical disks.
A. True, containers are mapped directly
B. False, the ring maps partitions to physical disks using consistent hash
C. True, but only for metadata
D. False, Swift has no ring structure

**50.** A company uses Amazon DynamoDB for a mobile app. The app requires strong consistency across all reads. Which statement is correct?
A. DynamoDB provides strong consistency across all reads
B. DynamoDB provides item-level consistency only; app must handle conflicts
C. DynamoDB joins related data automatically
D. DynamoDB synchronizes writes instantly across all regions

**51.** In DynamoDB, if you want to query items by an attribute other than the primary key, what must you create?
A. A new table
B. A secondary index
C. A global partition key
D. A backup

---

## PARTITIONING AND CONSISTENT HASHING (Questions 52-63)

**52.** What is the primary goal of partitioning data?
A. Increase security
B. Spread data and query load evenly across nodes
C. Ensure all data is in one location
D. Reduce replication

**53.** A company partitions user data by region. During a marketing campaign in India, 80% of queries target Indian users while only 5% target American users. What problem does this demonstrate?
A. Bad network design
B. Insufficient storage
C. Hot spot (uneven load distribution)
D. Successful partitioning

**54.** True or False: In key-range partitioning, range queries can be efficiently executed because data is sorted by key.
A. False, range queries are slow
B. True, range queries can be executed efficiently; risk is hot spots with timestamp keys
C. True, but only on single-node systems
D. False, keys cannot be sorted

**55.** A company uses hash(key) mod N for partitioning where N is the number of nodes. They add a new node, making N = N+1. What is the impact?
A. Only a few keys need remapping
B. Almost all keys need remapping (rehashing problem)
C. No keys are affected
D. The system automatically redistributes data

**56.** Consistent hashing arranges keys on a ring (0° to 360°). A key is assigned to which server?
A. The server at exactly the same angle
B. The server whose angle is lower than the key angle
C. The server at the closest angle clockwise
D. The closest server counterclockwise (ring direction)

**57.** True or False: Consistent hashing solves the rehashing problem by ensuring that when a new server is added, only nearby keys are remapped.
A. False, all keys are still remapped
B. True, consistent hashing limits remapping to nearby keys on the ring
C. True, but it doubles the latency
D. False, consistent hashing has nothing to do with rehashing

**58.** In OpenStack Swift, which concept uses consistent hashing to map partitions to physical disks?
A. AFR
B. Ring
C. Partition scheme
D. Load balancer

**59.** A search engine indexes documents. For query "Cloud Computing", it needs to search across many nodes. Which secondary index type is most efficient for reads?
A. Document-based index (local to each partition)
B. Term-based global index (all terms in one place)
C. Both are equally efficient
D. Neither can handle this query

**60.** True or False: Document-based secondary indexes require scatter-gather reads across all partitions for a single query.
A. True, document indexes require scatter-gather
B. False, document indexes are fully local
C. True, but only on the first query
D. False, document indexes have no read overhead

**61.** In a database using term-based global indexes, what is the trade-off?
A. Fast reads, fast writes
B. Fast reads, slow and asynchronous writes
C. Slow reads, fast writes
D. No performance impact

**62.** A company partitions customers by customer_id for workload-driven partitioning. Related customer orders are also stored with the same partition key. What is the advantage?
A. Reduces distributed transactions and co-locates related data
B. Increases query latency
C. Prevents data replication
D. Eliminates the need for indexes

**63.** True or False: Random assignment partitioning guarantees even data distribution.
A. False, random assignment can result in skewed data
B. True, random ensures perfect balance
C. True, but with higher query latency
D. False, random is never used in practice

---

## REBALANCING PARTITIONS AND REQUEST ROUTING (Questions 64-73)

**64.** Why might a company need to rebalance partitions? (Select the BEST reason)
A. To increase security
B. To spread throughput increase, handle dataset growth, and tolerate node failures
C. To reduce the number of queries
D. To simplify backups

**65.** True or False: Hash mod N rebalancing is efficient when adding new nodes.
A. True, it is efficient
B. False, it is expensive because almost all keys are remapped
C. True, but only for small datasets
D. False, hash mod N does not exist

**66.** In fixed partitions rebalancing strategy, what happens when data needs to be rebalanced?
A. Entire database is copied
B. Partitions are stolen from one node to another; old assignment is maintained during transfer
C. New partitions are created from scratch
D. No data moves; only metadata changes

**67.** Dynamic partitioning automatically splits partitions when they grow large and merges them when they shrink. What is the main advantage?
A. Reduces memory usage
B. Adapts to data volume changes; starts with single partition, splits as needed
C. Increases latency
D. Eliminates the need for backups

**68.** In proportional-to-nodes rebalancing, if you have 3 nodes with 10 partitions each and add a 4th node, what happens?
A. The new node gets no partitions
B. Each existing node loses 2.5 partitions to the new node
C. The new node splits random partitions from existing nodes and takes half
D. All data is redistributed equally

**69.** True or False: A routing tier (approach 2 for request routing) directly handles user requests without forwarding.
A. True, it handles requests
B. False, it is partition-aware but only routes requests, does NOT handle them
C. True, but only for read requests
D. False, it has no routing capability

**70.** In request routing approach 3, clients are aware of the partition mapping. What is an advantage?
A. No routing overhead; clients connect directly
B. Clients must update their mapping when nodes change
C. Faster than server-side routing
D. All of the above

**71.** Which service is commonly used as an authoritative mapping source for partition-to-node information?
A. DNS
B. Load balancer
C. ZooKeeper
D. Firewall

**72.** True or False: ZooKeeper is used by Kafka for managing partition-to-broker mappings.
A. True, old Kafka used ZooKeeper
B. False, Kafka never used ZooKeeper
C. True, modern Kafka still uses it
D. False, Kafka uses only gossip protocol

**73.** Cassandra and Riak use gossip protocol instead of ZooKeeper. What is an advantage?
A. Gossip is faster for all operations
B. No dependency on a central service; nodes communicate peer-to-peer
C. Gossip guarantees immediate consistency
D. Gossip has lower overhead than ZooKeeper

---

## REPLICATION - LEADER BASED AND REPLICATION LAG (Questions 74-85)

**74.** In leader-based replication, which nodes handle both read and write operations?
A. Followers only
B. Leader only
C. All nodes equally
D. Determined by the database

**75.** True or False: Synchronous replication ensures that all followers have received a write before the leader acknowledges success to the client.
A. False, sync replication is not safe
B. True, sync ensures all followers update before acknowledging
C. True, but adds no latency
D. False, only the leader needs to acknowledge

**76.** A company implements asynchronous replication. The leader crashes immediately after acknowledging a write to the client. What could happen?
A. The data is safely on all followers
B. The data might be lost if the leader crashes before followers are updated
C. Followers automatically promote themselves
D. The client must resubmit the write

**77.** Which log implementation is storage-engine specific and fastest for replication?
A. Statement-based log
B. WAL (Write Ahead Log) Shipping
C. CDC (Change Data Capture)
D. Trigger-based log

**78.** True or False: Trigger-based logs have the highest flexibility but also the highest overhead.
A. True, triggers are flexible but slow
B. False, triggers have minimal overhead
C. True, but triggers are mandatory
D. False, triggers cannot implement custom logic

**79.** A follower falls behind in replication. To catch up, the system uses the replication log. Which log type is most portable across different storage engines?
A. WAL Shipping
B. CDC/Logical Log
C. Trigger-based
D. Statement-based

**80.** In automatic failover, what risk occurs when the leader fails and takes too long to be detected?
A. Data loss
B. Split brain (both old and new leaders active, causing conflicts)
C. Increased latency
D. Storage corruption

**81.** True or False: Multi-leader replication (master/master) is suitable for single-datacenter deployments where consistency is critical.
A. True, multi-leader is best for single site
B. False, multi-leader is for multi-datacenter, offline clients, collaborative editing
C. True, but with performance penalties
D. False, multi-leader has no replication

**82.** In multi-leader replication with conflicting writes to the same record by different leaders, which approach gives priority to the most recent write (by timestamp)?
A. Conflict avoidance
B. Last Write Wins (LWW)
C. Custom resolution
D. Manual conflict detection

**83.** A company implements multi-leader replication across datacenters. To prevent single point of failure, what topology is best?
A. Circular (A→B→C→A)
B. Star (all nodes connect to central node)
C. All-to-all (every node replicates to every other)
D. Linear (A→B→C)

**84.** True or False: Monitoring replication lag is easier in multi-leader systems than single-leader systems because there is no fixed order.
A. True, multi-leader is easier to monitor
B. False, monitoring is harder in leaderless systems because there is no fixed order
C. True, but multi-leader is less reliable
D. False, both have equal difficulty

**85.** A company uses single-leader replication with eventual consistency. To ensure clients always read their own writes, what solution is used?
A. Read from leader only
B. Increase replication speed
C. Stronger consistency
D. No solution exists

---

## LEADERLESS REPLICATION (Questions 86-93)

**86.** In leaderless replication, how many nodes must acknowledge a write for it to be considered successful?
A. All nodes (quorum = n)
B. A majority (quorum = (n+1)/2)
C. Write quorum w, where w + r > n (usually w = r = (n+1)/2)
D. One node

**87.** True or False: In a 5-node leaderless system with w=3 and r=3, reading from 3 nodes guarantees a fresh (latest) value.
A. False, this doesn't guarantee fresh reads
B. True, because w + r > n (3+3=6 > 5), overlap is guaranteed
C. True, but only on the first read
D. False, leaderless systems cannot guarantee fresh reads

**88.** A leaderless system has n=3 nodes. For the formula w = r = (n+1)/2 to work optimally, what must n be?
A. Any number
B. Odd number (so (n+1)/2 is an integer)
C. Even number
D. Prime number

**89.** In leaderless replication, a node is down during a write. The write succeeds on other nodes. When the failed node recovers, it has stale data. What mechanisms catch it up?
A. Write replay only
B. Read repair and anti-entropy (gossip)
C. Re-quorum read
D. Automatic rollback

**90.** True or False: Last Write Wins (LWW) resolves concurrent writes in leaderless systems without data loss.
A. True, LWW always works perfectly
B. False, LWW can lose data when writes happen concurrently
C. True, but only with synchronized clocks
D. False, leaderless systems have no conflict resolution

**91.** Which leaderless systems are mentioned in the course materials? (Select the BEST answer)
A. MongoDB, MySQL
B. Amazon Dynamo, Cassandra, Riak
C. PostgreSQL, Oracle
D. Redis, Memcached

**92.** True or False: Leaderless systems can operate across multiple datacenters by waiting for global quorum acknowledgment before responding.
A. True, wait for global quorum always
B. False, they wait for local quorum only; cross-DC replication is asynchronous
C. True, but this increases latency
D. False, leaderless cannot span datacenters

**93.** In version vector algorithms for concurrent writes, what information is included with each write?
A. Timestamp only
B. Version numbers from the server
C. Server ID only
D. Client ID only

---

## CONSISTENCY MODELS (Questions 94-105)

**94.** Arrange consistency models from weakest to strongest:
A. Sequential, Causal, PRAM, Eventual, Linearizable
B. Eventual, PRAM, Causal, Sequential, Linearizable
C. Linearizable, Sequential, Causal, PRAM, Eventual
D. Cannot be ordered

**95.** Which consistency model only guarantees that a single process writes in order, but different processes may see different orders?
A. Eventual consistency
B. Causal consistency
C. PRAM (Pipelined RAM)
D. Linearizable consistency

**96.** True or False: Sequential consistency requires deterministic ordering of all operations and is equivalent to linearizability.
A. True, they are the same
B. False, sequential is deterministic but not real-time; linearizable is also real-time
C. True, but sequential is stronger
D. False, they are completely different

**97.** A developer implements Compare-and-Set (CAS) operation: cas(x, v_old, v_new). Which consistency model does this require?
A. Eventual consistency
B. Causal consistency
C. Linearizable consistency
D. PRAM consistency

**98.** Which statement best describes linearizability?
A. All processes see writes in the same order
B. Single-copy illusion; once any process reads a new value, all subsequent reads must see it
C. Writes are propagated eventually
D. Causal relationships are maintained

**99.** True or False: A multi-leader replication system can provide linearizable consistency.
A. True, multi-leader provides linearizability
B. False, multi-leader cannot guarantee linearizability due to independent writes
C. True, with special protocols
D. False, multi-leader has no consistency

**100.** In a single-leader replication with synchronous followers, which consistency model is achievable?
A. Eventual consistency only
B. Causal consistency
C. Potentially linearizable consistency
D. No consistency guarantee

**101.** Which consistency model can tolerate causally related operations being seen in different orders on different nodes?
A. Linearizable
B. Sequential
C. PRAM
D. Causal (by definition, respects causality)

**102.** True or False: Strict consistency is another name for linearizable consistency.
A. False, they are different
B. True, strict = linearizable = atomic consistency
C. True, but strict is stronger
D. False, strict has no formal definition

**103.** A system uses ZooKeeper (consensus-based) for replication. Which consistency model does this provide?
A. Eventual consistency
B. Linearizable consistency
C. Causal consistency
D. PRAM consistency

**104.** True or False: Leaderless replication with quorum (w+r>n) can provide linearizable consistency.
A. True, quorum provides linearizability
B. False, leaderless replication probably cannot guarantee linearizability
C. True, but only with additional protocols
D. False, leaderless has no consistency

**105.** Which real-world databases provide linearizable consistency?
A. Cassandra, DynamoDB
B. PostgreSQL, MySQL (single-leader), ZooKeeper
C. CouchDB, eventual consistency databases
D. All databases provide linearizability

---

## CAP THEOREM (Questions 106-113)

**106.** The CAP theorem states that a distributed system can guarantee at most 2 of 3 properties. Which property is about handling network partitions?
A. Consistency
B. Availability
C. Partition Tolerance
D. All three must be present

**107.** True or False: In the CAP theorem, you can always achieve all 3 properties in cloud systems.
A. True, modern systems provide all 3
B. False, partition tolerance is unavoidable in cloud, so choose CP or AP
C. True, but with high cost
D. False, CAP is outdated

**108.** When a network partition occurs and you must continue operating, which choice prioritizes data correctness?
A. Choose AP (Availability + Partition)
B. Choose CP (Consistency + Partition)
C. Choose CA (Consistency + Availability)
D. Shut down until partition heals

**109.** Which databases are mentioned as AP (choosing availability over consistency)?
A. PostgreSQL, MySQL
B. ZooKeeper, HBase
C. Cassandra, CouchDB
D. All relational databases

**110.** True or False: ZooKeeper is a CP system (Consistency + Partition Tolerance) but sacrifices availability during partitions.
A. False, ZooKeeper is AP
B. True, CP systems prioritize consistency and may become unavailable
C. True, but this is always bad
D. False, ZooKeeper is always available

**111.** CA systems (Consistency + Availability) are common in:
A. Cloud distributed systems
B. Single-datacenter or LAN environments with no partitions
C. Multi-datacenter systems
D. All modern systems

**112.** True or False: The CAP theorem applies only during network partitions; when no failures occur, systems can have all 3 properties.
A. False, CAP always applies
B. True, during normal operation both A and C are possible
C. True, but this is rare
D. False, no system can have CA together

**113.** A practical problem with CAP: when a partition occurs, a company must decide if users should get stale data (AP) or errors (CP). Which real-world scenario favors AP?
A. Banking transactions
B. Social media feeds with eventual consistency acceptable
C. Medical records
D) Stock trading

---

## DISTRIBUTED TRANSACTIONS AND 2PC (Questions 114-125)

**114.** What does ACID stand for in distributed transactions?
A. Access, Control, Information, Data
B. Atomic, Consistent, Isolated, Durable
C. Architecture, Computer, Internet, Database
D. Application, Concurrency, Index, Distribution

**115.** True or False: An atomic transaction means all steps execute or none execute; partial execution is not allowed.
A. False, partial execution is normal
B. True, atomic = all or nothing
C. True, but with time limits
D. False, transactions can split

**116.** In 2PC (Two-Phase Commit), what happens in Phase 1 (Prepare/Vote)?
A. Sites commit changes
B. Coordinator sends "Prepare T", sites respond with "Ready T" or "Don't Commit T"
C. Coordinator sends commit message
D. Sites release locks

**117.** True or False: In 2PC Phase 1, if a site responds "Ready T", it enters pre-committed state and can still self-abort.
A. True, pre-committed sites can still abort
B. False, pre-committed sites hold locks and cannot self-abort
C. True, but releases locks immediately
D. False, no pre-committed state exists

**118.** What is the primary risk of 2PC?
A. Too fast
B. Blocking: if coordinator fails after Phase 1, sites remain locked indefinitely
C. Data loss
D. Requires encryption

**119.** True or False: The coordinator logs "Prepare T" BEFORE sending messages to sites.
A. False, logging happens after
B. True, coordinator logs before sending to ensure recovery capability
C. True, but only for critical transactions
D. False, no logging is done

**120.** In 2PC Phase 2, if the coordinator receives "Don't Commit T" from ANY site, what happens?
A. That site's transaction commits anyway
B. The coordinator sends "Commit T" to others
C. The coordinator sends "Abort T" to all sites; all rollback
D. Voting continues on other sites

**121.** After a site executes "Commit T" in Phase 2, what must it do?
A. Immediately release locks
B. Commit changes, release locks, log "Done", and send done message
C. Keep locks until all sites confirm
D. Cannot release locks

**122.** True or False: 2PC guarantees immediate global consistency across all sites.
A. True, 2PC is instant
B. False, 2PC is blocking and can take time; consistency depends on implementation
C. True, but only for small transactions
D. False, 2PC has no consistency

**123.** A nested transaction is a transaction within a transaction. In this case, if the inner transaction fails, what happens?
A. Entire outer transaction must rollback
B. Inner transaction's private workspace is discarded; outer continues
C. Both commit separately
D. Error is thrown to user

**124.** Distributed transaction architecture consists of:
A. Single transaction manager
B. Local Transaction Managers (one per site) + Transaction Coordinator
C. Only database servers
D. Network routers

**125.** True or False: In distributed transactions, isolation means transactions do not interfere; one transaction cannot see uncommitted data from another.
A. True, isolation prevents dirty reads
B. False, isolation is just a label
C. True, but only in 2PC
D. False, distributed systems have no isolation

---

# ANSWER KEY FOR 100 MCQs

| Q # | Answer | Q # | Answer | Q # | Answer | Q # | Answer | Q # | Answer |
|-----|--------|-----|--------|-----|--------|-----|--------|-----|--------|
| 1 | **B** | 21 | **B** | 41 | **B** | 61 | **B** | 81 | **B** |
| 2 | **C** | 22 | **A** | 42 | **B** | 62 | **A** | 82 | **B** |
| 3 | **B** | 23 | **B** | 43 | **C** | 63 | **A** | 83 | **C** |
| 4 | **B** | 24 | **B** | 44 | **B** | 64 | **B** | 84 | **B** |
| 5 | **B** | 25 | **B** | 45 | **B** | 65 | **B** | 85 | **A** |
| 6 | **B** | 26 | **B** | 46 | **B** | 66 | **B** | 86 | **C** |
| 7 | **B** | 27 | **B** | 47 | **D** | 67 | **B** | 87 | **B** |
| 8 | **D** | 28 | **B** | 48 | **B** | 68 | **C** | 88 | **B** |
| 9 | **B** | 29 | **C** | 49 | **B** | 69 | **B** | 89 | **B** |
| 10 | **B** | 30 | **B** | 50 | **B** | 70 | **D** | 90 | **B** |
| 11 | **B** | 31 | **A** | 51 | **B** | 71 | **C** | 91 | **B** |
| 12 | **C** | 32 | **C** | 52 | **B** | 72 | **A** | 92 | **B** |
| 13 | **C** | 33 | **B** | 53 | **C** | 73 | **B** | 93 | **B** |
| 14 | **C** | 34 | **C** | 54 | **B** | 74 | **B** | 94 | **B** |
| 15 | **B** | 35 | **B** | 55 | **B** | 75 | **B** | 95 | **C** |
| 16 | **B** | 36 | **B** | 56 | **D** | 76 | **B** | 96 | **B** |
| 17 | **B** | 37 | **B** | 57 | **B** | 77 | **B** | 97 | **C** |
| 18 | **B** | 38 | **B** | 58 | **B** | 78 | **A** | 98 | **B** |
| 19 | **C** | 39 | **A** | 59 | **B** | 79 | **B** | 99 | **B** |
| 20 | **B** | 40 | **C** | 60 | **A** | 80 | **B** | 100 | **C** |

**Answer Key (continued):**

| Q # | Answer |
|-----|--------|
| 101 | **C** |
| 102 | **B** |
| 103 | **B** |
| 104 | **B** |
| 105 | **B** |
| 106 | **C** |
| 107 | **B** |
| 108 | **B** |
| 109 | **C** |
| 110 | **B** |
| 111 | **B** |
| 112 | **B** |
| 113 | **B** |
| 114 | **B** |
| 115 | **B** |
| 116 | **B** |
| 117 | **B** |
| 118 | **B** |
| 119 | **B** |
| 120 | **C** |
| 121 | **B** |
| 122 | **B** |
| 123 | **B** |
| 124 | **B** |
| 125 | **A** |

---

# PART 2: 20 FOUR-MARK QUESTIONS WITH COMPLETE ANSWERS

---

**Q1. Explain the three types of storage (File, Block, Object) with examples and their key differences.**

**Answer:**
File storage organizes data in a hierarchical file system using directories and subdirectories, accessed through protocols like SMB/CIFS or NFS (e.g., Windows file shares, NAS systems). Block storage provides raw storage at the block level, identified by address rather than name, offering low latency and used in RAID systems and SANs (e.g., iSCSI, Fibre Channel). Object storage uses a flat namespace with REST HTTP APIs where each object is identified by a unique URI key and accessed through GET/PUT/DELETE operations (e.g., Amazon S3, OpenStack Swift). File storage is best for unstructured data and user files, block storage for databases and high-performance applications, and object storage for massive-scale data with durability requirements.

**Key points for full marks:** (1) Hierarchical file system with directory structure (2) Block-level access by address (3) Flat namespace with REST APIs (4) Example for each (5) Use case differences

---

**Q2. What is RAID and why is it used in data centers? Explain with an example.**

**Answer:**
RAID (Redundant Array of Independent Disks) combines multiple physical disks into a single logical disk to improve performance, reliability, and fault tolerance. RAID is used in data centers to protect critical data: if one disk fails in RAID 5, the data can be reconstructed from parity information stored across other disks, ensuring no data loss. For example, a 4-disk RAID 5 array can tolerate one disk failure; if Disk 2 fails, the data is recovered using the parity bits and remaining disks. RAID provides redundancy without storing complete copies, making it more storage-efficient than simple replication while maintaining high availability for mission-critical systems.

**Key points for full marks:** (1) Multiple disks in single logical unit (2) Improves reliability and fault tolerance (3) Parity-based reconstruction (4) Specific example with RAID level (5) Use in data centers for high availability

---

**Q3. Explain the disk latency formula and its three components with realistic values.**

**Answer:**
Disk latency = Seek Time + Rotational Latency + Transfer Time. Seek time is the time required for the read/write head to move to the correct track (typically 5-10ms for modern disks). Rotational latency is the time for the desired sector to rotate under the read head (average is half a rotation; at 7200 RPM it is ~4ms). Transfer time is the actual data transfer duration from disk to memory (typically 1-2ms for small reads). For example, accessing a small 4KB file might take 5ms (seek) + 4ms (rotation) + 1ms (transfer) = 10ms total. This explains why solid-state drives (SSDs) with no moving parts (zero seek and rotational latency) are much faster—often <1ms total latency.

**Key points for full marks:** (1) Three components correctly named (2) Seek time explanation with realistic value (3) Rotational latency explanation with RPM context (4) Transfer time explanation (5) Total example or SSD comparison

---

**Q4. Define cloud storage and list its 8 key characteristics.**

**Answer:**
Cloud storage is a model providing on-demand, network-accessible storage of data over the Internet, backed by distributed datacenters and managed by service providers like Amazon S3 or OpenStack Swift. The 8 characteristics are: (1) Low TCO—reduced capital expenditure on hardware; (2) Unlimited Scalability—storage capacity can grow without physical limits; (3) Elasticity—scale up and down based on demand; (4) On-Demand provisioning—pay only for what you use; (5) Universal Access—access data from any location via HTTP/REST APIs; (6) Multitenancy—multiple customers share infrastructure with data isolation; (7) Data Durability—multiple replicas or erasure coding ensure data survives failures; (8) Disaster Recovery—geographic redundancy allows recovery from regional failures. These characteristics make cloud storage ideal for organizations needing flexible, cost-effective, and reliable data storage.

**Key points for full marks:** (1) Definition with service model context (2) All 8 characteristics listed (3) Brief explanation of each OR detailed explanation of 4-5 key ones (4) Example benefit or use case

---

**Q5. Explain LVM (Logical Volume Manager) and its four abstraction layers with a use case.**

**Answer:**
LVM abstracts physical storage into logical entities through four layers: Physical Disk → Physical Volume (PV) → Volume Group (VG) → Logical Volume (LV) → Filesystem. A physical disk is partitioned into physical volumes, which are grouped into volume groups to create a shared pool of storage. Within a volume group, logical volumes are created as virtual "disks" that can be mounted and formatted with filesystems like ext4. For example, if a company has two physical 1TB disks, they create two PVs, combine them into one 2TB VG, and then create two LVs (800GB for database, 1.2GB for backups) from this pool. The key advantage is flexibility—if the database LV needs more space, it can be extended using free space from the VG without downtime or data loss.

**Key points for full marks:** (1) Four layers in correct order (2) Explanation of each layer (3) How PV pool into VG (4) How LV provisioned from VG (5) Use case showing flexibility/resizing benefit

---

**Q6. What is thin provisioning and how does it differ from thick provisioning?**

**Answer:**
Thin provisioning (virtual provisioning) allocates storage on-demand from a shared pool, meaning the logical storage capacity appears larger than the physical storage available. For example, a company has 100GB physical storage but creates a 500GB logical volume; space is allocated only as data is written. Thick provisioning, conversely, pre-allocates all requested storage immediately. Thin provisioning saves costs by avoiding over-provisioning and enables overcommitment, but carries the risk of running out of physical storage if many users write data simultaneously. A realistic scenario: a SaaS company with 1TB physical storage uses thin provisioning to offer 10TB total storage to customers; as long as total usage stays below 1TB, no issue occurs. If total usage exceeds 1TB, storage alerts or failures may trigger. Thin provisioning is ideal for environments with variable usage patterns.

**Key points for full marks:** (1) Definition of thin provisioning with logical > physical (2) On-demand allocation concept (3) Comparison with thick provisioning (4) Cost/benefit analysis (5) Risk example or warning scenario

---

**Q7. Explain the Lustre distributed file system architecture and how it works.**

**Answer:**
Lustre consists of three main components: Lustre Clients (access files via POSIX interface), Metadata Server (MDT) with Object Storage Targets (OSTs managed by Object Storage Servers), and metadata is centralized while data is distributed. When a client accesses a file, it queries the MDT for metadata and file structure, receiving a Layout Object (LOV) that maps file offsets and sizes to specific OSTs. The client then directly reads data from the appropriate OSTs in parallel, bypassing the metadata server for data I/O. Synchronization is lock-based to prevent conflicts. Lustre excels for large files and high-throughput workloads (e.g., scientific simulations on 10TB files), but the centralized MDT becomes a bottleneck for small-file metadata-heavy operations. For example, a genome sequencing lab storing 50GB files benefits from Lustre's parallel I/O, but a photo sharing app with millions of tiny files would struggle due to metadata server saturation.

**Key points for full marks:** (1) MDT and OST components (2) Client POSIX access (3) LOV mapping concept (4) Parallel data I/O (5) Suitability for large files (6) MDT bottleneck for small files

---

**Q8. Differentiate between Lustre and GlusterFS in terms of architecture and use cases.**

**Answer:**
Lustre uses centralized metadata (single MDT) with distributed data (multiple OSTs), while GlusterFS has distributed metadata across all nodes. Lustre provides high throughput for large files via parallel I/O to multiple OSTs but suffers MDT bottleneck on metadata-heavy workloads. GlusterFS stores data in "bricks" (server + DAS/SAN + filesystem) with no central coordinator, using translators (filter-like modules) such as AFR for automatic replication and I/O Scheduler for load balancing; clients are optional. Lustre is ideal for HPC and scientific computing with large datasets (e.g., climate simulation on 100TB files). GlusterFS is better for general-purpose cloud storage and environments requiring high availability without central dependency (e.g., private cloud storage with replication). GlusterFS scales more easily when adding nodes since no MDT rebalancing is needed. The trade-off: Lustre offers higher performance for specific workloads, GlusterFS offers better fault tolerance and scalability.

**Key points for full marks:** (1) Centralized vs distributed metadata (2) OST/MDT vs bricks (3) Translators concept (4) AFR and I/O Scheduler explained (5) Use case or workload suitability for each (6) Scalability/availability differences

---

**Q9. Explain the three levels of block-level virtualization and their trade-offs.**

**Answer:**
Level 1—Host-based (e.g., LVM): The host OS manages virtualization; simple to implement with low latency but limited to single server and poor cross-server scalability. Level 2—Storage-device-based: RAID and LUNs are configured in the storage device firmware; host-agnostic and offers low latency, making it suitable for many servers accessing the same device, but complexity is higher. Level 3—Network-based (SAN via Fibre Channel): Multiple servers connect to centralized storage through a switch-based fabric; most scalable and flexible, supporting many servers and complex SAN topologies, but adds network latency and cost. In-band SAN (all I/O through appliance) simplifies design but can bottleneck; out-of-band (metadata through appliance, data direct via client agents) distributes load. Trade-offs: host-based is simple but limited, storage-device is balanced and efficient, network-based is most scalable but costly and complex. A data center might use host-based LVM for individual servers, storage-device RAID for shared storage, and network-based SAN for enterprise-wide virtualization.

**Key points for full marks:** (1) Three levels with examples (2) Host-based scoping and latency (3) Storage-device host-agnostic benefit (4) Network-based scalability (5) In-band vs out-of-band for SAN (6) Trade-off summary or use case

---

**Q10. What is object storage and how does it differ from file and block storage?**

**Answer:**
Object storage stores data as discrete objects in a flat namespace, each identified by a unique URI key and accessed via REST HTTP APIs (GET, PUT, DELETE, UPDATE operations). Unlike file storage which uses hierarchical directories and file systems, object storage has no name collisions—all keys are globally unique. Unlike block storage which provides raw block access by address (used for databases), object storage includes metadata with each object and is designed for massive scale. Object storage uses commodity hardware and provides durability through either multiple replicas (3+ copies) or erasure coding (data fragments + redundant pieces across nodes). Example: Amazon S3 stores objects in buckets with keys like "mybucket/photos/vacation.jpg", retrievable via HTTPS. Object storage is ideal for unstructured data, backups, archives, and media (images, videos) due to its scalability and durability, while file storage suits user data and block storage suits databases.

**Key points for full marks:** (1) Flat namespace with unique keys/URIs (2) REST API operations (3) Comparison: no hierarchy like files, no block-level like storage (4) Metadata attached to objects (5) Durability mechanisms (replication/erasure coding) (6) Use cases (backups, media)

---

**Q11. Explain Amazon S3 data protection mechanisms: replication, RRS, regions, versioning, and multi-part upload.**

**Answer:**
Amazon S3 provides multiple data protection layers: Standard replication creates 3 copies across different facilities, surviving 2 simultaneous failures for normal data. RRS (Reduced Redundancy Storage) uses fewer replicas (survives 1 failure) and costs less, suitable for non-critical, regenerable data like thumbnails. Regions allow geographic distribution across AWS regions; a bucket can be replicated to another region for disaster recovery. Versioning must be explicitly enabled and keeps all versions of an object; if a file is accidentally deleted or overwritten, previous versions remain recoverable. Multi-part upload divides large files (e.g., 5GB video) into parallel parts, allowing retry of only failed parts rather than re-uploading the entire file. For example, a company storing patient records (critical) uses Standard replication across regions with versioning enabled to survive data corruption. A media company uses RRS for thumbnails and multi-part upload for efficient large video uploads. Audit logs are stored in a DIFFERENT bucket for compliance and to prevent tampering.

**Key points for full marks:** (1) Replication 3 copies standard (2) RRS for non-critical data (3) Regions for geographic redundancy (4) Versioning for recovery (5) Multi-part upload for efficiency (6) Audit logs in separate bucket (7) Use case examples

---

**Q12. Explain OpenStack Swift's architecture: accounts, containers, objects, partitions, and the ring.**

**Answer:**
OpenStack Swift organizes data hierarchically: Accounts (user/tenant) contain Containers (like directories) which contain Objects (actual data files). The Swift ring is a consistent hash structure mapping partitions to physical disks; partitions are the core unit of replication and reassignment. When a new node joins the cluster, partitions are reassigned from existing nodes to distribute load evenly. For example, a company's OpenStack account contains containers "photos" and "videos"; each container holds many objects (vacation.jpg, birthday.mp4). The ring maps partition 42 (containing some of these objects) to physical disks across the cluster using consistent hashing. If a new storage node is added, some partitions move to it, and objects are replicated accordingly. Swift partitions are NOT Linux partitions—they are logical units of replication managed by Swift. This architecture enables linear scalability: adding nodes automatically redistributes partitions without re-hashing all data, unlike hash(key) mod N which would remapp everything.

**Key points for full marks:** (1) Account-container-object hierarchy (2) Partitions as replication unit (3) Ring maps partitions to disks (4) Consistent hashing in ring (5) Reassignment on node addition (6) Logical vs physical partitions (7) Scalability benefit

---

**Q13. What is partitioning in databases? Explain hot spots and skewed data with examples.**

**Answer:**
Partitioning divides data across multiple nodes to spread data volume and query load evenly, improving scalability and query performance. Skewed data means uneven distribution of data across partitions (e.g., 70% of data on one partition due to bad key choice). A hot spot occurs when query load is uneven—one partition receives most queries and becomes overloaded. Example of skew: partitioning user data by country, if 80% of users are from India, the India partition holds 80% of data. Example of hot spot: partitioning by timestamp for event logs; recent events (last day) are queried heavily while old events are rarely accessed, creating a hot spot on the current-day partition. Another hot spot example: during a celebrity's announcement on social media, queries about that celebrity spike while other user data is rarely queried. To mitigate, use compound keys like (user_id, timestamp) or workload-driven partitioning, or add replica partitions for frequently queried data. Understanding these issues is critical for designing scalable databases.

**Key points for full marks:** (1) Partitioning definition and goal (2) Skewed data explanation with example (3) Hot spot explanation with example (4) How they impact performance (5) Mitigation strategies (6) Real-world scenario

---

**Q14. Explain consistent hashing and how it solves the rehashing problem.**

**Answer:**
Consistent hashing arranges keys on a ring (0° to 360°); each key is assigned to the closest server in the clockwise direction. When a server is added or removed, only nearby keys on the ring need remapping, minimizing data redistribution. In contrast, hash(key) mod N (traditional hashing) requires remapping nearly all keys when the number of servers N changes—if N goes from 10 to 11, approximately 90% of keys are reassigned. Example: with consistent hashing and 10 servers on a ring, adding an 11th server at 45° only affects keys currently mapped to servers between 0° and 45°, perhaps 10-15% of total keys. This drastically reduces the overhead of adding/removing servers, making consistent hashing ideal for dynamic systems. OpenStack Swift uses consistent hashing in its ring structure to handle node additions without massive data reshuffling. The trade-off: consistent hashing provides natural load balancing and minimal rebalancing, but requires careful implementation to avoid uneven distribution (solved by placing multiple copies of each server on the ring).

**Key points for full marks:** (1) Ring structure with 0-360° (2) Clockwise key assignment (3) Rehashing problem definition (4) Why hash mod N is expensive (5) Consistent hashing solves it with % remapped (6) Example comparison (7) Real-world use (Swift)

---

**Q15. Explain the four rebalancing strategies for partitions: hash mod N, fixed, dynamic, and proportional-to-nodes.**

**Answer:**
1. Hash mod N: Partitions are assigned by hash(key) mod N; when N changes, nearly all keys remapp, making this inefficient for dynamic systems and should be avoided.
2. Fixed Partitions: Many partitions are created upfront; when adding nodes, partitions are stolen from existing nodes; old partition assignments are maintained during transfer for consistency. Scales linearly but requires pre-planning partition count.
3. Dynamic Partitioning: System starts with one partition, automatically splits when large, merges when small. Adapts naturally to data volume without pre-planning but more complex to implement. Ideal for systems with varying dataset sizes.
4. Proportional-to-Nodes: Each node owns a fixed number of partitions; when a new node joins, it randomly splits partitions from existing nodes and takes half from each. Maintains fixed ratio of partitions per node. Example: 3 nodes × 10 partitions = 30 total; add 4th node, now 40 partitions, each node gets 10. Fixed Partitions are preferred for predictable workloads, Dynamic for variable growth, Proportional-to-Nodes for elastic clusters. Trade-offs: complexity vs. adaptability vs. pre-planning.

**Key points for full marks:** (1) Hash mod N and its inefficiency (2) Fixed partitions mechanism (3) Dynamic partition split/merge logic (4) Proportional-to-nodes math (5) Comparison of when each is used (6) Trade-off analysis

---

**Q16. Explain leader-based replication including sync vs. async replication and their trade-offs.**

**Answer:**
In leader-based replication, one node (leader/master/primary) handles both reads and writes, sending replication logs to follower nodes (slaves/secondaries) which apply changes in the same order and serve read-only requests. Synchronous replication waits for all followers to acknowledge a write before the leader confirms to the client; this ensures all replicas are up-to-date but can block if followers are slow or fail, increasing latency. Asynchronous replication has the leader confirm immediately to the client without waiting for followers; fast but risky—if the leader crashes before followers are updated, the write is lost. Practical systems often use semi-synchronous (wait for at least one follower). Trade-off: sync provides safety and consistency at cost of latency; async provides low latency but risks data loss. Example: banking system uses sync (money must not be lost), social media uses mostly async (lost like is acceptable). Follower failure is tolerated via catch-up recovery using the replication log. Leader failure requires failover (manual or automatic), risking split-brain where both old and new leaders accept writes, causing conflicts.

**Key points for full marks:** (1) Leader handles writes, followers apply (2) Replication log mechanism (3) Sync waits for all, tradeoff (4) Async immediate, risk (5) Semi-sync compromise (6) Failover and split-brain (7) Use case examples

---

**Q17. What is replication lag? Explain its effects and solutions.**

**Answer:**
Replication lag is the delay between a write being committed on the leader and applied on followers. Effects: eventual consistency means a follower may return stale data immediately after a write; a user writes a profile update on the leader, but reads from a follower before it's applied, seeing their old profile. Solutions include: (1) Read from leader only for your own writes (trades availability for consistency). (2) Monitor lag and alert if exceeds threshold (e.g., >1 second). (3) Use timestamp tracking—client records write timestamp, subsequent reads wait until follower timestamp >= write timestamp. (4) Monotonic reads—client always reads from same replica (avoids seeing old data after seeing new data). (5) Consistent prefix reads—if A causes B, and B causes C, reads must see A before B before C. Example: user posts a comment (leader applied immediately); friend refreshes feed reading from slow follower (sees stale list without comment); waits 2 seconds for follower to catch up; now sees comment. For low-lag systems, these workarounds are unnecessary; for high-lag systems (multi-datacenter), they're critical. The trade-off: eventual consistency is faster but requires application-level handling.

**Key points for full marks:** (1) Definition: delay between leader write and follower application (2) Effects on data freshness (3) At least 3 solutions (read-from-leader, lag monitoring, timestamp tracking, monotonic reads, prefix) (4) Real-world example (5) Trade-off summary

---

**Q18. Explain the 2PC (Two-Phase Commit) protocol in detail, including both phases and why it is blocking.**

**Answer:**
2PC coordinates atomic transactions across multiple sites (repositories). Phase 1 (Prepare/Vote): The coordinator writes "Prepare T" to disk, sends to all sites; each site writes "Ready T" (if prepared) or "Don't Commit T" (if not) to disk, flushes, and sends response; sites enter pre-committed state (holding locks, cannot self-abort). Phase 2 (Decision): If ALL sites respond "Ready T", coordinator writes and sends "Commit T"; each site commits, releases locks, logs, and sends "Done". If ANY site responds "Don't Commit T", coordinator writes and sends "Abort T"; all sites rollback, release locks, log, and send "Done". Critical: coordinator must log ALL messages before sending (ensuring recovery capability on crash). 2PC is BLOCKING because if the coordinator crashes after Phase 1, sites remain in pre-committed state holding locks indefinitely, blocking other transactions. Recovery: coordinator recovers, re-reads logs, and either re-send "Commit T" or "Abort T" based on whether Phase 2 was completed. Example: bank transfer (account A → account B) uses 2PC to ensure both decrease and increase happen or neither happens. If coordinator fails mid-transfer, site locks are held until coordinator recovers.

**Key points for full marks:** (1) Phase 1: Prepare and site responses (2) Phase 2: Coordinator decision and site actions (3) Disk logging before sending (4) Blocking risk if coordinator fails (5) Recovery mechanism (6) Example transaction (7) Atomicity guarantee

---

**Q19. Explain the CAP theorem with examples of CP, AP, and CA systems and trade-off scenarios.**

**Answer:**
CAP theorem states a distributed system can guarantee at most 2 of 3 properties: Consistency (latest value), Availability (always respond), Partition Tolerance (works despite network failures). In cloud systems, network partitions are inevitable, so Partition Tolerance is non-negotiable; systems choose CP or AP. CP systems (Consistency + Partition) like ZooKeeper and HBase prioritize correctness; during partition, unavailable partitions may return errors until partition heals, ensuring no stale data is served. AP systems (Availability + Partition) like Cassandra and CouchDB prioritize availability; during partition, nodes respond with potentially stale data (eventual consistency). CA systems (Consistency + Availability) like PostgreSQL and MySQL work only in LAN without partitions. Trade-off scenarios: banking system chooses CP (money transfer must be accurate, acceptable downtime); social media chooses AP (feed staleness acceptable, downtime unacceptable); healthcare chooses CP (data accuracy critical). When no partition exists, both C and A are possible, making CAP less relevant. During actual partition: errors (CP) vs. stale data (AP). Practical choice depends on application: users tolerate temporary unavailability, or tolerate seeing old data.

**Key points for full marks:** (1) CAP definition (2) P is unavoidable (3) CP system with example (4) AP system with example (5. CA system context (6. Trade-off scenarios (7. Choice factors

---

**Q20. Differentiate between consistency models: eventual, causal, sequential, and linearizable.**

**Answer:**
1. Eventual Consistency (weakest): Processes eventually see the same data but no guarantee on "when." Example: social media like, distributed across datacenters, seen by all followers after delay.
2. PRAM (Pipelined RAM): A single process writes in order; different processes may see different orders for writes from different processes. Example: Process A writes 1 then 2; Process B might see 2 then 1, but B will always see A's writes in the same order Process A wrote them.
3. Causal Consistency: If write B is causally related to write A (B depends on A), all processes see A before B. Unrelated writes may appear in different orders. Example: comment depends on post creation; all see post before comment, but comments on different posts can appear in any order.
4. Sequential Consistency (Lamport): All processes see all writes in the same total order (though order is not necessarily real-time). Deterministic but may not match real-time causality.
5. Linearizable Consistency (strongest): Single-copy illusion; once any process reads a new value, all subsequent reads must see it or newer. Real-time causality is preserved. Most expensive to implement. Used in: single-leader (sync replicas)—potentially yes; consensus (ZooKeeper)—yes; multi-leader—no; leaderless—probably no. Trade-off: stronger consistency costs latency and availability.

**Key points for full marks:** (1) Eventual definition and example (2) PRAM/Causal definition and difference (3. Sequential (Lamport) definition (4. Linearizable with real-time ordering (5) Ordering from weakest to strongest (6. System/implementation examples (7. Trade-off (latency vs consistency)

---

## END OF DOCUMENT

**Total content: 100 MCQs with answer key + 20 four-mark questions with complete answers**

**Study Tips:**
- MCQs 1-40% cover definitions; 60% are scenario/application-based
- Group 4-mark answers by topic for focused revision
- Practice answers aloud to improve explanation clarity
- Focus on "why" not just "what" (professor values thinking)
- Review all 11 topics proportionally before exam

---
