# Advance Database Technology

A tutorial series on database performance, covering **Oracle** and **PostgreSQL** side by side. Each chapter explains the small set of ideas that solve most real-world problems, with concrete SQL, annotated execution plans and text diagrams.

Most chapters use one sample schema (customers, orders, order items and products, with deliberately skewed data), so examples build on each other from chapter to chapter.

Select a chapter title to open it.

---

## Query and Index Tuning

### [Chapter 01: Query Optimisation](01.Query%20Optimisation.md)

How the optimizer turns SQL into an execution plan, and why most slow queries come from wrong row estimates. Covers cost-based optimisation, reading plans with `EXPLAIN` and `DBMS_XPLAN`, join methods, subqueries, CTEs, query rewriting, statistics and histograms.

### [Chapter 02: Advanced Indexing](02.Advanced%20Indexing.md)

How indexes work, which type to use, and what each one costs on writes. Covers B-tree and hash indexes, selectivity, composite and covering indexes, index-only scans, expression, partial and invisible indexes, clustering, and index maintenance.

### [Chapter 03: Join Optimisation](03.Join%20Optimisation.md)

How the database executes joins and how to recognise a poor choice. Covers nested loop, hash and merge joins, semi- and anti-joins, join order, join elimination, and large-table joins under different data distributions.

### [Chapter 13: Read Optimisation](13.Read%20Optimisation.md)

How the optimizer chooses between a full scan, an index scan and an index-only scan, with worked cost numbers. Explains why an index can lose even at 1% of rows, how `ORDER BY` and `LIMIT` change the choice, and what to do when the optimizer chooses wrongly.

### [Chapter 16: Query Anti-Patterns](16.Query%20Anti-Patterns.md)

Query patterns that return correct results but get slower as data grows. Covers `SELECT *`, functions on indexed columns, leading-wildcard `LIKE`, N+1 queries, sorting on unindexed columns, very large `IN` lists and unnecessary correlated subqueries.

---

## Concurrency and Storage

### [Chapter 04: Transactions and Concurrency](04.Transactions%20and%20Concurrency.md)

How the database keeps concurrent work correct and who waits for whom. Covers ACID, MVCC, read phenomena, isolation levels, row and table locks, deadlocks, optimistic and pessimistic concurrency, serializable transactions and contention.

### [Chapter 05: Database Storage Internals](05.Database%20Storage%20Internals.md)

What happens physically when data is read or changed. Covers pages and blocks, the buffer cache, sequential and random I/O, write-ahead logging, checkpoints, redo and undo, heap tables, row and column storage, compression and cache locality.

### [Chapter 14: Schema-Level Optimisation](14.Schema-Level%20Optimisation.md)

Schema decisions that affect every query and are hard to change later. Covers normalisation, data types, primary key design (surrogate vs natural keys, UUID vs sequential IDs), foreign key indexes, table width, JSON columns, the EAV anti-pattern and temporal data.

---

## Organising and Precomputing Data

### [Chapter 06: Partitioning](06.Partitioning.md)

Splitting one large table into partitions that applications still see as a single table. Covers range, list and hash partitioning, partition pruning, partition-wise joins, partition maintenance and choosing a partition key.

### [Chapter 07: Materialised Data](07.Materialised%20Data.md)

Computing expensive results once and reading the stored copy. Covers materialised views and how they are refreshed, precomputed aggregates, summary tables, denormalisation and CQRS read models.

### [Chapter 08: Caching](08.Caching.md)

Keeping copies of data somewhere faster than the database, without serving wrong data. Covers the database buffer cache, result caches, application caching, Redis, cache-aside, read-through, write-through and write-behind, invalidation and stampede prevention.

### [Chapter 15: Advanced Aggregation](15.Advanced%20Aggregation.md)

How `GROUP BY` and window functions are executed, and what they cost on large tables. Covers hash and sort aggregation, window function costs, and incremental, precomputed and approximate aggregation.

