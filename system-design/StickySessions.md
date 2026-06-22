# Sticky Sessions

## What is Sticky Session?

A technique where the same user is always routed to the same server.

Example:

User A → Server 1
User A → Server 1
User A → Server 1

---

## Why Needed?

Suppose session data is stored in memory.

Server 1:

SessionId = ABC123

If next request goes to Server 2:

Session not found.

User gets logged out.

---

## How It Works

Load Balancer remembers:

Client IP → Server Mapping

or

Session Cookie → Server Mapping

---

## Problems with Sticky Sessions

### Uneven Load Distribution

Many users may end up on one server.

---

### Server Failure

If server dies:

Session data is lost.

User must login again.

---

### Poor Scalability

Adding new servers doesn't redistribute existing users.

---

## Better Solution

Store sessions in shared storage:

- Redis
- Database

Flow:

User → Any Server

Server → Redis Session Store

No sticky session required.