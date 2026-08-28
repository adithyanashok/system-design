# Scalability

When an application starts getting more users, the amount of work it has to do also increases.

A system that works perfectly with 100 users may struggle when it has to serve 100,000 users. **Scalability is about designing the system so it can grow without becoming unusable.**

---

## 1. What Does Scalability Mean?

In simple terms:

> **Scalability is the ability of a system to handle a growing workload by increasing its capacity.**

The workload might grow because of:

- More users
- More requests
- More data
- More files
- More background jobs
- More traffic

For example, an application might start like this:

```text
100 users
    ↓
Application
```

A few months later:

```text
100,000 users
      ↓
Application
```

The important question is not just whether the application works today.

It's:

> **What happens when the workload becomes 10x, 100x, or 1,000x larger?**

If the system can grow to handle that workload, it is scalable.

---

# 2. Why Do We Need Scalability?

Consider a very simple application:

```text
Users
  |
  v
+----------------+
| Application    |
| Server         |
+----------------+
        |
        v
    Database
```

When the application is small, this may be completely fine.

For example:

```text
100 users
   ↓
1 application server
   ↓
1 database
```

Now imagine the application becomes popular:

```text
100,000 users
      ↓
1 application server
      ↓
1 database
```

The server now has much more work to do.

It may start running into problems such as:

- CPU becoming heavily utilized
- Memory pressure
- Slow response times
- Too many open connections
- Failed requests
- Server crashes

Eventually, users may see:

```text
Request
   ↓
Overloaded server
   ↓
Error / timeout
```

Scalability gives us ways to deal with this growth instead of allowing one component to become the limit of the whole system.

---

# 3. A Simple Real-World Example

Imagine building an e-commerce application.

At first, the application has around:

```text
1,000 users
```

A single application server can probably handle the workload.

Then a large sale starts.

Traffic grows:

```text
1,000
  ↓
10,000
  ↓
100,000
  ↓
1,000,000 users
```

The system now has to deal with much more:

- HTTP traffic
- Product searches
- Database queries
- Orders
- Payments
- Images
- Network traffic

At some point, simply running the same application on the same server is no longer enough.

The system needs additional capacity.

That is the problem scalability tries to solve.

---

# 4. Scalability vs Performance

**Performance** and **scalability** are related, but they are not the same thing.

### Performance

Performance is mainly concerned with how quickly a system handles a workload.

For example:

```text
Request
   ↓
Server
   ↓
Response

Response time = 100 ms
```

A lower response time generally means better performance.

### Scalability

Scalability is concerned with what happens when the workload increases.

For example:

```text
1,000 users      → Works
10,000 users     → Works
100,000 users    → Works
1,000,000 users  → Works
```

A system can be fast at a small scale and still fail when traffic increases.

For example:

```text
1 request → 10 ms
```

That sounds excellent.

But if 100,000 requests cause the server to crash, the system still has a scalability problem.

### A simple way to remember it

> **Performance asks: "How fast is it?"**

> **Scalability asks: "How well does it handle growth?"**

---

# 5. The Two Basic Ways to Scale

There are two fundamental approaches:

1. **Vertical scaling**
2. **Horizontal scaling**

```text
                    Scaling
                       |
             +---------+---------+
             |                   |
       Vertical Scaling    Horizontal Scaling
          (Scale Up)          (Scale Out)
```

---

# 6. Vertical Scaling

Vertical scaling means **giving an existing machine more resources**.

Suppose an application is running on:

```text
CPU:      2 cores
RAM:      4 GB
Storage:  100 GB
```

We can move it to a larger machine:

```text
CPU:      8 cores
RAM:      32 GB
Storage:  1 TB
```

The application is still running on one server. The server has simply become more powerful.

```text
Before

Users
  |
  v
+-----------+
| Server    |
| 2 CPU     |
| 4 GB RAM  |
+-----------+
```

After:

```text
Users
  |
  v
+-------------+
| Server      |
| 8 CPU       |
| 32 GB RAM   |
+-------------+
```

This is called **vertical scaling**, or **scaling up**.

---

## 6.1 Why Use Vertical Scaling?

Vertical scaling is often the simplest first step.