---

## Scaling Beyond One Server

### [Chapter 09: Replication and Read Scaling](09.Replication%20and%20Read%20Scaling.md)

Keeping copies of a database to survive failures and share read load. Covers primary/replica architecture, building and using read replicas, synchronous and asynchronous replication, replication lag, read-after-write consistency, failover and quorum-based systems.

### [Chapter 10: Database Sharding](10.Database%20Sharding.md)

Splitting data across independent servers when one primary is no longer enough. Covers horizontal sharding and vertical partitioning, choosing a shard key, range and hash sharding with consistent hashing, cross-shard queries and transactions, rebalancing and hot partitions.

### [Chapter 11: Distributed Database Concepts](11.Distributed%20Database%20Concepts.md)

The ideas that explain how data behaves across several machines. Covers the CAP theorem, consistency models, distributed transactions, two-phase commit, consensus, quorum reads and writes, leader/follower designs and distributed locks.

---

## Workload and Application Optimisation

### [Chapter 12: Write Optimisation](12.Write%20Optimisation.md)

Removing the per-row costs that make writes slow. Covers batch inserts, bulk loading, upserts, transaction batching, WAL and redo optimisation, group commit, reducing indexes and foreign-key checks, and asynchronous writes.

### [Chapter 17: Connection Optimisation](17.Connection%20Optimisation.md)

Why a well-tuned database can still perform badly because of how the application connects to it. Covers connection pooling, pool sizing, connection lifetime, idle connections, prepared statements, network round trips, transaction duration and connection saturation.

---

## Measuring and Diagnosing

### [Chapter 18: Observability and Performance Analysis](18.Observability%20and%20Performance%20Analysis.md)

Finding where time goes before changing anything. Covers execution plans, statement statistics, slow-statement capture, activity history (ASH, AWR, `pg_stat_activity`), and the key metrics: latency, throughput, CPU, memory, I/O, cache hit ratio, locks and connections.

### [Chapter 19: Optimisation at Different Layers](19.Optimisation%20at%20Different%20Layers.md)

One slow page traced down every layer, from the application and ORM through the optimizer, indexes and cache to storage and disk. Shows where each kind of problem arises and how to diagnose from the top down, and gives a recommended order for learning the subject.

---

## Chapter List

| No. | Chapter |
|-----|---------|
| 01 | [Query Optimisation](01.Query%20Optimisation.md) |
| 02 | [Advanced Indexing](02.Advanced%20Indexing.md) |
| 03 | [Join Optimisation](03.Join%20Optimisation.md) |
| 04 | [Transactions and Concurrency](04.Transactions%20and%20Concurrency.md) |
| 05 | [Database Storage Internals](05.Database%20Storage%20Internals.md) |
| 06 | [Partitioning](06.Partitioning.md) |
| 07 | [Materialised Data](07.Materialised%20Data.md) |
| 08 | [Caching](08.Caching.md) |
| 09 | [Replication and Read Scaling](09.Replication%20and%20Read%20Scaling.md) |
| 10 | [Database Sharding](10.Database%20Sharding.md) |
| 11 | [Distributed Database Concepts](11.Distributed%20Database%20Concepts.md) |
| 12 | [Write Optimisation](12.Write%20Optimisation.md) |
| 13 | [Read Optimisation](13.Read%20Optimisation.md) |
| 14 | [Schema-Level Optimisation](14.Schema-Level%20Optimisation.md) |
| 15 | [Advanced Aggregation](15.Advanced%20Aggregation.md) |
| 16 | [Query Anti-Patterns](16.Query%20Anti-Patterns.md) |
| 17 | [Connection Optimisation](17.Connection%20Optimisation.md) |
| 18 | [Observability and Performance Analysis](18.Observability%20and%20Performance%20Analysis.md) |
| 19 | [Optimisation at Different Layers](19.Optimisation%20at%20Different%20Layers.md) |
