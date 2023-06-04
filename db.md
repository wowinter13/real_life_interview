## Databases

### Table of Content

- [Databases](#databases)
  - [Common](#common)
  - [Postgresql](#postgresql)
  - [Redis](#redis)


### <a id="common"></a> Common

**Data races**

A data race is a situation that occurs when multiple threads are running the same program and 2 or more threads get access to the same shared variable, at the same time. Cases like this can be prevented using locks, so the access to the variable will be locked until one of the threads completes its operation.

**Deadlocks**

This is a situation where two or more threads are blocked permamently because they are waiting for each other.



### <a id="postgresql"></a> Postgresql

**Sharding**

Sharding is the process of splitting data into multiple physical nodes (shards) to handle increased workload.
Each shard contains only a portion of the data, and database queries are distributed among the shards. Sharding can be done horizontally (splitting data by rows) or vertically (splitting data by columns).

PostgreSQL provides the `pg_shard` extension, which enables data and query distribution across shards at the database level.

**Replication and its types**

Replication involves creating and maintaining copies of a database on different nodes (replicas), enabling load balancing and ensuring fault tolerance. 

- **Master-slave replication:** One database (master) serves as the primary source, while other databases (slaves) contain copies of the data that are regularly updated from the master. Slaves can be used for read operations, while writes are performed only on the master.

- **Logical replication:** Data is replicated based on logical changes that occur on the master. It allows more flexibility in choosing which data and tables to replicate on the slaves.

- **Streaming replication:** Synchronous replication mode – where changes are written to the transaction log and streamed in real-time to the replicas.

- **Asynchronous replication:** Changes are sent to the replicas asynchronously, without guarantees of synchronization between the master and the replicas. This can result in some temporary data lag between the master and the replicas.



**Master/Slave replication. How will the indexing work?**

Indexing will be performed on the master node. Slaves will receive copies of indexes in a real-time or with some delay. They do not perform "personal" indexing.




### <a id="redis"></a> Redis

**Data types**

- **Strings:** byte sequences and can contain any data, including text, numbers, or binary data.

- **Lists:** Lists are ordered collections of elements. Can be used to implement stacks, queues, or event logs.

- **Hashes:** They are suitable for storing structured data, such as user information or objects.

- **Sets/Sorted Sets:** Sets are unordered collections of unique elements. You can perform set operations like union, intersection, and difference.
**Sorted sets** are sets of elements, where each element is associated with a score.