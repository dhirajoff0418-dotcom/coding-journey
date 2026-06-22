# TinyURL System Design (Happy Path)

# Functional Requirements

- Generate short URL
- Redirect short URL to original URL
- Custom alias support
- Link expiry support

---

# Non Functional Requirements

- High Availability
- Low Latency
- Horizontal Scalability
- High Read Throughput

---

# Capacity Estimation

Assume:

100M URLs/day

Read:Write = 100:1

Writes/day = 100M

Reads/day = 10B

---

# High Level Design

Client
↓
Load Balancer
↓
API Servers
↓
Redis
↓
Database

---

# Database Design

## URL Table

| Column | Type |
|----------|----------|
| id | bigint |
| short_code | varchar |
| original_url | text |
| created_at | datetime |
| expiry_at | datetime |

---

# Base62 Encoding

Characters:

a-z
A-Z
0-9

Total = 62

Example:

125 → cb

Used to generate compact URLs.

---

# Write Flow

Client
↓
POST /shorten
↓
Generate ID
↓
Base62 Encode
↓
Store DB
↓
Return Short URL

---

# Read Flow

Client
↓
GET /abc123
↓
Redis Check
↓ Hit
Return URL

Miss
↓
DB
↓
Update Redis
↓
Redirect

---

# Redis Caching

Key:

abc123

Value:

https://example.com

TTL:

24 Hours

---

# Custom Alias

User:

/myportfolio

Store alias directly.

Need uniqueness check.

---

# Link Expiry

Before redirect:

Check expiry_at

Expired → Error

---

# 301 vs 302

## 301

Permanent Redirect

Browser caches response.

SEO Friendly.

---

## 302

Temporary Redirect

No permanent caching.

---

# Full Architecture

Client
↓
DNS
↓
Load Balancer
↓
API Servers
↓
Redis
↓
Database