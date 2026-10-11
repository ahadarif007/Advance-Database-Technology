# Advanced Database Technology

A technical guide to database performance and design, using **Oracle Database** and **PostgreSQL** as the two reference systems. It covers query planning, indexing, joins, schema design, storage, concurrency, write and connection handling, observability, and the techniques used when one server is no longer enough: partitioning, caching, replication, sharding and distributed design.

Each chapter explains the mechanism behind a performance behaviour, shows it in SQL and execution plans, and uses text diagrams where a picture is clearer than prose. By the end, readers should be able to read and compare execution plans, explain why the optimizer chose a plan, design indexes and schemas for a given workload, diagnose blocking, write and connection problems, and weigh the trade-offs of each scaling option.

---

## Who This Guide Is For

Developers, backend engineers and database administrators who already write SQL and want to understand why the same query is fast in one situation and slow in another.

**Assumed knowledge:** `SELECT` with joins and `GROUP BY`, basic transactions (`COMMIT`, `ROLLBACK`), and what an index is. No knowledge of database internals is assumed.

---

## How Oracle and PostgreSQL Are Compared

- Each topic introduces the **shared concept** first, then how each system implements it. The two systems often reach the same goal by different means, and the chapters point out where behaviour differs.
- Where only one system offers a feature, for example invisible indexes in Oracle or hash indexes in PostgreSQL, the chapter says so and shows the usual alternative in the other system instead of implying an equivalent.
- Code blocks and plan outputs are labelled **Oracle** or **PostgreSQL** whenever syntax differs. Each system's own terms are used: blocks and redo in Oracle, pages and WAL in PostgreSQL, full table scan and sequential scan.
- The guide uses British spelling, with the established technical term **optimizer** throughout. PostgreSQL's documentation calls the same component the *planner*. Command names, parameters and product names keep their official spelling.

---

## Examples, Versions and Limitations

**Sample schema.** Most chapters share one schema (customers, orders, order items and products) with **intentionally skewed data distributions**: 80% of customers in one country, 1% of orders cancelled. The skew is there to demonstrate cardinality estimation, selectivity, and access paths and join methods that change with the data.

**Versions.** Examples target Oracle Database 19c and later and PostgreSQL 14 and later. Features introduced in later releases (Oracle 21c and 23ai; PostgreSQL 15 to 18) are marked with the version that added them.

**Execution plans.** Plans and timings are representative, edited for readability. They are not captured output to be reproduced exactly. Real plans differ with database version, statistics, configuration, hardware and data.

**Licensing.** Several Oracle features discussed (Partitioning, Active Data Guard, the Diagnostics and Tuning Packs, Advanced Compression, Database In-Memory) are separately licensed options of Enterprise Edition. The chapters note this where it applies.

**Applying the advice.** Performance depends on workload, data distribution, hardware, configuration, database version and operational requirements. The chapters explain cause and effect so that readers can judge when a technique applies. They are not universal tuning prescriptions. Test changes against a representative workload before applying them in production. Treat changes to constraints, durability settings, partitioning, caching and replication as operational decisions with correctness and recovery consequences.

---

## Reading Order

Chapter numbers reflect the order in which the chapters were written. The parts below group them by topic, and **this is the recommended reading order**. Each part builds on the ones before it.

| Part | Chapters |
|------|----------|
| 1. Plans and Measurement | 01, 18 |
| 2. Access Paths, Joins and Schema | 02, 13, 03, 16, 14 |
| 3. Concurrency, Storage and Writes | 04, 05, 12, 17 |
| 4. Organising and Precomputing Data | 06, 07, 15, 08 |
| 5. Scaling Beyond One Server | 09, 10, 11 |
| 6. Putting It Together | 19 |

Chapter 18 refers to topics covered later. On a first reading, Sections 1 to 7 are enough; the metric sections make most sense after Part 3.

---

## Part 1: Plans and Measurement

### [Chapter 01: Query Optimisation](01.Query%20Optimisation.md)

The same SQL can run in milliseconds or minutes depending on the plan the optimizer chooses. This chapter shows how plans are built and displayed: `EXPLAIN` in PostgreSQL, `EXPLAIN PLAN` and `DBMS_XPLAN` in Oracle. It separates estimated plans from plans with runtime statistics, and explains how cardinality estimation, statistics and histograms drive the optimizer's choices. Subqueries, CTEs and query rewriting complete the picture.

### [Chapter 18: Observability and Performance Analysis](18.Observability%20and%20Performance%20Analysis.md)

