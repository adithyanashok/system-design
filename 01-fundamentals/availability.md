# Availability

## 1. What is Availability?

**Availability is the ability of a system to remain accessible and usable when users need it.**

In simple terms:

> **Availability answers the question: "Is my system working when I need it?"**

If a user opens an application and can use it normally, the system is available.

If the application is down or cannot handle the request:

```text
User
  |
  v
Application
  |
  X
Unavailable
```

A system does not have to run perfectly forever to be considered available. The goal is to keep downtime as low as the requirements demand.

---

## 2. Why Does Availability Matter?

Imagine an online shopping application.

```text
User
  |
  v
E-Commerce Application
  |
  v
Place Order
```

If the application is unavailable, the user cannot:

- Browse products
- Add items to a cart
- Place an order
- Make a payment
- Check an order

For some systems, downtime can be much more serious.

For example:

- A banking application may need to be available for transactions.
- A payment service may need to be available for businesses to accept payments.
- A messaging application needs to be available when users want to communicate.

So as an application becomes more important, availability becomes an important system-design requirement.

---

## 3. Availability vs Uptime

You will often hear **availability** and **uptime** together.

### Uptime

The amount of time a system is running.

### Availability

How reliably users can access and use the system over a period of time.

Availability is normally expressed as a percentage:

```text
99%
99.9%
99.99%
99.999%
```

The more `9`s there are, the less downtime is acceptable.

---

## 4. Calculating Availability

A simple formula is:

```text
Availability =
(Uptime / Total Time) × 100
```

For example:

```text
Total time = 100 hours
Downtime   = 1 hour

Uptime = 99 hours
```

Therefore:

```text
Availability = (99 / 100) × 100

             = 99%
```

---

## 5. What Do the "Nines" Mean?

The percentage becomes easier to understand when we convert it into possible downtime.

| Availability | Approx. downtime per year |
|---|---:|
| 90% | ~36.5 days |
| 99% | ~3.65 days |
| 99.9% | ~8.76 hours |
| 99.99% | ~52.6 minutes |
| 99.999% | ~5.26 minutes |
| 99.9999% | ~31.5 seconds |

For example:

```text
99%
```

sounds good, but it can still mean around **3.65 days of downtime per year**.

For a critical service, that might not be acceptable.

---

## 6. Availability Is More Than "The Server Is Running"

A server can be running while the application is effectively unavailable.

For example:

```text
Server       → Running
Application  → Running
Database     → Down
```

Users may still be unable to use the application.

Another example:

```text
Server       → Running
Database     → Running
Application  → Extremely slow
```

The infrastructure may technically be up, but the user experience is still poor.

So the important question is:

> **Can the user actually perform the operation they need?**

---

## 7. What Can Cause Downtime?

There are many possible causes.

### Hardware failure

A server or storage device can fail.

### Software failure

A bug can crash an application.

### Database failure

If the database becomes unavailable, important application features may stop working.

### Network failure

The servers may be healthy, but users may not be able to reach them.

### Bad deployments

A faulty release can introduce errors or bring down a service.

### Traffic spikes

A sudden increase in traffic can overload a system.

```text
Normal traffic
      |
      v
Application

Huge traffic
      |
      v
Overloaded application
```

### Dependency failure

Your application may depend on another service.

```text
Your Application
       |
       v
Payment Service
       |
       X
```

Your own servers may be healthy, but payment functionality can still be unavailable.

---

## 8. Single Point of Failure

A **Single Point of Failure (SPOF)** is a component whose failure can make the entire system, or an important part of it, unavailable.

For example:

```text
Users
  |
  v
Server
  |
  v
Database
```

If the only server fails:

```text
Server
  |
  X
Application unavailable
```

That server is a single point of failure.

---

## 9. Removing a Single Point of Failure

Instead of depending on one server:

```text
Users
  |
  v
Server
```

we can use multiple servers:

```text
             Users
               |
               v
        Load Balancer
          /        \
         v          v
     Server 1    Server 2
```

If Server 1 fails:

```text
Server 1 → Failed
Server 2 → Healthy
```

the load balancer can send requests to Server 2.

The system can continue serving users.

---

## 10. Redundancy

**Redundancy** means having additional components that can continue the work when another component fails.

For example:

```text
Primary Server
      +
Backup Server
```

Or:

```text
Server 1
Server 2
Server 3
```

The idea is:

> **Don't depend on a single component when its failure would cause downtime.**

Redundancy can exist at different levels:

```text
Application Servers
        |
        v
Load Balancers
        |
        v
Database
        |
        v
Storage
        |
        v
Network
        |
        v
Data Center / Region
```

---

## 11. High Availability

**High Availability (HA)** means designing a system to minimize downtime and continue operating when individual components fail.

A simple example:

```text
                 Users
                   |
                   v
             Load Balancer
              /         \
             v           v
         Server 1     Server 2
             \           /
              \         /
                Database
```

If Server 1 fails:

```text
Server 1 → Failed
Server 2 → Healthy
```

Server 2 can continue serving requests.

The goal is not to make failure impossible.

> **The goal is to handle failures without unnecessarily taking the whole system down.**

---

## 12. Failover

**Failover** is the process of switching from a failed component to another available component.

For example:

```text
Primary
   |
   X
Failed
   |
   v
Backup
```

The backup takes over.

A database might have:

```text
Primary Database
       |
       v
Replica / Standby
```

If the primary fails, the standby may be promoted to become the new primary.

---

## 13. Active-Passive

In an **active-passive** setup, one component handles traffic while another waits as a standby.

```text
          Users
            |
            v
        Primary
            |
            X
          fails
            |
            v
         Backup
```

### Advantages

- Relatively simple
- Easier to reason about
- Useful when standby capacity is enough

### Disadvantages

- Backup resources may sit unused
- Failover can take some time

---

## 14. Active-Active

In an **active-active** setup, multiple components serve traffic at the same time.

```text
             Users
               |
        +------+------+
        |             |
        v             v
     Server 1      Server 2
       Active        Active
```

If Server 1 fails:

```text
Server 1 → Failed
Server 2 → Active
```

Server 2 can continue serving users.

### Advantages

- Resources are actively used
- Better resource utilization
- No need to start a completely idle standby

### Disadvantages

- More complex
- Shared state can be harder to manage
- Data consistency can become more difficult

---

## 15. Health Checks

A load balancer needs a way to know whether a server is healthy.

It can use **health checks**.

For example:

```text
Load Balancer
      |
      +----> Server 1
      |
      +----> Server 2
      |
      +----> Server 3
```

It may periodically call:

```text
GET /health
```

A healthy server might return:

```text
200 OK
```

If Server 2 stops responding correctly:

```text
Server 1 → Healthy
Server 2 → Unhealthy
Server 3 → Healthy
```

the load balancer can stop sending traffic to Server 2.

Health checks are an important part of failover.

---

## 16. Replication

**Replication** means keeping copies of data on multiple systems.

For example:

```text
Primary Database
       |
       +------> Replica 1
       |
       +------> Replica 2
```

If the primary database fails, a replica may be used for recovery or promoted to become the new primary.

Replication improves resilience, but it creates another question:

> **How do we keep all the copies up to date?**

This leads to concepts such as:

- Synchronous replication
- Asynchronous replication
- Strong consistency
- Eventual consistency
- Replication lag

---

## 17. Synchronous vs Asynchronous Replication

### Synchronous Replication

The system waits for the replica to confirm the write.

```text
Application
    |
    v
Primary
    |
    v
Replica
    |
    v
Confirm
    |
    v
Application
```

This can provide stronger consistency, but it can increase latency.

### Asynchronous Replication

The primary does not wait for the replica before responding.

```text
Application
    |
    v
Primary
    |
    +------> Replica
```

The response can be faster, but the replica can temporarily be behind.

That delay is called **replication lag**.

---

## 18. Database Availability

The database is often one of the most important components to protect.

Without redundancy:

```text
Application
     |
     v
Database
     |
     X
```

The application may lose important functionality.

A more resilient design could use replicas:

```text
              Application
                   |
                   v
             Primary DB
              /      \
             v        v
         Replica 1  Replica 2
```

If the primary fails, a replica may be promoted.

The exact design depends on the database technology and the availability requirements.

---

## 19. Availability vs Scalability

These concepts are related, but they answer different questions.

### Scalability

> **Can the system handle more workload?**

### Availability

> **Can the system remain usable when something fails?**

For example:

```text
             Users
               |
               v
        Load Balancer
          /        \
         v          v
      Server 1   Server 2
```

Multiple servers can help with both:

```text
More traffic       → Scalability
Server failure     → Availability
```

But they are not the same requirement.

---

## 20. Availability vs Reliability

These are also easy to mix up.

### Availability

> Is the system accessible when users need it?

### Reliability

> Does the system consistently perform the correct operation?

A service can be available but still return incorrect results.

For example:

```text
Service → Responds successfully
Service → Returns incorrect data
```

It is available, but that doesn't make it reliable.

---

## 21. Availability vs Fault Tolerance

**Fault tolerance** is the ability of a system to continue operating despite certain failures.

For example:

```text
Server 1 → Failed
Server 2 → Healthy
Server 3 → Healthy
```

If the system continues operating, it is tolerating the failure of Server 1.

A simple relationship is:

```text
Fault Tolerance
       |
       v
Survive certain failures
       |
       v
Better Availability
```

They are closely related, but they are not identical concepts.

---

## 22. Graceful Degradation

Sometimes a system cannot keep every feature working.

Instead of failing completely, it can keep the most important features available.

This is called **graceful degradation**.

For example:

```text
Recommendation Service → Failed
```

Instead of:

```text
Entire Website → Failed
```

the application can continue:

```text
Product browsing → Available
Cart             → Available
Checkout         → Available
Recommendations  → Unavailable
```

This is much better for the user.

> **If one feature fails, don't let the entire application fail unless you have to.**

---

## 23. Reducing the Blast Radius

A failure should ideally affect as little of the system as possible.

Imagine:

```text
Users
  |
  +---- Product Service
  |
  +---- Order Service
  |
  +---- Payment Service
  |
  +---- Notification Service
```

If the notification service fails:

```text
Notification → Failed
```

we want:

```text
Products       → Available
Orders         → Available
Payments       → Available
Notifications  → Unavailable
```

rather than:

```text
Everything → Unavailable
```

This is about reducing the **blast radius** of failures.

---

## 24. Redundancy at Different Levels

It is not enough to duplicate only application servers.

Consider:

```text
                 Users
                   |
                   v
              Load Balancer
                   |
          +--------+--------+
          |                 |
       Server             Server
          |                 |
          +--------+--------+
                   |
                 Database
```

What happens if the load balancer fails?

You have simply moved the single point of failure.

A more resilient design might use redundant load balancers as well:

```text
                 Users
                   |
          +--------+--------+
          |                 |
          v                 v
      Load Balancer 1   Load Balancer 2
          |                 |
          +--------+--------+
                   |
          +--------+--------+
          |                 |
       Server             Server
```

The same thinking applies to:

- Application servers
- Load balancers
- Databases
- Storage
- Network paths
- Data centers
- Cloud regions

---

## 25. Multi-AZ Architecture

Cloud providers commonly offer multiple **Availability Zones (AZs)** inside a region.

Instead of putting everything in one zone:

```text
Region

AZ 1
 |
 +-- Application
 +-- Database
```

components can be distributed:

```text
             Region
          /          \
        AZ 1         AZ 2
         |            |
      Server 1      Server 2
         |            |
         +-----+------+
               |
            Database
```

If one zone has a problem:

```text
AZ 1 → Failed
AZ 2 → Healthy
```

the application may continue operating from the remaining zone, assuming the architecture supports it.

---

## 26. Multi-Region Availability

For systems with very high availability requirements, infrastructure can be deployed across multiple geographic regions.

For example:

```text
                 Users
                   |
             Global Routing
              /           \
             v             v
        Region A        Region B
           |               |
        Servers          Servers
           |               |
        Database         Database
```

If Region A becomes unavailable:

```text
Region A → Failed
Region B → Available
```

traffic can potentially be directed to Region B.

This protects against larger failures, but the architecture becomes much more complicated.

---

## 27. Multi-Region Challenges

Running in multiple regions introduces several problems.

### Data replication

How does data move between regions?

### Consistency

How quickly do changes appear in another region?

### Network latency

Regions are geographically separated, so communication takes time.

### Conflicts

What happens if the same data changes in two regions?

### Cost

Running infrastructure in multiple regions costs more.

### Operations

There are more systems to deploy, monitor, debug, and maintain.

So multi-region architecture should be introduced when its benefits justify the additional complexity.

---

## 28. Disaster Recovery

Some failures are bigger than a server failure.

For example:

```text
Data center failure
Region failure
Major infrastructure failure
Data corruption
```

A **Disaster Recovery (DR)** strategy defines how the system will recover from serious failures.

It answers questions such as:

- Where are our backups?
- How quickly can we restore the service?
- How much data can we afford to lose?
- Where can the application run if the primary environment is unavailable?

---

## 29. RTO and RPO

Two important disaster-recovery concepts are **RTO** and **RPO**.

### RTO — Recovery Time Objective

RTO answers:

> **How quickly do we need to restore the system after a failure?**

For example:

```text
RTO = 30 minutes
```

The target is to recover the service within about 30 minutes.

### RPO — Recovery Point Objective

RPO answers:

> **How much recent data can we afford to lose?**

For example:

```text
RPO = 5 minutes
```

The system may accept losing up to around five minutes of recent data after a disaster.

Easy way to remember:

```text
RTO → How quickly must we recover?

RPO → How much data can we lose?
```

---

## 30. Backups

Backups are an important part of disaster recovery.

```text
Production Database
        |
        v
      Backup
        |
        v
     Storage
```

If the production database is lost or corrupted, the backup can be used to restore data.

But:

> **A backup is not the same as high availability.**

A backup helps you **recover**.

High availability tries to keep the system **running despite failures**.

---

## 31. Monitoring

You cannot maintain availability if you don't know when something is failing.

Useful metrics include:

- Error rate
- Response time
- CPU usage
- Memory usage
- Database health
- Request volume
- Failed requests
- Server health
- Queue depth

For example:

```text
Error rate
   |
   |        /
   |       /
   |______/
          Time
```

A sudden increase in errors can indicate a problem.

Monitoring helps engineers detect problems and respond quickly.

---

## 32. Alerts

Monitoring collects information.

**Alerts** notify engineers when something needs attention.

For example:

```text
Error rate > 5%
       |
       v
     Alert
       |
       v
Engineer investigates
```

Alerts should be meaningful.

If engineers receive too many unnecessary alerts, they can develop **alert fatigue**, making important alerts easier to miss.

---

## 33. Availability in System Design

When designing a system, don't simply say:

> "The system should be highly available."

Define what that means.

For example:

```text
Availability target = 99.99%
```

Then ask:

```text
What can fail?
      |
      v
How do we detect it?
      |
      v
What happens after failure?
      |
      v
How quickly can we recover?
```

This turns availability into something that can actually be designed and measured.

---

## 34. A Simple Highly Available Architecture

A simplified architecture might look like:

```text
                         Users
                           |
                           v
                    Load Balancer
                    /           \
                   v             v
              Server 1       Server 2
                   |             |
                   +------+------+
                          |
                        Cache
                          |
                          v
                    Primary DB
                       /   \
                      v     v
                 Replica 1 Replica 2
```

Possible benefits:

- Multiple application servers reduce dependence on one server.
- A load balancer distributes traffic.
- Cache can reduce database load.
- Database replicas provide additional copies of data.
- Failover can allow another component to take over.

This is only a simplified architecture. Real systems need to consider many more failure scenarios.

---

## 35. What Happens When a Server Fails?

Normally:

```text
Users
  |
  v
Load Balancer
  |
  +----> Server 1
  |
  +----> Server 2
```

Now Server 1 fails:

```text
Users
  |
  v
Load Balancer
  |
  +----> Server 1 → Failed
  |
  +----> Server 2 → Healthy
```

The health check detects the failure.

The load balancer stops sending new requests to Server 1:

```text
Users
  |
  v
Load Balancer
  |
  +----> Server 2
```

The user may not even notice that one server failed.

That is one of the basic goals of high availability.

---

## 36. Availability Is a Trade-off

Higher availability usually means higher cost and more complexity.

A simple system:

```text
1 Server
```

is easy to operate.

But:

```text
Server fails
    |
    v
Downtime
```

A more resilient system might have:

```text
Multiple Servers
Load Balancer
Database Replication
Monitoring
Failover
Backups
```

Going further:

```text
Multi-AZ
Multi-Region
Global Routing
Cross-Region Replication
```

can provide stronger resilience, but the system becomes significantly harder and more expensive to operate.

So the goal isn't:

> **"Get the highest availability possible."**

The goal is:

> **"Choose an availability level that makes sense for the system."**

---

## 37. Availability vs Cost

Different applications have different requirements.

For example:

```text
Personal Project
99%
Low cost
```

```text
Business Application
99.9%+
Higher cost
```

```text
Critical Service
99.99%+
Much higher requirements
```

The exact target depends on the business.

Think about it as:

```text
Business Requirement
        |
        v
Availability Target
        |
        v
Architecture
        |
        v
Cost + Complexity
```

---

## 38. Common Mistakes

### Mistake 1: One backup server automatically makes a system highly available

Not necessarily.

You also need to consider:

- Load balancers
- Databases
- Networks
- Storage
- Shared dependencies

---

### Mistake 2: Backups provide high availability

