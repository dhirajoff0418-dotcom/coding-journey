# CAP Theorem - System Design Notes

# What is CAP Theorem?

CAP Theorem states that in a Distributed System, when a Network Partition occurs, we must choose between:

* Consistency (C)
* Availability (A)

Because Partition Tolerance (P) is unavoidable in distributed systems.

---

# CAP = Consistency + Availability + Partition Tolerance

## Consistency (C)

Every node returns the latest data.

No stale reads.

Example:

```text
Balance = ₹1000

Mumbai Node     = ₹1000
Singapore Node  = ₹1000
London Node     = ₹1000
```

All users see the same value.

---

## Availability (A)

Every request receives a response.

System remains operational.

Example:

```text
User Request
      |
      v
 Database
      |
      v
 Response Returned
```

Even if the response contains slightly stale data.

---

## Partition Tolerance (P)

System continues operating despite network failures between nodes.

Example:

```text
Mumbai Node    X----NETWORK FAILURE----X    Singapore Node
```

Nodes cannot communicate.

System must continue functioning.

---

# Biggest CAP Myth

Many people say:

```text
Choose Any Two:
C + A
A + P
C + P
```

This is WRONG.

Correct understanding:

```text
Partition Happens
        |
        v
Must Choose
   /      \
  CP      AP
```

Because Network Partition is unavoidable.

---

# Why Partition Tolerance is Mandatory

Distributed systems run across:

* Multiple servers
* Multiple racks
* Multiple regions
* Multiple countries

Network failures WILL happen.

Therefore:

```text
P = Mandatory
```

Real choice:

```text
Consistency
      vs
Availability
```

---

# Distributed Database Example

Before Partition:

```text
           Client
              |
              v

      +---------------+
      | Mumbai Node   |
      +---------------+
             |
      Replication
             |
      +---------------+
      | Singapore Node|
      +---------------+

Balance = ₹2000 on both nodes
```

---

# Network Partition Occurs

```text
      +---------------+
      | Mumbai Node   |
      +---------------+

             X
             X
      NETWORK FAILURE
             X
             X

      +---------------+
      | Singapore Node|
      +---------------+
```

Communication is broken.

Now CAP tradeoff begins.

---

# CP System

Consistency + Partition Tolerance

Sacrifice Availability.

## Flow

```text
Client Sends Request
          |
          v

Can all required nodes communicate?

          |
     +----+----+
     |         |
    Yes       No
     |         |
     v         v

Process    Reject/Delay Request
Request
```

---

## Example

Banking System

```text
Balance = ₹2000

User Withdraws ₹1000

Mumbai updated = ₹1000

Singapore unreachable

CP Decision:

Reject requests until consistency can be guaranteed.
```

Result:

```text
Consistency  = YES
Availability = NO
```

---

# AP System

Availability + Partition Tolerance

Sacrifice Consistency.

## Flow

```text
Client Sends Request
          |
          v

Network Partition?

          |
     +----+----+
     |         |
    No        Yes
     |         |
     v         v

Normal     Accept Request
Operation
               |
               v

      Sync Later When
      Partition Heals
```

---

## Example

TinyURL

```text
Original URL:

abc.com
|
v

short.ly/xyz
```

Even if one replica is stale:

```text
User gets redirected
```

Service remains available.

Result:

```text
Consistency  = NO (temporarily)
Availability = YES
```

---

# Banking Example (CP)

## Initial State

```text
Mumbai      = ₹2000
Singapore   = ₹2000
```

Partition occurs.

User 1:

```text
Withdraw ₹1000 from Mumbai
```

New state:

```text
Mumbai = ₹1000
```

Singapore doesn't know yet.

If user checks balance through Singapore:

CP system says:

```text
Cannot guarantee latest data.

Request Rejected.
```

Result:

```text
Correct Data
But Lower Availability
```

---

# Banking Example (AP)

Initial State:

```text
Mumbai = ₹2000
Singapore = ₹2000
```

Partition occurs.

User 1:

```text
Withdraw ₹1000
```

Mumbai:

```text
Balance = ₹1000
```

Singapore:

```text
Still thinks balance = ₹2000
```

User 2:

```text
Checks balance
```

Gets:

```text
₹2000
```

This is a:

```text
STALE READ
```

Result:

```text
High Availability
Temporary Inconsistency
```

---

# Real World Examples

## CP Systems

Strong consistency matters.

Examples:

* Banking
* Stock Trading
* Payment Systems
* Financial Ledger
* Inventory Reservation

Rule:

```text
Wrong Data > Downtime
```

---

## AP Systems

Availability matters more.

Examples:

* WhatsApp Messaging
* Instagram Feed
* Facebook Likes
* TinyURL
* Analytics Dashboards

Rule:

```text
Downtime > Slightly Stale Data
```

---

# Why Companies Prefer Eventual Consistency

Strong consistency causes:

* Higher latency
* More coordination
* Reduced availability
* Lower scalability

Eventual consistency provides:

* Better user experience
* Higher availability
* Better scalability
* Faster response times

---

# Interview Questions

## Q1

What does CAP theorem state?

Answer:

CAP states that during a network partition, a distributed system must choose between Consistency and Availability.

---

## Q2

Can a system have all three?

Answer:

No.

When partition occurs, only one of Consistency or Availability can be fully guaranteed.

---

## Q3

Does CAP mean choose any two?

Answer:

No.

Partition Tolerance is mandatory.

The real choice is:

```text
CP or AP
```

---

## Q4

Does CAP apply to a single database server?

Answer:

No.

CAP is about distributed systems.

A single PostgreSQL instance is not a distributed system.

---

## Q5

Why not make every system CP?

Answer:

Because CP may:

* Increase latency
* Reduce availability
* Hurt user experience

Many applications can tolerate eventual consistency.

---

# Quick Revision

```text
Consistency
=
Latest data everywhere

Availability
=
Every request gets response

Partition Tolerance
=
System survives network failure

Partition Happens
        |
        v

Choose One

CP -> Consistency > Availability

AP -> Availability > Consistency
```

# One-Line Interview Answer

CAP theorem states that in a distributed system, when a network partition occurs, we must choose between Consistency and Availability because Partition Tolerance is unavoidable.