Measure before changing anything. Statement statistics, statement logging and `auto_explain` show which statements cost the most. PostgreSQL's `pg_stat_activity` shows current activity, while Oracle keeps sampled history in Active Session History and the AWR repository. The chapter then works through latency, throughput, CPU, memory pressure, I/O, lock contention and connection saturation, and explains why a cache hit ratio says little on its own.

---

## Part 2: Access Paths, Joins and Schema

### [Chapter 02: Advanced Indexing](02.Advanced%20Indexing.md)

How B-tree indexes are organised, and what each index design costs in storage, write overhead, caching and maintenance. Composite column order, covering indexes, expression and partial indexes, uniqueness and physical clustering are explained in turn. Where one system lacks a feature (PostgreSQL has no invisible indexes, Oracle has no hash or partial indexes), the chapter shows the usual alternative.

### [Chapter 13: Read Optimisation](13.Read%20Optimisation.md)

Why the optimizer picks a full scan, an index scan, a bitmap or batched scan, or an index-only scan. Worked cost calculations show that how many blocks the matching rows occupy, not only how many rows match, decides the outcome. The chapter also covers how `ORDER BY` with a row limit (`LIMIT` in PostgreSQL, `FETCH FIRST` in both) changes the choice, and how to investigate an unexpected plan.

### [Chapter 03: Join Optimisation](03.Join%20Optimisation.md)

The detailed treatment of nested-loop, hash and merge joins: how each executes, what it costs, and which input sizes and data distributions favour it. It also explains the semi-joins and anti-joins the optimizer derives from `EXISTS` and `NOT EXISTS`, the search for a join order, join elimination, and joins between large tables.

### [Chapter 16: Query Anti-Patterns](16.Query%20Anti-Patterns.md)

Query shapes that return correct results but whose cost grows with data volume or request rate: N+1 queries, functions on indexed columns, leading-wildcard searches and avoidable large sorts. It also sets out the conditions under which `SELECT *`, long `IN` lists and correlated subqueries become a problem, since none of them is wrong in every case.

### [Chapter 14: Schema-Level Optimisation](14.Schema-Level%20Optimisation.md)

Design decisions that become expensive to change once data and applications depend on them. The chapter covers normalisation, data types, primary key design, surrogate and natural keys, sequential IDs and UUIDs, foreign key indexes, table width, JSON columns, entity–attribute–value designs and temporal data.

---

## Part 3: Concurrency, Storage and Writes

### [Chapter 04: Transactions and Concurrency](04.Transactions%20and%20Concurrency.md)

How concurrent transactions are kept correct, and where one blocks another. It describes dirty, non-repeatable and phantom reads, lost updates and write skew, and how isolation levels differ between the two systems. MVCC is explained separately for each, since Oracle keeps old row versions in undo and PostgreSQL keeps them in the table. Locks, deadlocks and serializable execution follow.

### [Chapter 05: Database Storage Internals](05.Database%20Storage%20Internals.md)

How rows are stored in blocks (Oracle) or pages (PostgreSQL), cached, read and modified, and how that turns into memory and disk activity. Topics include buffer caches, sequential and random I/O, PostgreSQL's WAL against Oracle's redo and undo, checkpoints, heap tables, compression and cache locality. Standard tables in both systems are row-oriented, so column storage is covered through Oracle Database In-Memory and PostgreSQL extensions.

### [Chapter 12: Write Optimisation](12.Write%20Optimisation.md)

Where the time in a write goes (round trips, constraint checks, index maintenance, log flushes) and how batch inserts, transaction batching, bulk loading, upserts and group commit reduce it. PostgreSQL's `INSERT ... ON CONFLICT` and Oracle's `MERGE` are compared, including how they differ under concurrency. Asynchronous commit and constraint changes during bulk loads are covered together with their durability and correctness risks.

### [Chapter 17: Connection Optimisation](17.Connection%20Optimisation.md)

How the application's handling of connections affects the database. The chapter covers connection pooling, and sizing pools from measured workload rather than a fixed formula. It also covers connection lifetime, prepared statements and plan caching in each system, network round trips, and long transactions that hold locks and delay cleanup.

---

## Part 4: Organising and Precomputing Data

### [Chapter 06: Partitioning](06.Partitioning.md)

Range, list, hash and composite partitioning, and when they help. The benefits come from partition pruning, partition-wise joins, bulk loading, data retention and per-partition maintenance, and only when the partition key matches how the data is queried and managed. Poorly chosen partitioning adds planning and operational overhead, and the chapter covers that too.

### [Chapter 07: Materialised Views and Precomputed Data](07.Materialised%20Views%20and%20Precomputed%20Data.md)

