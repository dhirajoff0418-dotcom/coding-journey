# Load Balancer

## What is a Load Balancer?

A Load Balancer distributes incoming requests across multiple servers to:
- Improve availability
- Improve scalability
- Prevent server overload
- Reduce latency

Without a load balancer:

Client → Server 1

If Server 1 fails, application becomes unavailable.

With a load balancer:

Client → Load Balancer → Multiple Servers

---

## Load Balancing Algorithms

### 1. Round Robin

Requests are distributed sequentially.

Example:

Request 1 → Server A
Request 2 → Server B
Request 3 → Server C
Request 4 → Server A

#### Pros
- Simple
- Easy to implement

#### Cons
- Doesn't consider server load

---

### 2. Weighted Round Robin

Servers receive traffic based on weights.

Example:

Server A Weight = 3
Server B Weight = 1

Traffic:

A → A → A → B

#### Use Case

When servers have different capacities.

---

### 3. IP Hashing

Hash(Client IP) % Number of Servers

Example:

IP 192.168.1.10 → Server B

Same IP always reaches same server.

#### Pros
- Session consistency

#### Cons
- Uneven traffic distribution

---

### 4. Least Connections

Request goes to server with least active connections.

Example:

Server A = 100 connections
Server B = 20 connections

Next request → Server B

#### Pros
- Better utilization

#### Cons
- Slightly more expensive

---

## Interview Questions

### Why use a Load Balancer?

- High availability
- Scalability
- Fault tolerance

### What happens if a server goes down?

Load balancer performs health checks and stops routing traffic.

### Hardware vs Software Load Balancer

Hardware:
- Expensive
- Dedicated appliance

Software:
- Nginx
- HAProxy
- AWS ALB