You may not need to change the application architecture at all.

For example:

```text
2 CPU  →  8 CPU
4 GB   →  32 GB RAM
```

The application can continue running in the same way.

### Advantages

- Simple to understand
- Usually easy to implement
- Requires fewer architectural changes
- Can be a good choice for smaller systems

---

## 6.2 Limits of Vertical Scaling

A machine has a practical limit.

You cannot keep making the same server larger forever.

For example:

```text
2 CPU
  ↓
8 CPU
  ↓
32 CPU
  ↓
64 CPU
  ↓
128 CPU
  ↓
Maximum practical size
```

Larger machines can also become expensive.

There is another important problem: if the application depends on one server and that server fails, the application may become unavailable.

```text
Users
  |
  v
Server
  |
  X
Failure
```

So vertical scaling can increase capacity, but by itself it does not remove the single-server dependency.

---

# 7. Horizontal Scaling

Horizontal scaling takes a different approach.

Instead of making one server bigger, we **add more servers**.

Instead of:

```text
Users
  |
  v
Large Server
```

we can have:

```text
             Users
               |
               v
        +---------------+
        | Load Balancer |
        +---------------+
          /      |      \
         v       v       v
     Server 1 Server 2 Server 3
```

Each server handles a portion of the requests.

This is also called **scaling out**.

---

# 8. Scale Up vs Scale Out

The difference is easier to remember with a simple example.

### Scale Up

Make one machine stronger:

```text
1 server
   ↓
Bigger server
```

This is **vertical scaling**.

### Scale Out

Add more machines:

```text
1 server
   ↓
3 servers
   ↓
10 servers
   ↓
100 servers
```

This is **horizontal scaling**.

---

# 9. Why Horizontal Scaling Helps

Suppose one application server can handle roughly:

```text
1,000 requests/second
```

If the workload is suitable for distribution, adding servers can increase the total capacity.

For example:

```text
1 server  → ~1,000 requests/sec
3 servers → ~3,000 requests/sec
```

The actual capacity will depend on the application and workload, so this is only a simplified example.

A load balancer can distribute incoming traffic:

```text
                 Requests
                    |
                    v
             Load Balancer
             /      |      \
            v       v       v
         Server   Server   Server
            1       2       3
```

This gives the system more application capacity without requiring one enormous machine.

---

# 10. Stateless Applications

Horizontal scaling becomes much easier when application servers are **stateless**.

A stateless server does not depend on information stored only in its own local memory to handle the next request.

For example:

```text
Request 1 → Server 1
Request 2 → Server 2
Request 3 → Server 3
```

Any server can handle the request.

The problem becomes more obvious if a server keeps important session information only in its own memory:

```text
Server 1
   |
   v
Local Memory
   |
   v
User Session
```

If the next request goes to Server 2, Server 2 does not have that information.

A common approach is to move shared state to something that all servers can access:

```text
Server 1 ─┐
Server 2 ─┼──→ Shared Store
Server 3 ─┘       |
                  ├── Redis
                  └── Database
```

This makes it much easier to add or remove application servers.

---

# 11. Load Balancers

Once there are multiple application servers, something needs to decide where incoming requests should go.

A **load balancer** sits in front of the servers and distributes traffic.

Without one, a client would need to know which server to contact:

```text
Users
 ├──→ Server 1
 ├──→ Server 2
 └──→ Server 3
```

With a load balancer:

```text
Users
   |
   v
Load Balancer
   |
   +----→ Server 1
   |
   +----→ Server 2
   |
   +----→ Server 3
```

A load balancer can also perform health checks.

For example:

```text
Server 1 → Healthy
Server 2 → Healthy
Server 3 → Unhealthy
```

The load balancer can stop sending new requests to the unhealthy server.

---

# 12. Scaling the Database

Adding application servers does not automatically make the entire system scalable.

Consider:

```text
Users
  |
  v
Load Balancer
  |
  +----→ API Server
  +----→ API Server
  +----→ API Server
              |
              v
          Database
```

The application layer now has more capacity.

But all those servers still depend on the same database.

If the database cannot handle the additional workload, it becomes the bottleneck.

```text
More API Servers
       |
       v
More Database Queries
       |
       v
Database
       |
       v
Bottleneck
```

