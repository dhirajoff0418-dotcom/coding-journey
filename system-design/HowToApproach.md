# How To Approach Any Scalability Question

Most candidates fail system design interviews because they jump directly into technologies:

```text
Let's use Redis
Let's use Kafka
Let's use MongoDB
```

A strong engineer follows a structured process:

```text
Requirements
      ↓
Estimation
      ↓
Bottlenecks
      ↓
Architecture
      ↓
Tradeoffs
```

The interviewer is not testing technology knowledge.

The interviewer is testing how you think.

---

# Step 1: Clarify Requirements (1–2 Minutes)

Never jump directly into the solution.

Ask questions first.

---

## Questions To Ask

### Traffic

```text
How many requests per second?
```

Example:

```text
10K req/sec
100K req/sec
1M req/sec
```

---

### Read vs Write Pattern

```text
Is the system read heavy or write heavy?
```

Examples:

TinyURL

```text
Read Heavy
```

Analytics System

```text
Write Heavy
```

---

### Consistency Requirements

```text
Do we need strong consistency?
Or is eventual consistency acceptable?
```

Examples:

Bank Transfer

```text
Strong Consistency
```

Social Media Feed

```text
Eventual Consistency
```

---

### Geographic Scope

```text
India only?
Global users?
Multi-region?
```

This impacts:

* CDN
* Database Replication
* Latency

---

### Existing Infrastructure

```text
Greenfield project?
Existing system?
```

Important because:

```text
You cannot redesign everything from scratch.
```

---

### Budget Constraints

```text
Startup budget?
Enterprise budget?
```

Because:

```text
The best architecture is not always affordable.
```

---

# Why Clarification Matters

Bad Answer:

```text
I'll use Kafka, Redis and Cassandra.
```

Good Answer:

```text
Before designing,
I'd like to understand traffic,
consistency requirements,
and access patterns.
```

This immediately shows senior-level thinking.

---

# Step 2: Capacity Estimation (2 Minutes)

Before architecture:

Calculate scale.

---

## Example

Assume:

```text
10K Requests/Second
```

Daily Requests:

```text
10,000 × 86,400
=
864 Million Requests/Day
```

---

## Storage Estimation

If each request stores:

```text
1 KB
```

Then:

```text
864 Million KB

≈ 864 GB / Day
```

---

## Peak Traffic

Never design for average traffic.

Assume:

```text
Peak = 3x Average
```

Example:

```text
Average = 10K/sec

Peak = 30K/sec
```

---

## Why Estimation Matters

It helps answer:

```text
Can one database handle this?
Can one server handle this?
Do we need sharding?
```

---

# Step 3: Identify Bottlenecks

Before proposing solutions:

Ask:

```text
What will fail first?
```

---

## Single Server

Problem:

```text
CPU
Memory
Disk
Network
```

Eventually one machine cannot handle traffic.

Solution:

```text
Horizontal Scaling
```

---

## Single Database

Problem:

```text
Too many reads
Too many writes
```

Solution:

```text
Read Replicas
Sharding
Caching
```

---

## No Cache

Problem:

```text
Every request hits database
```

Result:

```text
Database overload
```

Solution:

```text
Redis Cache
```

---

## No Load Balancer

Problem:

```text
One server receives all traffic
```

Solution:

```text
Load Balancer
```

---

## Synchronous Everything

Problem:

```text
Slow response times
```

Example:

```text
User Signup
    |
Send Email
Generate Report
Upload File
```

User waits.

Solution:

```text
Queue + Background Worker
```

---

# Step 4: Solve Layer By Layer

Always move from top to bottom.

Never randomly add technologies.

---

# Layer 1: Traffic Management

---

## Load Balancer

Purpose:

```text
Distribute traffic
```

Before:

```text
Client
   |
Server
```

After:

```text
Client
   |
Load Balancer
   |
-------------
|     |     |
S1    S2    S3
```

---

## Rate Limiter

Purpose:

```text
Prevent abuse
```

Example:

```text
100 requests/minute/user
```

Returns:

```text
429 Too Many Requests
```

---

## CDN

Purpose:

```text
Serve static files
```

Stores:

```text
Images
Videos
CSS
JS
```

Reduces server load.

---

# Layer 2: Application Layer

---

## Horizontal Scaling

Before:

```text
1 Server
```

After:

```text
10 Servers
```

Traffic distributed via Load Balancer.

---

## Stateless Servers

Bad:

```text
Session stored in server memory
```

If request hits another server:

```text
Session lost
```

---

Good:

```text
Session stored in Redis
```

Any server can serve request.

---

## Auto Scaling

Traffic increases:

```text
10K/sec
→
50K/sec
```

Automatically create more servers.

Traffic decreases:

```text
Remove extra servers
```

---

# Layer 3: Caching Layer

---

## Redis Cache

Purpose:

```text
Reduce DB load
```

---

### Cache Aside

Flow:

```text
Request
   |
Redis
   |
Miss
   |
Database
   |
Update Cache
```

Most common strategy.

---

### Cache Eviction

Usually:

```text
LRU
```

Because:

```text
Recently used data
is likely to be used again.
```

---

# Layer 4: Database Layer

---

## Read Replicas

Before:

```text
All Reads + Writes
      |
   Primary DB
```

After:

```text
              Read Replica
             /
Primary DB
             \
              Read Replica
```

---

### Benefit

```text
Read traffic distributed
```

---

### Tradeoff

```text
Replication Lag
```

Eventual consistency.

---

## Database Indexing

Without Index:

```text
Full Table Scan
```

With Index:

```text
Direct Lookup
```

Query becomes much faster.

---

## Connection Pooling

Problem:

```text
10K users
10K DB connections
```

Database struggles.

Solution:

```text
Reuse connections
```

---

## Sharding

When one database is not enough.

Split data.

Example:

```text
User 1 - 1M
→ Shard 1

User 1M - 2M
→ Shard 2
```

---

# Layer 5: Asynchronous Processing

Some tasks do not need immediate execution.

---

## Queue

Examples:

```text
Send Email
Generate Reports
Thumbnail Creation
Analytics Events
```

---

## Architecture

```text
User
 |
API
 |
Kafka
 |
Workers
 |
Task Completed
```

---

## Benefits

* Faster API response
* Better scalability
* Better reliability

---

# Step 5: Discuss Tradeoffs

This separates junior from senior engineers.

Never present a solution without discussing tradeoffs.

---

## Read Replica Tradeoff

Benefit:

```text
Scales reads
```

Cost:

```text
Replication delay
```

---

## Cache Aside Tradeoff

Benefit:

```text
Reduces DB load
```

Cost:

```text
Cold Start
```

First request is slow.

---

## Sharding Tradeoff

Benefit:

```text
Massive scalability
```

Cost:

```text
Complex joins
Cross shard queries
```

---

## Queue Tradeoff

Benefit:

```text
Fast API response
```

Cost:

```text
Eventual processing
```

Not instant.

---

# Step 6: Present Final Architecture

After solving layer by layer:

Present the complete system.

---

## Generic Scalable Architecture

```text
                +--------+
                | Client |
                +--------+
                     |
                     v
              +-------------+
              |     CDN     |
              +-------------+
                     |
                     v
            +------------------+
            |   Rate Limiter   |
            +------------------+
                     |
                     v
            +------------------+
            |  Load Balancer   |
            +------------------+
                     |
      ------------------------------------
      |                |                |
      v                v                v
 +---------+     +---------+     +---------+
 | App S1  |     | App S2  |     | App S3  |
 +---------+     +---------+     +---------+
      |
      v
 +---------+
 | Redis   |
 +---------+
      |
      |
 -------------------------
 |                       |
 v                       v
Read Replica       Primary DB
(Read)             (Writes)
 |
 v
 Kafka / RabbitMQ
 |
 Background Workers
```

---

# Universal Scalability Answer Template

Whenever interviewer asks:

```text
How would you scale this system?
```

Use:

### 1. Clarify Requirements

Traffic?
Read/Write Ratio?
Consistency?
Geography?

### 2. Estimate Scale

Requests/sec
Storage/day
Peak Traffic

### 3. Identify Bottlenecks

Server
Database
Cache
Network

### 4. Scale Layer By Layer

Load Balancer
CDN
Redis
Read Replicas
Sharding
Queues

### 5. Discuss Tradeoffs

Consistency
Latency
Complexity
Cost

### 6. Present Final Architecture

Complete end-to-end flow.

---

# Key Takeaways

* Don't start with technologies.
* Start with requirements.
* Estimate before designing.
* Identify bottlenecks first.
* Scale one layer at a time.
* Always discuss tradeoffs.
* End with a complete architecture diagram.

This framework works for almost every system design question:

* TinyURL
* WhatsApp
* Instagram
* Netflix
* Uber
* Food Delivery
* Payment Systems
* Notification Systems
* E-commerce Platforms