Backups help with recovery.

They don't necessarily keep the application running while the failure is happening.

---

### Mistake 3: More servers automatically mean higher availability

Consider:

```text
Server 1 ─┐
Server 2 ─┼──→ One Database
Server 3 ─┘
```

If the database fails, all three servers may be affected.

The database is still a single point of failure.

---

### Mistake 4: 100% availability is realistic

Failures can happen because of:

- Hardware problems
- Software bugs
- Network failures
- Human mistakes
- Infrastructure problems

The practical goal is to **minimize downtime and recover from failures quickly**.

---

## 39. How to Think About Availability

When designing a system, ask:

### 1. What are the critical components?

```text
Application?
Database?
Cache?
Queue?
External services?
```

### 2. What happens if each one fails?

```text
Server fails   → ?
Database fails → ?
Network fails  → ?
Region fails   → ?
```

### 3. Do we have redundancy?

```text
One server?
Multiple servers?
Multiple zones?
Multiple regions?
```

### 4. How do we detect failures?

```text
Health checks
Monitoring
Alerts
```

### 5. How do we recover?

```text
Failover
Restart
Replacement
Backup restoration
```

### 6. How much downtime is acceptable?

```text
99%
99.9%
99.99%
99.999%
```

### 7. How much data loss is acceptable?

```text
RPO
```

### 8. How quickly must we recover?

```text
RTO
```

---

## 40. Advanced: Dependency Failures

Your application may depend on services you don't control.

For example:

```text
Your Application
      |
      +---- Payment API
      |
      +---- Email API
      |
      +---- Maps API
```

If the payment service fails, your application needs to decide what to do.

Possible approaches include:

- Timeout
- Retry
- Circuit breaker
- Fallback
- Queue the operation
- Graceful degradation

Availability has to be considered across the dependency chain, not just inside your own application.

---

## 41. Advanced: Timeouts

Imagine your application calls another service:

```text
Application
    |
    v
External Service
```

The external service stops responding.

Without a timeout:

```text
Request
  |
  v
Waiting...
  |
  v
Waiting...
  |
  v
Waiting...
```

Too many requests waiting can consume application resources.

A timeout limits how long the application waits:

```text
Request
  |
  v
External Service
  |
  X
Timeout
```

The application can then handle the failure instead of waiting indefinitely.

---

## 42. Advanced: Retries

If a temporary failure occurs:

```text
Request
  |
  v
Service
  |
  X
Failure
```

the application may retry:

```text
Request
  |
  v
Service
  |
  X
Retry
  |
  v
Service
```

Retries can help with temporary failures.

But retries can also make an overloaded service even worse.

For example:

```text
Service overloaded
       |
       v
100 requests fail
       |
       v
100 requests retry
       |
       v
More load
       |
       v
More failures
```

This is why retries should usually have limits and use techniques such as **exponential backoff** and **jitter**.

---

## 43. Advanced: Circuit Breaker

A **circuit breaker** prevents an application from repeatedly calling a service that is already failing.

For example:

```text
Application
     |
     v
Payment Service
```

After repeated failures, the circuit can open:

```text
Application
     |
     X
Circuit Breaker
     |
     X
Payment Service
```

The application can fail fast or use a fallback instead of continuously sending requests to the failing service.

After some time, the circuit can allow requests again to check whether the dependency has recovered.

This helps prevent one failing service from causing problems throughout the system.

---

## 44. Advanced: Bulkheads

The **bulkhead pattern** separates resources so that a problem in one part of the application does not consume everything.

For example:

```text
Application
 |
 +---- Payment resources
 |
 +---- Search resources
 |
 +---- Notification resources
```

If notifications suddenly receive a huge amount of traffic, they should not consume every connection and prevent payments from working.

The idea is:

> **Isolate resources so one failure doesn't bring down unrelated parts of the system.**

---

## 45. Advanced: Queue-Based Resilience

Queues can help when a dependency is temporarily unavailable.

For example:

```text
Application
    |
    v
Message Queue
    |
    v
Worker
    |
    v
External Service
```

If the external service is temporarily unavailable, the message can remain in the queue and be processed later.

Instead of:

```text
User request
    |
    v
External service fails
    |
    v
Request fails
```

the system may be able to accept the work and process it asynchronously.

Whether this is appropriate depends on the operation.

---

## 46. Testing Availability

A system can look highly available on paper and still fail in production.

That's why failure scenarios should be tested.

For example:

