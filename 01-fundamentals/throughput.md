# Throughput

## 1. What is Throughput?

**Throughput is the amount of work a system completes in a given amount of time.**

For web systems, it is commonly measured as:

```text
Requests per second (RPS)
```

Other examples:

- Transactions per second (TPS)
- Messages per second
- Jobs per second
- MB/s or GB/s

Example:

```text
1 second → 1,000 requests completed

Throughput = 1,000 requests/second
```

---

## 2. Throughput vs Latency

They measure different things.

- **Latency** → how long one operation takes.
- **Throughput** → how much work the system handles over time.

A system can have low latency but limited throughput, or high throughput while individual requests are slow.

---

## 3. Throughput vs Capacity

**Capacity** is how much work a system can handle under defined conditions.

**Throughput** is how much work it is actually handling.

```text
Capacity = 10,000 requests/sec
Traffic  = 6,000 requests/sec

Current throughput = 6,000 requests/sec
```

Capacity is not always a single fixed number; it depends on the workload and the limits you define.

---

## 4. What Limits Throughput?

Common bottlenecks include:

- CPU
- Memory
- Database
- Disk I/O
- Network bandwidth
- Database connections
- Worker threads
- External services
- Queue consumers

Example:

```text
API Servers
     ↓
 Database
     ↓
2,000 writes/sec limit
```

Even if the API servers can handle 10,000 requests/sec, the database may limit the overall system.

The key question is:

> **Which component reaches its practical limit first?**

---

## 5. Vertical vs Horizontal Scaling

### Vertical Scaling

Make one machine more powerful.

```text
4 CPU  → 2,000 req/sec
8 CPU  → 3,500 req/sec
```

Simple, but hardware has limits and larger machines can become expensive.

### Horizontal Scaling

Add more machines.

```text
             → Server A
Requests → LB → Server B
             → Server C
```

If one server handles 2,000 req/sec, three servers may handle roughly 6,000 **when the workload scales well and another bottleneck does not appear**.

---

## 6. Stateless Services

Horizontal scaling is easier when application servers are stateless.

```text
          → Server A
LB ──────→ Server B
          → Server C
```

Any server can handle a request.

Shared state such as sessions may need to live in a database, cache, or another shared system.

---

## 7. Database Throughput

Databases often become bottlenecks as traffic grows.

Possible improvements include:

- Better queries
- Appropriate indexes
- Connection pooling
- Batching
- Caching suitable reads
- Read replicas
- Partitioning or sharding when necessary

Each has trade-offs.

For example, indexes can improve reads but add storage and write overhead.

---

## 8. Batching

Instead of doing many small operations:

```text
100 items
↓
100 database operations
```

some workloads can batch them:

```text
100 items
↓
1 batch operation
```

Batching can reduce per-operation and network overhead.

The trade-off is that batching can increase waiting time for individual items.

---

## 9. Queues and Throughput

Queues separate producers from consumers.

```text
Producer
   ↓
 Queue
   ↓
Workers
```

If one worker processes 100 jobs/sec:

```text
1 worker → 100 jobs/sec
5 workers → potentially 500 jobs/sec
```

The real throughput still depends on the database, downstream services, message size, and other bottlenecks.

Queues can also absorb temporary traffic spikes.

---

## 10. Backpressure

If producers create work faster than consumers can process it, the queue grows.

```text
Producer → 10,000 jobs/sec
              ↓
            Queue
              ↓
Consumer → 5,000 jobs/sec
```

The queue grows by roughly 5,000 jobs/sec.

Backpressure prevents the system from accepting unlimited work.

Possible approaches:

- Rate limiting
- Limiting queue size
- Slowing producers
- Rejecting excess work
- Scaling consumers

---

## 11. Concurrency and Throughput

More concurrent workers can increase throughput, but only up to a point.

```text
1 worker   → 100 jobs/sec
5 workers  → 450 jobs/sec
10 workers → 700 jobs/sec
20 workers → 700 jobs/sec
```

At some point, another resource becomes the bottleneck.

Adding more workers after that may only increase contention.

---

## 12. Little's Law

A useful queueing relationship is:

```text
Concurrency = Throughput × Average Latency
```

Example:

```text
Throughput = 1,000 requests/sec
Latency    = 0.1 sec

Concurrency ≈ 1,000 × 0.1
            = 100 requests
```

This is **Little's Law**, under its usual steady-state assumptions.

It helps estimate how much work is in the system at a given time.

---

## 13. Load and Utilization

As traffic approaches a system's limits, latency can rise significantly.

```text
Traffic
0% ───── 50% ───── 90% ─── 100%
                    ↑
              contention grows
```

Requests may spend more time waiting for CPU, connections, locks, queues, or other resources.

Designing for exactly the maximum measured throughput leaves little room for traffic spikes or failures.

---

## 14. Throughput vs Latency Trade-off

Improving throughput does not always improve latency.

For example:

```text
Small batches → lower waiting time
Large batches → better efficiency
```

Large batches may process more work efficiently but make individual operations wait longer.

The right choice depends on the workload.

---

## 15. Measuring Throughput

Useful metrics include:

- Requests/sec
- Successful requests/sec
- Transactions/sec
- Messages/sec
- Jobs/sec
- Bytes/sec
- Queue processing rate
- Database operations/sec

Also watch:

- Error rate
- p95/p99 latency
- CPU and memory
- Database utilization
- Queue depth

High throughput with many errors is not useful throughput.

---

## 16. Sustainable Throughput

A benchmark showing:

```text
10,000 requests/sec
```

does not automatically mean the system can safely run at 10,000 requests/sec.

A useful capacity test should consider:

- Error rate
- p95/p99 latency
- Resource utilization
- Test duration
- Realistic request mix
- Downstream limits

The goal is usually **sustainable throughput within acceptable latency and error targets**.

---

## 17. Practical Optimization Process

When throughput is too low:

```text
Measure
  ↓
Find bottleneck
  ↓
Improve or scale it
  ↓
Test again
  ↓
Repeat
```

Do not automatically add more application servers.

If the database is the bottleneck, adding API servers may simply send more traffic to the same database.

---

## 18. Throughput in System Design

Ask:

1. How much traffic do we expect?
2. What throughput is required?
3. Is the workload mostly reads, writes, or both?
4. What is likely to become the bottleneck?
5. Can the workload scale horizontally?
6. Do we need batching or queues?
7. How will we handle traffic spikes?
8. What are the database and downstream limits?
9. What latency and error rate are acceptable?
10. How will we test sustainable capacity?

---

## Mental Model

> **Throughput = how much useful work the system completes per unit of time.**

To increase throughput, find the bottleneck and increase the amount of useful work that bottleneck can handle.

Always consider **latency, errors, cost, and correctness** along with throughput.

---

## Summary

- **Throughput** measures how much work a system completes over time.
- It is commonly measured in RPS, TPS, jobs/sec, or MB/sec.
- The system's bottleneck limits overall throughput.
- Horizontal scaling helps when the workload and dependencies scale with it.
- Batching, queues, caching, and database optimization can improve throughput when appropriate.
- More concurrency does not always mean more throughput.
- Measure **throughput together with latency, errors, and resource usage**.
