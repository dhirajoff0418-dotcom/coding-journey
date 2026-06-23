# Database Sharding - Interview Notes

## What is Sharding?

Sharding is a horizontal scaling technique where a large database is split into smaller databases called shards.

Each shard contains only a subset of the data and is usually hosted on a separate database server.

### Before Sharding

```text
                Application
                      |
                      |
                +-----------+
                | Database  |
                +-----------+
```

Problems:

* CPU bottleneck
* RAM bottleneck
* Storage bottleneck
* Single point of failure
* Limited vertical scaling

---

## After Sharding

```text
                Application
                      |
        +-------------+-------------+
        |             |             |
        |             |             |
    +-------+     +-------+     +-------+
    |Shard 1|     |Shard 2|     |Shard 3|
    +-------+     +-------+     +-------+
```

Benefits:

* Horizontal scalability
* Higher write throughput
* Better storage distribution
* Better fault isolation

---

# Sharding vs Replication

## Replication

Every replica contains the same data.

```text
          Primary DB
               |
      +--------+--------+
      |                 |
 Replica 1         Replica 2
```

Used for:

* Read scaling
* High availability
* Disaster recovery

---

## Sharding

Each shard contains different data.

```text
Shard 1 -> Users 1-1M
Shard 2 -> Users 1M-2M
Shard 3 -> Users 2M-3M
```

Used for:

* Write scaling
* Storage scaling
* Massive datasets

---

# Sharding vs Partitioning

Partitioning is a broader concept.

Partitioning may exist inside a single database server.

```text
Server A

Partition 1
Partition 2
Partition 3
```

Sharding is a form of horizontal partitioning where partitions are placed on different servers.

```text
Server A -> Partition 1
Server B -> Partition 2
Server C -> Partition 3
```

---

# Types of Sharding

## 1. Range Based Sharding

```text
Shard 1 -> UserId 1 - 1M
Shard 2 -> UserId 1M - 2M
Shard 3 -> UserId 2M - 3M
```

Advantages:

* Easy to understand
* Efficient range queries

Disadvantages:

* Hot shard problem
* Uneven distribution

---

## 2. Hash Based Sharding

```text
Shard = UserId % N
```

Example:

```text
User 10 -> 10 % 4 = 2
User 11 -> 11 % 4 = 3
User 12 -> 12 % 4 = 0
```

Advantages:

* Even distribution
* Reduces hot shards

Disadvantages:

* Difficult range queries
* Resharding problem

---

## 3. Directory Based Sharding

A lookup table stores shard location.

```text
User 101 -> Shard A
User 102 -> Shard B
User 103 -> Shard C
```

Advantages:

* Flexible

Disadvantages:

* Requires routing service
* Extra lookup

---

# Good Shard Key

A good shard key should have:

## 1. High Cardinality

Bad:

```text
Country
```

Good:

```text
UserId
```

---

## 2. Even Distribution

Traffic should spread evenly.

Bad:

```text
Country
```

If 80% users are from India:

```text
India -> Hot Shard
```

---

## 3. Query Locality

Most queries should hit only one shard.

Good:

```sql
Get Notifications By CustomerId
```

Bad:

```sql
Find All Delhi Users
```

May require scanning all shards.

---

# Hot Shard Problem

Example:

```text
Shard 1 -> 10,000 RPS
Shard 2 -> 500 RPS
Shard 3 -> 400 RPS
```

One shard receives most traffic.

Problems:

* High latency
* CPU spikes
* Database failures

Solutions:

* Better shard key
* Caching
* Read replicas
* Resharding
* Consistent hashing

---

# Data Skew

Example:

```text
Customer 1 -> 10 Million Transactions

Other Customers -> 100 Transactions
```

Result:

```text
Shard A -> Huge
Shard B -> Small
Shard C -> Small
```

Problems:

* Uneven storage
* Uneven traffic

Solutions:

