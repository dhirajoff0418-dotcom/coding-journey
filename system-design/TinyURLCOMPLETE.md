# TinyURL System Design

## What Problem Does It Solve?

TinyURL converts a long URL into a short, easy-to-share URL.

Example:

Long URL:

```text
https://www.example.com/products/electronics/mobile/apple/iphone-18-pro-max
```

Short URL:

```text
https://tinyurl.com/abc123
```

Benefits:

* Easier sharing
* Better user experience
* Reduced URL length
* Click tracking and analytics

---

# Functional Requirements

### Core Features

* Create short URL from long URL
* Redirect short URL to original URL
* Support billions of URLs
* Custom alias support
* Expiry support
* Analytics support

---

# Non Functional Requirements

### Availability

Redirect service should always be available.

### Low Latency

Redirection should happen within milliseconds.

### Scalability

System should support billions of URLs.

### Durability

URLs should never be lost.

---

# Capacity Estimation

Assume:

* 100 Million URLs created per day
* Read : Write = 100 : 1

Writes:

```text
100 Million/day
```

Reads:

```text
10 Billion/day
```

This means:

```text
Read Heavy System
```

---

# High Level Architecture

```text
                  +-------------+
                  |    Client   |
                  +-------------+
                         |
                         v
                  +-------------+
                  | LoadBalancer|
                  +-------------+
                         |
          -------------------------------
          |                             |
          v                             v
   +-------------+              +-------------+
   | URL Service |              | URL Service |
   +-------------+              +-------------+
          |
          |
     +---------+
     | Redis   |
     +---------+
          |
          |
     +---------+
     |  DB     |
     +---------+
```

---

# Database Design

## URL Table

| Column       | Type     |
| ------------ | -------- |
| id           | bigint   |
| short_code   | varchar  |
| original_url | text     |
| created_at   | datetime |
| expiry_at    | datetime |
| user_id      | bigint   |

---

# How Short Codes Are Generated

## Option 1: Auto Increment + Base62

DB ID:

```text
125
```

Base62:

```text
cb
```

Characters:

```text
a-z
A-Z
0-9
```

Total:

```text
62 characters
```

Advantages:

* Small URL size
* Simple implementation

---

## Option 2: Hashing

```text
MD5
SHA256
```

Not preferred because collisions must be handled.

---

# Happy Path

## URL Creation Flow

```text
Client
  |
POST /shorten
  |
Load Balancer
  |
URL Service
  |
Generate ID
  |
Base62 Encode
  |
Store in DB
  |
Cache in Redis
  |
Return Short URL
```

---

## URL Redirect Flow

```text
User
 |
GET /abc123
 |
Redis
 |
Cache Hit
 |
Return Original URL
 |
301 Redirect
```

Latency:

```text
Very Low
```

---

## Cache Miss Flow

```text
User
 |
Redis
 |
Miss
 |
Database
 |
Update Redis
 |
Return URL
```

---

# 301 vs 302 Redirect

## 301 Permanent Redirect

```text
Browser caches result
```

Advantages:

* Faster future requests
* Better SEO

Use when:

```text
Permanent mapping
```

---

## 302 Temporary Redirect

Advantages:

* No browser caching

Use when:

```text
Destination may change
```

---

# Custom Alias Support

User requests:

```text
tinyurl.com/dhiraj
```

Flow:

```text
Check Availability
       |
 Available?
       |
      Yes
       |
 Save Alias
```

Need:

* Unique constraint
* Validation

---

# Expiry Support

User creates:

```text
Expires after 7 days
```

Store:

```text
expiry_at
```

During redirect:

```text
Check Expiry
```

Expired:

```text
410 Gone
```

---

# Unhappy Path

---

## Non Existing Short Code

Request:

```text
/xyz123
```

Redis:

```text
Miss
```

Database:

```text
Miss
```

Response:

```text
404 Not Found
```

---

## Cache Penetration

Problem:

Millions of invalid requests.

Example:

```text
/aaaa
/bbbb
/cccc
```

Each request:

```text
Redis Miss
Database Hit
```

Database overloads.

---

### Solution: Bloom Filter

```text
Request
   |
Bloom Filter
   |
Not Present
   |
Return 404
```

Database never hit.

---

## Cache Stampede

Problem:

Popular cache expires.

Example:

```text
URL visited 1 Million times
```

Cache expires.

Suddenly:

```text
1 Million requests
```

All hit database.

---

### Solutions

#### Mutex Lock

```text
Only one request
rebuilds cache
```

---

#### Random TTL

Avoid simultaneous expiration.

---

#### Background Refresh

Refresh cache before expiry.

---

## Cache Breakdown

Problem:

Very hot key expires.

All traffic hits DB.

Solution:

```text
Never expire hot keys
```

or

```text
Background refresh
```

---

## Duplicate URLs

Problem:

Same long URL shortened repeatedly.

Example:

```text
google.com
google.com
google.com
```

Creates unnecessary entries.

---

### Solution

Store URL Hash

```text
hash(long_url)
```

Check before creating.

---

## Token Generation Service Failure

Problem:

Cannot generate short code.

---

### Solution

* Retry
* Backup generator
* Circuit Breaker

---

## Database Failure

### Primary Failure

```text
Primary Down
```

Solution:

```text
Failover to Replica
```

---

### Region Failure

Solution:

```text
Multi Region Deployment
```

---

## Redis Failure

Problem:

Cache unavailable.

Flow:

```text
Redis Down
      |
Database Handles Traffic
```

Issue:

```text
DB load increases
```

Solution:

```text
Redis Cluster
Redis Replication
```

---

## Malicious URLs

Examples:

* Phishing websites
* Malware downloads

---

### Solution

Before storing:

```text
URL Scanner
```

Unsafe URL:

```text
Rejected
```

---

## Abuse & Spam

Problem:

Bots create millions of URLs.

---

### Solution

* Rate Limiter
* CAPTCHA
* User Quotas
* API Keys

---

# Scaling Discussion

## If Traffic Grows 10x

Scale:

### Application Layer

```text
More URL Services
```

---

### Cache Layer

```text
Redis Cluster
```

---

### Database Layer

```text
Read Replicas
```

---

### Massive Scale

```text
Database Sharding
```

Shard By:

```text
short_code
```

---

# Interview Follow-Up Questions

## Why Redis?

* In-memory
* Very fast
* Reduces DB load

---

## Why Base62?

* Short URLs
* URL-safe characters
* Compact representation

---

## Why Read Through Cache?

* Read-heavy workload

---

## Why Bloom Filter?

* Prevent DB hits for invalid URLs

---

## Why 301 Redirect?

* Better performance
* Browser caching
* SEO friendly

---

## How Would You Handle 100 Billion URLs?

* Sharding
* Redis Cluster
* Multiple Regions
* Read Replicas

---

## What If Redis Crashes?

Traffic falls back to database.

Need:

* Replication
* High Availability
* Auto Recovery

---

# Key Takeaways

* TinyURL is a read-heavy system.
* Redis is critical for performance.
* Base62 is commonly used for short code generation.
* Bloom Filters prevent cache penetration.
* Read replicas and sharding help scale.
* 301 redirects are usually preferred.
* Availability is more important than strong consistency.