So scalability has to be considered across the whole system, not just the application servers.

---

# 13. Database Scaling Techniques

Two common techniques are **read replicas** and **sharding**.

## 13.1 Read Replicas

A primary database can replicate its data to other databases.

A simplified architecture looks like:

```text
              Application
                   |
                   v
           Primary Database
                   |
              Replication
               /       \
              v         v
        Read Replica  Read Replica
```

The primary database can handle writes, while read traffic can be distributed across replicas.

The exact read/write setup depends on the database and application.

---

## 13.2 Sharding

Sharding means splitting data across multiple database instances.

Instead of:

```text
One Database
     |
     +-- All users
```

data can be distributed:

```text
             Application
                  |
          +-------+-------+
          |       |       |
          v       v       v
        DB 1    DB 2    DB 3
```

For example, a simplified partition might look like:

```text
DB 1 → Users 1 - 1M
DB 2 → Users 1M - 2M
DB 3 → Users 2M - 3M
```

The exact sharding strategy depends on how the data is accessed.

Sharding can provide more capacity, but it also makes the system more complicated.

---

# 14. Caching and Scalability

Another way to improve scalability is to **avoid doing the same expensive work repeatedly**.

Suppose millions of users request the same piece of data.

Without a cache:

```text
Users
  |
  v
Application
  |
  v
Database
```

The database may have to process the same type of request many times.

With a cache:

```text
Users
  |
  v
Application
  |
  v
Cache
  |
  v
Database
```

If the requested data is already in the cache:

```text
User
 |
 v
Application
 |
 v
Cache
 |
 v
Response
```

The database does not need to handle that request.

Common caching technologies and layers include:

- Redis
- Memcached
- CDN caching
- Application-level caching
- Database caching

Caching can improve performance and, importantly for scalability, reduce load on backend systems.

---

# 15. CDN and Scalability

Applications often serve large amounts of static content such as:

- Images
- Videos
- JavaScript
- CSS
- Other static files

If every request has to travel back to the main server:

```text
Users
  |
  v
Main Server
  |
  v
Static Content
```

the origin server can end up doing unnecessary work.

A CDN can keep copies of content closer to users:

```text
                    Users
                  /   |   \
                 v    v    v
               CDN  CDN  CDN
                 \    |   /
                  \   |  /
                   Origin
                   Server
```

Users can receive cached content from a nearby CDN location instead of always requesting it from the origin.

This reduces traffic reaching the origin servers.

---

# 16. Asynchronous Processing

Not every task needs to finish before the user receives a response.

Imagine an order workflow:

```text
Create Order
Send Email
Generate Invoice
Update Analytics
Send Notification
```

If the application performs everything during the original request, the user may have to wait for all of it.

A message queue can move some work to background workers:

```text
User
  |
  v
Application
  |
  +----→ Create Order
  |
  v
Message Queue
  |
  v
Background Workers
  ├── Send Email
  ├── Generate Invoice
  ├── Update Analytics
  └── Send Notification
```

Now the application can accept the request and let workers handle tasks that do not need to happen immediately.

Examples of messaging systems include:

- Kafka
- RabbitMQ
- Amazon SQS

---

# 17. Auto Scaling

Traffic is rarely constant.

An application might experience:

```text
2 AM       → Low traffic
10 AM      → Normal traffic
8 PM       → High traffic
Sale event → Very high traffic
```

Running a large number of servers all the time may waste resources.

Auto scaling allows infrastructure to add or remove capacity based on demand.

For example:

```text
Low traffic
    ↓
2 servers
```

Then:

```text
High traffic
    ↓
10 servers
```

And when traffic falls:

```text
Lower traffic
    ↓
3 servers
```

This is called **auto scaling**.

---

# 18. Scalability Is Not Just "Add More Servers"

One of the easiest mistakes in system design is assuming that every scaling problem can be solved by adding application servers.

Consider:

```text
             Users
               |
               v
         Load Balancer
               |
        +------+------+------+
        |      |      |      |
        v      v      v      v
       API    API    API    API
        |      |      |      |
        +------+------+------+
               |
             Cache
               |
               v
           Database
               |
               v
        Other Services
```