Storing a result instead of recomputing it. The chapter covers materialised views and their refresh strategies: Oracle offers fast refresh and query rewrite, while core PostgreSQL offers complete refresh only. Summary tables and denormalised columns follow. CQRS read models are discussed as an application-level architecture built on the same idea.

### [Chapter 15: Advanced Aggregation](15.Advanced%20Aggregation.md)

How the cost of aggregation changes with input size, number of groups, sorting and available memory. It compares hash and sort aggregation, the execution cost of window functions, and the mergeable state that makes incremental aggregation possible. Approximate distinct counts and percentiles trade a small loss of precision for large savings in resources. Precomputed aggregates are covered in Chapter 07.

### [Chapter 08: Caching](08.Caching.md)

Reducing repeated work while keeping staleness, invalidation and failure behaviour under control. The chapter separates the database buffer cache and Oracle's result cache from application-level caches, using Redis as an example of an external cache. It covers cache-aside, read-through, write-through and write-behind, the trade-offs between invalidation strategies, and cache stampedes, where many requests try to rebuild the same expired entry at once.

---

## Part 5: Scaling Beyond One Server

### [Chapter 09: Replication and Read Scaling](09.Replication%20and%20Read%20Scaling.md)

Physical and logical replication, read replicas, synchronous and asynchronous commit, replication lag and read-after-write consistency. Failover is shown with Oracle Data Guard and with PostgreSQL tools such as Patroni. The chapter also explains why replication complements backups rather than replacing them: it copies mistakes as faithfully as correct changes.

### [Chapter 10: Database Sharding](10.Database%20Sharding.md)

Distributing rows across independent databases for capacity, write throughput, geographic placement, or tenant and fault isolation, and the complexity this adds. The chapter covers choosing a shard key, range and hash placement, consistent hashing, cross-shard queries and transactions, rebalancing and hot partitions. Vertical partitioning is treated as a separate technique, usually tried first.

### [Chapter 11: Distributed Database Concepts](11.Distributed%20Database%20Concepts.md)

Consistency, coordination and failure handling across nodes. The CAP theorem is presented as a statement about behaviour during network partitions, followed by consistency models and two-phase commit, including how it differs from consensus. Quorum reads and writes, leader/follower designs, and distributed locks with leases and fencing tokens complete the chapter.

---

## Part 6: Putting It Together

### [Chapter 19: End-to-End Performance Diagnosis](19.End-to-End%20Performance%20Diagnosis.md)

One slow application request traced through every layer: application, ORM, connection pool, transaction, SQL statement, execution plan, indexes, cache and storage. At each layer the chapter shows what evidence identifies the cause and which earlier chapter covers the fix.

---

## Chapters by Number

For lookup by chapter number:

| No. | Chapter | Part |
|-----|---------|------|
| 01 | [Query Optimisation](01.Query%20Optimisation.md) | 1 |
| 02 | [Advanced Indexing](02.Advanced%20Indexing.md) | 2 |
| 03 | [Join Optimisation](03.Join%20Optimisation.md) | 2 |
| 04 | [Transactions and Concurrency](04.Transactions%20and%20Concurrency.md) | 3 |
| 05 | [Database Storage Internals](05.Database%20Storage%20Internals.md) | 3 |
| 06 | [Partitioning](06.Partitioning.md) | 4 |
| 07 | [Materialised Views and Precomputed Data](07.Materialised%20Views%20and%20Precomputed%20Data.md) | 4 |
| 08 | [Caching](08.Caching.md) | 4 |
| 09 | [Replication and Read Scaling](09.Replication%20and%20Read%20Scaling.md) | 5 |
| 10 | [Database Sharding](10.Database%20Sharding.md) | 5 |
| 11 | [Distributed Database Concepts](11.Distributed%20Database%20Concepts.md) | 5 |
| 12 | [Write Optimisation](12.Write%20Optimisation.md) | 3 |
| 13 | [Read Optimisation](13.Read%20Optimisation.md) | 2 |
| 14 | [Schema-Level Optimisation](14.Schema-Level%20Optimisation.md) | 2 |
| 15 | [Advanced Aggregation](15.Advanced%20Aggregation.md) | 4 |
| 16 | [Query Anti-Patterns](16.Query%20Anti-Patterns.md) | 2 |
| 17 | [Connection Optimisation](17.Connection%20Optimisation.md) | 3 |
| 18 | [Observability and Performance Analysis](18.Observability%20and%20Performance%20Analysis.md) | 1 |
| 19 | [End-to-End Performance Diagnosis](19.End-to-End%20Performance%20Diagnosis.md) | 6 |
