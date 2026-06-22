# System Design Decision Framework

One of the biggest mistakes in system design interviews is trying to memorize technologies.

Instead, start with the requirement and then choose the technology.

Think:

```text
Requirement
    ↓
Tradeoffs
    ↓
Technology
```

---

# 1. Which Database Should I Choose?

## Decision Tree

```text
Is the data highly structured?
Do I need joins, transactions, consistency?

        YES
         ↓
        SQL

         NO
         ↓
       NoSQL
```

---

## SQL (PostgreSQL / MySQL)

### When To Use

* Banking Systems
* Payment Systems
* Orders
* Inventory
* User Management
* Transactions

### Why?

SQL databases provide:

* ACID Transactions
* Strong Consistency
* Joins
* Referential Integrity

Example:

```text
User
  |
Orders
  |
Payments
```

These relationships are easier in SQL.

### Pros

* Strong consistency
* Supports transactions
* Mature ecosystem
* Complex queries

### Cons

* Harder to scale horizontally
* More expensive at massive scale

### Interview Rule

```text
Money is involved?
Choose SQL first.
```

---

## Cassandra / DynamoDB

### When To Use

* TinyURL
* Social Media Timelines
* Chat Messages
* Activity Feeds
* Event Storage

### Why?

Access pattern is usually:

```text
Key → Value
```

Example:

```text
ShortCode → LongURL
```

```text
UserId → Timeline
```

No joins required.

### Pros

* Massive horizontal scaling
* High write throughput
* Multi-region support

### Cons

* No joins
* Eventual consistency

### Interview Rule

```text
Simple lookup + huge scale
Choose Cassandra/DynamoDB
```

---

## Redis

### When To Use

* Session Storage
* Rate Limiting
* Caching
* Leaderboards
* Counters

### Why?

Redis stores everything in memory.

Latency:

```text
Microseconds
```

Instead of:

```text
Milliseconds
```

### Pros

* Extremely fast
* In-memory
* Rich data structures

### Cons

* Expensive memory
* Data loss risk if not persisted

### Interview Rule

```text
Need speed?
Think Redis.
```

---

## MongoDB

### When To Use

* Product Catalogs
* CMS Systems
* Blogging Platforms
* Dynamic User Profiles

### Why?

Schema can change frequently.

Example:

Product A:

```json
{
  "name":"iPhone",
  "color":"Black"
}
```

Product B:

```json
{
  "name":"Laptop",
  "ram":"16GB",
  "gpu":"RTX"
}
```

No schema migration required.

### Pros

* Flexible schema
* Easy evolution
* Developer friendly

### Cons

* Data duplication
* Not ideal for heavy joins

### Interview Rule

```text
Flexible structure?
Choose MongoDB.
```

---

# Database Cheat Sheet

| Requirement     | Database  |
| --------------- | --------- |
| Payments        | SQL       |
| Orders          | SQL       |
| User Accounts   | SQL       |
| TinyURL         | Cassandra |
| Chat Messages   | Cassandra |
| Sessions        | Redis     |
| Rate Limiting   | Redis     |
| Product Catalog | MongoDB   |
| CMS             | MongoDB   |

---

# 2. Which Cache Strategy Should I Choose?

## Decision Tree

```text
Read Heavy?
     ↓
Cache Aside

Need Consistency?
     ↓
Write Through

Need Fast Writes?
     ↓
Write Back
```

---

## Cache Aside (Lazy Loading)

### Flow

```text
Request
   |
Cache
   |
Miss
   |
Database
   |
Update Cache
```

### Best For

* TinyURL
* Product Pages
* User Profiles
* Most Web Applications

### Pros

* Easy implementation
* Cache only useful data

### Cons

* First request is slow

### Interview Rule

```text
90% of systems use Cache Aside.
```

---

## Write Through

### Flow

```text
Write
  |
Cache
  |
Database
```

Both updated together.

### Best For

* User Settings
* User Profiles
* Configuration Data

### Pros

* Cache always fresh

### Cons

* Slower writes
* Cache stores unused data

### Interview Rule

```text
Consistency > Performance
Choose Write Through
```

---

## Write Back

### Flow

```text
Write
  |
Cache
  |
Async DB Update
```

### Best For

* Analytics
* Gaming Scores
* Metrics Systems

