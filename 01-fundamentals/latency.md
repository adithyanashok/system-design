# Latency

## 1. What is Latency?

**Latency is the time an operation takes to complete.**

In a web system, it usually means how long a user waits for a response.

```text
Client → Server → Database → Server → Client
                 ↑
              Latency
```

Lower latency usually means a faster-feeling system.

---

## 2. A Simple Example

Suppose an API request takes:

```text
Network       → 30 ms
Application   → 20 ms
Database      → 40 ms
Response      → 10 ms
----------------------
Total         → 100 ms
```

The request latency is about **100 ms**.

The important part is finding where that time is being spent.

---

## 3. Latency vs Throughput

These are different.

- **Latency** → how long one request takes.
- **Throughput** → how many requests the system can handle in a period of time.

Example:

```text
Latency   = 100 ms per request
Throughput = 1,000 requests/second
```

A system can have high throughput and still have high latency.

---

## 4. Where Does Latency Come From?

Common sources are:

- Network communication
- Database queries
- Application code
- External APIs
- Queues
- Disk I/O
- Serialization/deserialization
- Waiting for connections or resources

A request often spends time **waiting**, not just computing.

---

## 5. Network Latency

A request travelling between two machines takes time.

Distance, network congestion, routing, and connection setup can all contribute.

```text
User
  ↓
Internet
  ↓
API Server
  ↓
Database
```

If the user is far away from the server, network latency can increase.

### Round-Trip Time (RTT)

RTT is the time for a message to travel to another system and for a response to come back.

If an application makes several network calls, these delays can add up.

---

## 6. Sequential vs Parallel Requests

Consider two independent services:

```text
Sequential:

API → Service A → Service B
      50 ms        50 ms

≈ 100 ms
```

If they can safely run at the same time:

```text
        → Service A → 50 ms
API ────
        → Service B → 50 ms

≈ 50 ms
```

Parallelism can reduce latency, but it increases concurrency and resource usage, so it should be used where it actually helps.

---

## 7. Database Latency

Database queries are a common source of latency.

Poorly designed queries may cause:

- Full table scans
- Too much data being returned
- Unnecessary joins
- Excessive queries

### Indexes

An appropriate index can make lookups much faster by avoiding unnecessary scanning.

But indexes are not free. They use storage and add work to writes.

```text
Without useful index:
Query → scan many rows → result

With useful index:
Query → index → relevant rows → result
```

Always measure before assuming an index will solve the problem.

---

## 8. Caching

A cache stores frequently needed data closer to the application.

```text
Request
   ↓
Cache ── hit ──→ Response
   │
  miss
   ↓
Database
```

A cache hit is usually faster than going to the database.

But caching introduces trade-offs:

- Stale data
- Cache invalidation
- Extra infrastructure
- Cache misses
- Memory usage

**Caching is useful when the cost of fetching data repeatedly is higher than the cost and complexity of caching it.**

---

## 9. CDN

A CDN can serve static or cacheable content from locations closer to users.

```text
User → Nearby CDN → Content
```

This can reduce network latency and load on the origin server.

CDNs are especially useful for images, JavaScript, CSS, videos, and other cacheable content.

---

## 10. Tail Latency

Average latency can hide slow requests.

Suppose:

```text
Most requests → 50 ms
Some requests → 2 seconds
```

The average may look acceptable even though some users have a very slow experience.

That's why systems often use **percentiles**:

- **p50** → 50% of requests are faster than this
- **p95** → 95% are faster
- **p99** → 99% are faster

For user-facing systems, p95 and p99 can be more useful than the average.

---

## 11. Latency Budget

A latency budget is the maximum time available for a request.

For example:

```text
Total budget = 300 ms

Network       → 50 ms
API processing → 80 ms
Database      → 100 ms
Other         → 70 ms
```

If the database suddenly takes 200 ms, the system may exceed the target.

A latency budget helps decide where optimization matters most.

---

## 12. Fan-Out

One request may call many services.

```text
             → Service A
            ↗
User → API → Service B
            ↘
             → Service C
```

The more dependencies involved, the more opportunities there are for delay.

If services are called sequentially, their latency can add up.

If they run in parallel, the overall time is often closer to the slowest dependency, plus overhead.

---

## 13. Timeouts

Every network dependency should have a reasonable timeout.

Without a timeout:

```text
API → Slow service
        ↓
     waits...
        ↓
     waits...
```

Threads, connections, or other resources can remain occupied.

With a timeout:

```text
API → Slow service
        ↓
     timeout
        ↓
fallback / error
```

