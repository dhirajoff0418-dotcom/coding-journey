# TinyURL System Design (Unhappy Path)

# Non-Existent Code

Request:

/xyz123

Redis Miss
↓
DB Miss
↓
404 Not Found

---

# Cache Penetration

Problem:

Repeated requests for invalid URLs.

Every request hits DB.

---

## Solution

Bloom Filter

Flow:

Request
↓
Bloom Filter

Not Present
↓
Return 404

No DB hit.

---

# Expired Links

Check expiry timestamp.

If expired:

410 Gone

or

404 Not Found

---

# Duplicate URLs

Same long URL shortened multiple times.

Solution:

Hash Original URL

Check existing mapping first.

---

# Token Service Failure

Cannot generate short code.

Solutions:

- Retry
- Fallback generator
- Multiple token services

---

# Cache Stampede

Problem:

Popular cache key expires.

Thousands of requests hit DB.

---

## Solutions

### Cache Rebuild Lock

Only one request updates cache.

---

### Random TTL

Avoid simultaneous expiry.

---

### Background Refresh

Refresh before expiration.

---

# Database Failure

## Primary Down

Switch to Replica.

---

## Regional Failure

Use Multi-Region Deployment.

---

# Malicious URLs

Examples:

- Phishing
- Malware

Solution:

URL Safety Scanner

Block unsafe URLs.

---

# Abuse & Spam

Examples:

- Millions of URLs
- Bot Traffic

Solutions:

- Rate Limiter
- CAPTCHA
- User Quotas

---

# Interview Follow-Up Questions

### Why Redis?

Low latency.

### Why Bloom Filter?

Prevent DB hits for invalid keys.

### Why Base62?

Short URLs with compact size.

### Why 301?

Better performance and SEO.

### How handle 10x traffic?

- Add servers
- Redis Cluster
- DB Sharding
- CDN