### Pros

* Very fast writes

### Cons

* Data loss possible

### Interview Rule

```text
Write speed matters most
Choose Write Back
```

---

# Cache Strategy Cheat Sheet

| Requirement   | Strategy      |
| ------------- | ------------- |
| TinyURL       | Cache Aside   |
| Product Page  | Cache Aside   |
| User Settings | Write Through |
| Gaming Scores | Write Back    |
| Analytics     | Write Back    |

---

# 3. Which Cache Eviction Policy?

Cache is limited.

When cache becomes full:

```text
Need to remove something.
```

Question:

```text
What should be removed?
```

---

## LRU (Least Recently Used)

### How It Works

Remove the item not used recently.

Example:

```text
A B C D
```

User accesses:

```text
A
B
```

Least recently used:

```text
C
```

Remove C.

### Best For

* TinyURL
* Product Pages
* Social Media Feeds
* Most Applications

### Pros

* Simple
* Effective

### Interview Rule

```text
Default choice = LRU
```

---

## LFU (Least Frequently Used)

### How It Works

Remove item with lowest access count.

Example:

```text
Video A → 10000 Views
Video B → 5 Views
```

Remove:

```text
Video B
```

### Best For

* Trending Videos
* Music Rankings
* Popular Content

### Interview Rule

```text
Popularity matters?
Choose LFU
```

---

## FIFO (First In First Out)

### How It Works

Remove oldest entry.

```text
A → B → C → D
```

Remove:

```text
A
```

### Best For

* Queues
* Log Processing

### Interview Rule

```text
Order matters?
Choose FIFO
```

---

# Cache Eviction Cheat Sheet

| Requirement       | Policy |
| ----------------- | ------ |
| TinyURL           | LRU    |
| Product Pages     | LRU    |
| News Feed         | LRU    |
| Trending Videos   | LFU    |
| Spotify Top Songs | LFU    |
| Queue Systems     | FIFO   |

---

# 4. CDN vs Redis

Many people confuse these.

They solve completely different problems.

---

## CDN

### Stores

```text
Images
Videos
CSS
JavaScript
HTML
```

### Purpose

Serve content closer to users.

Example:

```text
User (India)
      ↓
CDN Edge Server (India)
```

instead of:

```text
User (India)
      ↓
Origin Server (USA)
```

### Benefit

Lower latency.

---

## Redis

### Stores

```text
User Sessions
API Responses
JSON Data
Database Results
```

### Purpose

Reduce database load.

Example:

```text
User
 |
Application
 |
Redis
 |
Database
```

### Benefit

Faster data retrieval.

---

# CDN vs Redis Cheat Sheet

| Feature  | CDN                 | Redis             |
| -------- | ------------------- | ----------------- |
| Stores   | Files               | Data              |
| Examples | Images, Videos      | Sessions, JSON    |
| Location | Edge Servers        | Application Layer |
| Purpose  | Reduce user latency | Reduce DB load    |

---

# Master System Design Cheat Sheet

| Requirement            | Technology    |
| ---------------------- | ------------- |
| Money / Transactions   | SQL           |
| Orders                 | SQL           |
| User Accounts          | SQL           |
| Massive Scale + Lookup | Cassandra     |
| Chat Messages          | Cassandra     |
| Sessions               | Redis         |
| Rate Limiting          | Redis         |
| Leaderboards           | Redis         |
| Product Catalog        | MongoDB       |
| CMS                    | MongoDB       |
| Read Heavy System      | Cache Aside   |
| Consistency Important  | Write Through |
| Fast Writes            | Write Back    |
| General Purpose Cache  | LRU           |
| Popular Content        | LFU           |
| Queue Processing       | FIFO          |
| Static Files           | CDN           |
| DB Results             | Redis         |

---

# Final Interview Rules

```text
Transactions → SQL

Simple Lookup + Scale → Cassandra

Fast Temporary Data → Redis

Flexible Documents → MongoDB

Read Heavy → Cache Aside

Consistency → Write Through

Write Speed → Write Back

Default Cache Policy → LRU

Popularity Based → LFU

Static Files → CDN

Application Data → Redis
```

The goal is not to memorize technologies.

The goal is to understand the requirement and then select the correct tradeoff.
