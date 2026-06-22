# Caching

## What is Caching?

Caching stores frequently accessed data in a faster storage layer.

Benefits:

- Lower latency
- Reduced DB load
- Better throughput

---

# Browser Cache vs Redis

## Browser Cache

Stored on client side.

Example:

Images
CSS
JavaScript

Flow:

Browser → Local Cache

No server call needed.

---

## Redis Cache

Stored on server side.

Flow:

Application → Redis

Used for:

- Product details
- User profiles
- API responses

---

# Cache Aside Pattern

## Flow

1. Check Cache
2. Cache Miss
3. Read DB
4. Update Cache
5. Return Response

### Diagram

Client
↓
Cache
↓ Miss
DB
↓
Cache Update
↓
Client

### Pros

- Most common
- Easy implementation

### Cons

- Cache miss latency

---

# Write Through

## Flow

Write Request
↓
Cache
↓
Database

Both updated together.

### Pros

- Cache always fresh

### Cons

- Higher write latency

---

# Write Back

## Flow

Write Request
↓
Cache

Database updated later asynchronously.

### Pros

- Fast writes

### Cons

- Risk of data loss

---

# TTL (Time To Live)

Defines expiration time.

Example:

User Profile Cache

TTL = 5 Minutes

After 5 minutes cache is removed.

---

# Cache Invalidation

Removing stale cache.

Example:

Product Price Updated

Old Cache = ₹100

New DB Value = ₹120

Cache must be invalidated.

---

## Common Cache Problems

### Cache Miss

Data not present.

### Cache Hit

Data found.

### Cache Stampede

Many requests hit DB simultaneously after cache expiry.

### Cache Penetration

Requests for non-existing keys repeatedly hit DB.