If the database is the bottleneck, adding ten more API servers may actually make the situation worse by sending even more traffic to the database.

Possible bottlenecks include:

- CPU
- Memory
- Database
- Network
- Disk/storage
- Cache
- External APIs
- Message queues
- Application code

So the first step in scaling is often:

> **Find the bottleneck.**

---

# 19. What Is a Bottleneck?

A bottleneck is the part of a system that limits the capacity of the overall system.

For example:

```text
100 requests/sec
       |
       v
Application
       |
       v
100 requests/sec
       |
       v
Database
       |
       v
30 requests/sec
```

Here, the database can only process about 30 requests/sec in this simplified example.

The database is therefore limiting the system.

Adding more application servers:

```text
10 API Servers
       |
       v
   Database
       |
       v
30 requests/sec
```

doesn't solve the database's capacity limit.

This is why measuring the system is important before deciding how to scale it.

---

# 20. Vertical and Horizontal Scaling Together

Real systems do not always choose one approach exclusively.

It is common to combine them.

For example:

```text
                 Users
                   |
                   v
             Load Balancer
             /      |      \
            v       v       v
         Server   Server   Server
         8 CPU    8 CPU    8 CPU
             \      |      /
              \     |     /
                Cache
                  |
                  v
              Database
              32 CPU
```

Here:

- Multiple application servers provide horizontal scaling.
- Each server has more CPU and memory than a small machine, which is vertical scaling.
- The database is also given additional resources.

The right combination depends on the workload and constraints.

---

# 21. Scalability vs Availability

Scalability and availability solve different problems.

### Scalability

> **Can the system handle more workload?**

### Availability

> **Can users access the system when they need it?**

Consider a single powerful server:

```text
Server
  |
  v
Can handle 1 million requests
```

It might have enough capacity for the workload.

But if that server fails:

```text
Server
  X
Failure
```

the application may become unavailable.

Using multiple servers can help with both capacity and failure handling:

```text
Server 1 → Healthy
Server 2 → Healthy
Server 3 → Failed

Users can still be served by 1 and 2.
```

This is why scalable architectures often include redundancy.

---

# 22. Fault Tolerance

When a system has multiple instances, one instance can fail without necessarily taking down the whole application.

For example:

```text
Server 1 → Healthy
Server 2 → Healthy
Server 3 → Failed
```

The remaining servers can continue serving requests.

This is one of the benefits of distributing workloads across multiple machines.

However, simply having multiple servers does not automatically make a system fault tolerant. The architecture also needs to handle failures correctly.

---

# 23. Vertical vs Horizontal Scaling

|                  | Vertical Scaling             | Horizontal Scaling                                 |
| ---------------- | ---------------------------- | -------------------------------------------------- |
| Also called      | Scale Up                     | Scale Out                                          |
| Main idea        | Make a machine bigger        | Add more machines                                  |
| Complexity       | Usually lower                | Usually higher                                     |
| Hardware limit   | Yes                          | Less dependent on one machine                      |
| Failure handling | Often depends on one machine | Multiple instances can provide redundancy          |
| Architecture     | Simpler                      | More distributed                                   |
| Typical use      | Smaller or simpler systems   | Systems that need to grow across multiple machines |

---

# 24. When Should You Scale?

Not every application needs a complicated distributed architecture.

For a small application, this may be enough:

```text
Users
  |
  v
Application
  |
  v
Database
```

If the workload grows, you can evolve the architecture.

For example:

```text
Users
  |
  v
Load Balancer
  |
  +----→ Application
  +----→ Application
  +----→ Application
              |
              v
             Cache
              |
              v
           Database
```

The important principle is:

> **Don't introduce complexity before you have a reason for it.**

A system with 100 users does not necessarily need the architecture of a system serving millions of users.

---

# 25. A Typical Scaling Journey

A system often grows gradually rather than jumping directly to a massive architecture.

## Stage 1 — Small Application

```text
Users
  |
  v
Application
  |
  v
Database
```

A single application server and database may be enough.

---

## Stage 2 — The Server Needs More Resources

```text
Users
  |
  v
Bigger Application Server
  |
  v
Database
```