```text
What happens if:

Server crashes?
Database becomes unavailable?
Cache goes down?
Network becomes slow?
Region becomes unavailable?
External API stops responding?
```

Testing these scenarios can reveal weaknesses before real failures happen.

For larger systems, this can lead to practices such as **chaos engineering**.

---

## 47. The Bigger Picture

Availability is not one feature that is added at the end.

It comes from many design decisions:

```text
                    Availability
                         |
       +-----------------+-----------------+
       |                 |                 |
   Redundancy         Failover         Monitoring
       |                 |                 |
   Replication       Health Checks       Alerts
       |                 |
   Multi-AZ          Timeouts
       |             Retries
   Multi-Region      Circuit Breaker
       |
   Disaster Recovery
```

Each technique handles a different kind of failure.

---

## 48. Key Mental Model

Think about availability like this:

```text
Something will eventually fail
            |
            v
Identify what can fail
            |
            v
Remove unnecessary single points of failure
            |
            v
Add redundancy
            |
            v
Detect failures
            |
            v
Fail over or recover
            |
            v
Limit the impact of failures
            |
            v
Monitor and test the system
```

The main idea:

> **You don't make a system highly available by assuming nothing will fail. You make it highly available by designing for failure.**

---

## 49. Common Interview Questions

### Beginner

**1. What is availability?**

Availability is the ability of a system to remain accessible and usable when users need it.

**2. What is uptime?**

The amount of time a system remains operational during a given period.

**3. What is high availability?**

Designing a system to minimize downtime and continue operating despite individual component failures.

**4. What is a single point of failure?**

A component whose failure can make the system or an important part of it unavailable.

**5. What is redundancy?**

Having additional components that can continue the work when another component fails.

---

### Intermediate

**6. How can you improve availability?**

Common approaches include:

- Redundancy
- Replication
- Load balancing
- Health checks
- Failover
- Monitoring
- Backups
- Multi-AZ deployment
- Graceful degradation

**7. What is failover?**

Switching from a failed component to another available component.

**8. What is the difference between active-active and active-passive?**

Active-active systems have multiple components serving traffic at the same time. Active-passive systems have a primary component serving traffic while another waits to take over.

**9. How can a load balancer improve availability?**

It can distribute traffic across multiple servers and stop sending traffic to unhealthy servers.

**10. Why isn't a backup enough for high availability?**

A backup helps with recovery, but it doesn't necessarily keep the application running during the failure.

---

### Advanced

**11. How would you design a highly available web application?**

A possible starting point is:

```text
Users
  |
  v
Load Balancer
  |
  v
Multiple Application Servers
  |
  v
Cache
  |
  v
Database + Replicas
```

Then consider:

- Health checks
- Automatic failover
- Monitoring
- Backups
- Multi-AZ deployment
- Disaster recovery

The exact architecture depends on the availability requirements.

**12. What is the difference between RTO and RPO?**

```text
RTO → How quickly must we recover?

RPO → How much data can we afford to lose?
```

**13. How do you handle dependency failures?**

Use techniques such as:

- Timeouts
- Retries
- Exponential backoff
- Circuit breakers
- Fallbacks
- Queues
- Graceful degradation

**14. Why doesn't adding more servers always improve availability?**

Because another component may still be a single point of failure.

For example:

```text
Server 1 ─┐
Server 2 ─┼──→ One Database
Server 3 ─┘
```

If the database fails, all application servers may be affected.

---

## 50. Summary

- **Availability** means the system is accessible and usable when users need it.
- **Uptime** describes how much time the system remains operational.
- Availability is commonly expressed as a percentage such as `99.9%` or `99.99%`.
- A **Single Point of Failure** can make an important part of the system unavailable.
- **Redundancy** reduces dependence on individual components.
- **High Availability** aims to minimize downtime.
- **Failover** allows another component to take over after failure.
- **Replication** provides additional copies of data.
- **Health checks** help detect unhealthy components.
- **Active-active** and **active-passive** are common redundancy approaches.
- **Graceful degradation** allows important functionality to continue when some features fail.
- **Multi-AZ** and **multi-region** deployments can protect against larger infrastructure failures.
- **Backups** help with recovery but are not the same as high availability.
- **RTO** defines how quickly recovery should happen.
- **RPO** defines how much data loss is acceptable.
- **Timeouts, retries, circuit breakers, and bulkheads** help prevent failures from spreading.
- Monitoring and testing are essential for maintaining availability.

> **The main idea: A highly available system is not a system that never fails. It is a system that can handle failures without unnecessarily becoming unavailable.**