Timeouts should be based on expected behavior and the latency budget, not chosen arbitrarily.

---

## 14. Connection Reuse

Creating a new network or database connection can add overhead.

Connection pools allow applications to reuse existing connections.

```text
Without pooling:
Request → create connection → query → close

With pooling:
Request → reuse connection → query
```

Connection pooling can reduce latency, especially for frequently used databases.

However, an incorrectly sized pool can also cause contention or overload the database.

---

## 15. Payload Size

Large responses take more time to transfer and process.

Instead of returning unnecessary data:

```json
{
  "id": 123,
  "name": "User",
  "profile": "...",
  "history": "...",
  "unusedData": "..."
}
```

return only what the client needs when practical.

Compression can also reduce network transfer size, but compression itself consumes CPU.

---

## 16. Asynchronous Processing

Not every task needs to happen before the user gets a response.

For example:

```text
User → API → save order → Response
                ↓
              Queue
                ↓
          Send email
```

The email can be processed asynchronously.

This reduces the user-facing latency, but the operation is no longer fully synchronous.

---

## 17. Read Replicas

For read-heavy systems, read replicas can move some read traffic away from the primary database.

```text
             → Primary → writes
Application
             → Replica → reads
```

This can improve scalability and sometimes latency.

But replicas can lag behind the primary, so they should not be used blindly when the latest data is required.

---

## 18. Load and Queueing

As a system gets busy, latency can increase even if the code itself has not changed.

Why?

Because requests start waiting for limited resources:

```text
Requests
   ↓
Queue → Workers → Database
```

Examples of limited resources:

- CPU
- Database connections
- Worker threads
- Network connections
- Queue consumers

High utilization can therefore lead to much higher latency.

---

## 19. Measuring Latency

Do not optimize based only on assumptions.

Measure:

- Endpoint latency
- Database query latency
- External API latency
- Queue wait time
- Cache hit/miss rate
- p50 / p95 / p99
- Resource utilization

For distributed systems, **distributed tracing** helps show where time is spent across services.

```text
Request
 ├─ API        20 ms
 ├─ Database   80 ms
 └─ Payment    150 ms
```

This makes the actual bottleneck easier to find.

---

## 20. Common Ways to Reduce Latency

Depending on the bottleneck:

- Add or improve database indexes
- Cache frequently accessed data
- Reduce unnecessary network calls
- Run independent operations in parallel
- Use connection pooling
- Reduce response size
- Use a CDN
- Move compute/data closer to users
- Optimize slow application code
- Use asynchronous processing
- Avoid unnecessary database queries

The right optimization depends on **what is actually slow**.

---

## 21. Latency Trade-offs

Reducing latency is not always free.

| Approach            | Possible benefit      | Possible cost          |
| ------------------- | --------------------- | ---------------------- |
| Cache               | Faster reads          | Stale data, complexity |
| More replicas       | More read capacity    | Cost, replication lag  |
| Parallel calls      | Lower latency         | More concurrency       |
| Compression         | Less network transfer | More CPU               |
| More infrastructure | Lower bottleneck      | Higher cost            |
| Async processing    | Faster response       | Eventual completion    |

There is no single solution that is always best.

---

## 22. Practical Optimization Process

When an API is slow:

```text
1. Measure
   ↓
2. Find the bottleneck
   ↓
3. Set a latency target
   ↓
4. Optimize the bottleneck
   ↓
5. Measure again
```

Do not add caching, replicas, queues, or other infrastructure just because they are common system-design patterns.

---

## 23. Latency in System Design

When designing a system, ask:

1. What latency does the user need?
2. Where is the latency coming from?
3. How many network calls are required?
4. Can independent work run in parallel?
5. Can frequently used data be cached?
6. Are database queries efficient?
7. What happens when a dependency is slow?
8. What are the p95 and p99 targets?
9. What is the latency budget?
10. How will latency be measured?

---

## 24. Mental Model

Remember:

> **Latency = time spent communicating + waiting + processing.**

To reduce latency, don't simply make the code faster.

First find **where the time is going**, then remove unnecessary work, waiting, or communication.

---

## Summary

- **Latency** is how long an operation takes.
- Network, databases, dependencies, queues, and application code can all add latency.
- **p95/p99** reveal slow requests that averages can hide.
- Caching, indexing, parallelism, connection reuse, CDNs, and async processing can reduce latency when used appropriately.
- High load can increase latency because requests spend more time waiting for resources.
- Always **measure first, find the bottleneck, then optimize**.