Vertical scaling may be enough at this stage.

---

## Stage 3 — More Application Traffic

```text
              Users
                |
                v
          Load Balancer
            /       \
           v         v
       Server      Server
           \         /
            \       /
             Database
```

The application layer is now horizontally scaled.

---

## Stage 4 — Database Load Increases

A cache can reduce repeated database work:

```text
Application Servers
        |
        v
      Cache
        |
        v
     Database
```

Database replicas may also be introduced when appropriate.

---

## Stage 5 — The System Becomes Much Larger

A more advanced architecture might look like:

```text
                    Users
                      |
                     CDN
                      |
                Load Balancer
                      |
              +-------+-------+
              |       |       |
             API     API     API
              |       |       |
              +-------+-------+
                      |
                    Cache
                      |
              +-------+-------+
              |               |
          Primary DB      Read Replicas
              |
            Queue
              |
           Workers
```

At this scale, additional techniques may become useful:

- Database replication
- Sharding
- Message queues
- Auto scaling
- CDN
- Multiple regions
- Service decomposition

The architecture should be driven by the actual bottlenecks and requirements.

---

# 26. Challenges of Horizontal Scaling

Horizontal scaling provides more capacity, but it also introduces distributed-system problems.

### 1. Shared State

Multiple servers may need access to the same information.

### 2. Data Consistency

When data is replicated, different copies may not always be updated at exactly the same time.

### 3. Network Communication

Servers now depend on communication over a network.

Networks can be slow, unreliable, or unavailable.

### 4. Failure Handling

Any server, database, network connection, or service can fail.

### 5. Debugging

A single request can pass through many components:

```text
Client
  |
  v
Load Balancer
  |
  v
API
  |
  v
Service
  |
  v
Queue
  |
  v
Worker
  |
  v
Database
```

When something goes wrong, finding the exact source can be more difficult.

This is one of the trade-offs of distributed architectures.

---

# 27. Scalability and Distributed Systems

As applications become larger, they often move from a simple single-machine architecture to a distributed architecture.

Instead of:

```text
One Server
```

we may eventually have:

```text
Multiple Servers
Multiple Databases
Caches
Queues
CDNs
Services
Multiple Regions
```

These components communicate over a network.

That introduces topics such as:

- Replication
- Partitioning
- Sharding
- Consistency
- Eventual consistency
- Fault tolerance
- Consensus
- CAP theorem

Scalability is therefore one of the core ideas behind system design.

---

# 28. Example: Scaling a Social Media Application

Let's take a simple social media application.

At the beginning:

```text
Users
  |
  v
API Server
  |
  v
Database
```

As the number of users grows:

```text
                 Users
                   |
                   v
             Load Balancer
              /    |    \
             v     v     v
           API   API   API
             \    |    /
              \   |   /
                Cache
                  |
                  v
               Database
```

As the system grows further:

```text
                         Users
                           |
                          CDN
                           |
                    Load Balancer
                           |
                +----------+----------+
                |          |          |
               API        API        API
                |          |          |
                +----------+----------+
                           |
                         Cache
                           |
                  +--------+--------+
                  |                 |
              Database            Queue
                  |                 |
                  |              Workers
                  |
             Read Replicas
```

Different components solve different problems:

- CDN → static content delivery
- Load balancer → distributes traffic
- API servers → handle application requests
- Cache → reduces repeated work
- Database → stores persistent data
- Read replicas → distribute read workload
- Queue/workers → process background tasks

---

# 29. How to Think About Scalability

When you are designing a system, don't immediately start adding technologies.

Start by asking questions.

## Step 1 — How much traffic do we have?

```text
Requests/second?
Concurrent users?
Users/day?
```

## Step 2 — What is growing?

```text
Users?
Requests?
Data?
Files?
Messages?
```

## Step 3 — Where is the bottleneck?

```text
CPU?
Memory?
Database?
Network?
Storage?
```

## Step 4 — Can we scale the bottleneck vertically?

```text
Can we use a larger machine?
```

## Step 5 — Can we scale it horizontally?

```text
Can we distribute the workload across machines?
```

## Step 6 — Can we avoid doing the work?

For example:

```text
Caching
CDN
Compression
Batch processing
```