* Composite shard key
* Sub-sharding
* Dedicated shard

---

# Resharding Problem

Suppose:

```text
Shard = UserId % 4
```

User:

```text
11 % 4 = 3
```

User 11 stored in Shard 3.

Later:

```text
Shard = UserId % 5
```

Now:

```text
11 % 5 = 1
```

User suddenly belongs to another shard.

Problem:

* Massive data migration
* Routing changes
* Downtime risk

---

# Consistent Hashing

Purpose:

Minimize data movement when shards are added or removed.

Normal Hashing:

```text
UserId % N
```

Problem:

Almost all users move after adding a shard.

---

## Ring Concept

```text
           Shard A
               |
               |
Shard C -------+------- Shard B
               |
               |
```

Users are hashed onto the ring.

Rule:

Move clockwise and store in the first shard encountered.

---

## Adding New Shard

Before:

```text
A ---------- B ---------- C
```

After:

```text
A ----- D ---- B -------- C
```

Only nearby keys move.

Benefits:

* Minimal migration
* Easier scaling

---

# Cross-Shard Join Problem

Single Database:

```sql
SELECT *
FROM Users U
JOIN Orders O
ON U.Id = O.UserId
```

Easy.

---

Sharded Environment:

```text
Users  -> Shard A
Orders -> Shard B
```

Now application must:

1. Query User shard
2. Query Order shard
3. Merge results

Problems:

* Network calls
* Higher latency
* More complexity

---

# Scatter Gather Query

Query:

```sql
Find all users from Delhi
```

With Hash(UserId) sharding:

```text
Delhi Users
    |
    +--> Shard 1
    +--> Shard 2
    +--> Shard 3
    +--> Shard 4
```

Process:

```text
Send Query To All Shards
          |
          v
Collect Results
          |
          v
Merge Results
```

Problems:

* Expensive
* Slow

---

# Sharding + Replication

```text
              Shard A
             /       \
        Replica1   Replica2


              Shard B
             /       \
        Replica1   Replica2
```

Sharding solves:

* Write scalability
* Storage scalability

Replication solves:

* Read scalability
* High availability

---

# Distributed Transactions Problem

Example:

```text
Order Service     -> DB A
Payment Service   -> DB B
Inventory Service -> DB C
```

A single SQL transaction cannot span multiple shards easily.

---

# Two Phase Commit (2PC)

Flow:

```text
Coordinator
      |
      |
 Prepare
      |
      v
All Databases

      |
      |
 Commit
      |
      v
All Databases
```

Advantages:

* Strong consistency

Disadvantages:

* Slow
* Blocking
* Hard to scale

---

# Saga Pattern

A business transaction is split into multiple local transactions.

Example:

```text
Create Order
      |
Reserve Inventory
      |
Take Payment
```

If payment fails:

```text
Release Inventory
      |
Cancel Order
```

These are called compensating transactions.

---

# Interview Favorites

## What is a Hot Shard?

One shard receives significantly more traffic than others.

---

## What is Data Skew?

Data distribution is uneven across shards.

---

## What is Query Locality?

Most queries should hit a single shard.

---

## Why is Consistent Hashing used?

To reduce data movement when adding or removing shards.

---

## Why not always use Hash Sharding?

Range queries and filtering become difficult.

---

## When to use Replication?

Read-heavy systems.

---

## When to use Sharding?

Write-heavy systems and huge datasets.

---

# One Line Interview Definitions

Sharding:
Splitting data across multiple databases to achieve horizontal scalability.

Replication:
Copying the same data to multiple databases for read scalability and availability.

Hot Shard:
A shard receiving disproportionately high traffic.

Data Skew:
Uneven distribution of data among shards.

Consistent Hashing:
A technique that minimizes key movement when shards are added or removed.

Saga Pattern:
A distributed transaction pattern using compensating actions to handle failures.

Scatter-Gather:
Querying all shards and merging the results.