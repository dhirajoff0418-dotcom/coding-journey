# DNS

## What is DNS?

DNS converts domain names into IP addresses.

Example:

google.com
↓
142.250.x.x

---

## DNS Resolution Flow

### Step 1

Browser checks local cache.

---

### Step 2

Request goes to Recursive Resolver.

---

### Step 3

Resolver asks Root Server.

Root Server says:

Ask .com TLD server.

---

### Step 4

Resolver asks TLD Server.

TLD says:

Ask Authoritative Server.

---

### Step 5

Resolver asks Authoritative Server.

Returns:

google.com → IP

---

### Step 6

IP returned to browser.

Browser connects to server.

---

## DNS Hierarchy

Root Server
↓
TLD Server (.com)
↓
Authoritative Server
↓
IP Address

---

## DNS TTL

TTL = Time To Live

Example:

TTL = 300 seconds

DNS response cached for 5 minutes.

Benefits:

- Faster lookup
- Reduced DNS traffic

---

## Interview Questions

### Why DNS caching?

Reduce latency.

### What if Authoritative Server is down?

Cached records may still work until TTL expires.