## Step 7 — Can the work happen asynchronously?

```text
Request
   |
   v
Message Queue
   |
   v
Workers
```

## Step 8 — What happens when something fails?

Think about:

```text
Server failure
Database failure
Network failure
Service failure
Region failure
```

This way of thinking is more important than memorizing a list of technologies.

---

# 30. A Simple Mental Model

A useful way to think about scalability is:

```text
More Users
    |
    v
More Requests
    |
    v
More Work
    |
    v
Existing Resources Become a Bottleneck
    |
    v
Find the Bottleneck
    |
    v
Choose a Solution
```

Possible solutions:

```text
                       Scalability
                            |
             +--------------+--------------+
             |              |              |
          Scale Up       Scale Out      Reduce Work
             |              |              |
        Bigger Server   More Servers   Cache / CDN
                                       Compression
                                       Async Work
```

---

# 31. Common Mistakes

### Mistake 1: "Scalability means adding more servers."

Not necessarily.

Sometimes the bottleneck is the database, network, storage, or application code.

---

### Mistake 2: "Horizontal scaling is always better."

Horizontal scaling can provide more capacity and redundancy, but it also introduces complexity.

A simple application may be better served by vertical scaling.

---

### Mistake 3: "More servers automatically mean more capacity."

Only if the workload can actually be distributed and another component does not become the bottleneck.

For example:

```text
20 API Servers
      |
      v
One overloaded database
```

The database can still limit the system.

---

### Mistake 4: "Scalability and performance are the same."

They are related, but different.

A system can have excellent response times at low traffic and still fail under heavy traffic.

---

### Mistake 5: "Every application should be distributed."

Distributed systems are useful, but they also introduce complexity.

Start simple and add complexity when the requirements justify it.

---

# 32. Interview Questions

## Beginner

### 1. What is scalability?

Scalability is the ability of a system to handle increasing workload by increasing its capacity.

### 2. What is vertical scaling?

Increasing the resources of an existing machine.

### 3. What is horizontal scaling?

Adding more machines and distributing the workload across them.

### 4. What is the difference between scale up and scale out?

Scale up means making a machine more powerful. Scale out means adding more machines.

---

## Intermediate

### 5. Why is horizontal scaling useful?

It allows workload to be distributed across multiple machines and can provide additional capacity and redundancy.

### 6. Why is a load balancer used with horizontal scaling?

It distributes incoming requests across multiple application servers and can help avoid sending traffic to unhealthy instances.

### 7. Why might adding more application servers not solve a scalability problem?

Because another component, such as the database or network, may already be the bottleneck.

### 8. How does caching help scalability?

Caching can reduce repeated work and decrease the load on backend systems such as databases.

---

## Advanced

### 9. How would you scale an application from 1,000 to 1 million users?

There is no universal architecture.

First estimate the workload and identify bottlenecks. Depending on the system, solutions might include horizontal scaling, load balancing, caching, database replication or sharding, asynchronous processing, CDNs, and auto scaling.

### 10. What problems does horizontal scaling introduce?

It can introduce problems around shared state, consistency, network failures, coordination, debugging, and data management.

### 11. How do you decide what to scale?

Measure the system first. Look at metrics such as CPU, memory, latency, throughput, database utilization, connection counts, queue depth, and error rates to identify the limiting component.

---

# 33. Summary

- **Scalability** is about handling growth in workload.
- **Vertical scaling** means making an existing machine more powerful.
- **Horizontal scaling** means adding more machines.
- **Load balancers** distribute traffic across multiple servers.
- **Stateless applications** are generally easier to scale horizontally.
- **Caching** reduces repeated work and backend load.
- **CDNs** can reduce traffic reaching origin servers for static content.
- **Database replication and sharding** are ways to increase database capacity.
- **Message queues** allow suitable work to be processed asynchronously.
- **Auto scaling** can adjust infrastructure as demand changes.
- Adding more servers does not fix every problem; **find the bottleneck first**.
- Large systems usually combine several scaling techniques.
- More scalability often means more architectural complexity.

> **The key idea:** Scalability is not simply about adding more servers. It is about designing the system so that its capacity can grow as the workload grows.
