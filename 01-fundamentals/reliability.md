# Reliability

## 1. What is Reliability?

**Reliability is the ability of a system to consistently do what it is supposed to do, correctly, over time.**

In simple words:

> **Reliability asks: "Can I trust this system to keep doing the right thing?"**

For example, imagine an API that calculates an order total:

```text
Request
   |
   v
Order Service
   |
   v
Total = ₹1,500
```

If it consistently returns the correct result, it is behaving reliably.

If it sometimes returns the wrong total or fails unexpectedly, it is not reliable.

---

## 2. Reliability vs Availability

These two concepts are easy to confuse.

### Availability

> **Is the system available to use?**

### Reliability

> **Does the system work correctly and consistently?**

For example:

```text
System responds
       |
       v
Available
```

But if it returns incorrect data:

```text
System responds
       |
       v
Wrong result
```

it is available, but not reliable.

A simple way to remember:

```text
Availability → Is it working?

Reliability  → Is it working correctly and consistently?
```

A good system needs both.

---

## 3. A Simple Example

Imagine a payment system.

A user pays:

```text
₹1,000
```

The system should:

1. Charge the correct amount.
2. Record the payment.
3. Return the correct status.
4. Avoid charging the customer twice.
5. Continue behaving correctly when traffic increases.

If the payment API is reachable but sometimes charges the customer twice:

```text
Available → Yes
Reliable  → No
```

This shows why reliability is more than keeping servers running.

---

## 4. Why Does Reliability Matter?

A small bug in a simple application might be annoying.

A bug in a critical system can cause serious problems.

For example:

- An e-commerce system could create duplicate orders.
- A payment system could charge a customer twice.
- A banking system could show an incorrect balance.
- A messaging system could lose messages.
- A storage system could lose files.

The more important the system, the more carefully reliability needs to be considered.

---

## 5. What Can Make a System Unreliable?

There are many possible causes.

### Software bugs

```text
Bug
 ↓
Incorrect behavior
 ↓
Unreliable system
```

### Hardware failures

Servers, disks, memory, or other hardware can fail.

### Network failures

Requests can be delayed, dropped, or interrupted.

### Data corruption

Corrupted data can lead to incorrect results.

### Dependency failures

Your application may depend on another service:

```text
Application
     |
     v
External Service
     |
     X
```

### Bad deployments

A new release can introduce bugs.

### Configuration mistakes

A wrong configuration can cause unexpected behavior.

### Unexpected load

A system may work correctly under normal traffic but behave badly under heavy traffic.

---

## 6. Reliability Is About Failure

A common mistake is to think:

> "A reliable system is one that never fails."

That's not realistic.

Servers fail.

Networks fail.

Software has bugs.

People make mistakes.

External services go down.

Instead, reliability engineering asks:

> **What happens when something goes wrong?**

A useful flow is:

```text
Failure
   |
   v
Detect it
   |
   v
Handle it
   |
   v
Recover
   |
   v
Learn from it
```

---

## 7. Reliability vs Performance

These are different things.

### Performance

How quickly does the system respond?

```text
Request → Response
          50ms
```

### Reliability

Does the system consistently produce the correct result?

A system can be:

```text
Very fast
+
Incorrect
```

That is not reliable.

Another system can be:

```text
Correct
+
Very slow
```

It may be reliable but have poor performance.

Ideally, we want:

```text
Correct
+
Consistent
+
Fast enough
```

---

## 8. Reliability vs Scalability

### Scalability

> Can the system handle more workload?

### Reliability

> Can the system continue doing the correct thing as time and conditions change?

For example:

```text
1,000 users
   |
   v
System works correctly
```

Then:

```text
100,000 users
   |
   v
System still works correctly
```

Handling the increased load is a scalability concern.

Making sure the system continues behaving correctly under that load is also a reliability concern.

The two often need to be designed together.

---

## 9. Reliability vs Fault Tolerance

These concepts are related.

### Fault tolerance

The ability to continue operating when certain components fail.

Example:

```text
Server 1 → Failed
Server 2 → Healthy
```

The system continues working.

### Reliability

The broader goal of consistently providing the correct behavior over time.

So:

```text
Fault Tolerance
      |
      v
Handle certain failures
      |
      v
Helps improve Reliability
```

Fault tolerance is one of the techniques used to build reliable systems.

---

## 10. Reliability and Redundancy

One way to improve reliability is to avoid depending on a single component.

Instead of:

```text
Application
    |
    v
One Server
```

use:

```text
Application
    |
    v
+-----------+
| Server 1  |
| Server 2  |
| Server 3  |
+-----------+
```

If one server fails:

```text
Server 1 → Failed
Server 2 → Healthy
Server 3 → Healthy
```

the system can continue operating.

Redundancy reduces the impact of individual failures.

---

## 11. Reliability and Replication

The same idea applies to data.

Instead of keeping only one copy:

```text
Database
   |
   v
One copy of data
```

we can replicate the data:

```text
Primary
   |
   +----> Replica 1
   |
   +----> Replica 2
```

If one copy becomes unavailable, another copy may still exist.

But replication introduces its own concerns:

- Replication lag
- Data consistency
- Failover
- Conflicting writes
- Split-brain scenarios

Replication can improve reliability, but it has to be designed carefully.

---

## 12. Data Integrity

A reliable system must protect the correctness of its data.

Suppose a bank account has:

```text
Balance = ₹10,000
```

A withdrawal of ₹2,000 should result in:

```text
Balance = ₹8,000
```

The system should not accidentally produce:

```text
Balance = ₹12,000
```

or another incorrect value.

This is why reliable systems care about:

- Validation
- Transactions
- Constraints
- Consistency
- Atomic operations
- Data recovery

---

## 13. Atomic Operations

An operation is **atomic** when it happens completely or not at all.

Consider transferring money:

```text
Account A
   |
   v
-₹1,000

Account B
   |
   v
+₹1,000
```

We don't want this:

```text
Account A
   |
   v
-₹1,000
```

and then the application crashes before updating Account B.

The system should behave like:

```text
Both changes happen
       OR
Neither change happens
```

This is an important part of reliable data processing.

---

## 14. Idempotency

**Idempotency** means that performing the same operation multiple times produces the same intended result as performing it once.

This is especially useful when requests are retried.

Imagine:

```text
Client
  |
  v
Payment API
```

The client sends:

```text
Pay ₹1,000
```

The payment succeeds, but the response is lost.

The client doesn't know whether the payment happened, so it retries.

Without protection:

```text
Request 1 → Charge ₹1,000
Request 2 → Charge ₹1,000
```

The customer could be charged twice.

With an idempotency key:

```text
Request 1
Key = abc123
```

Retry:

```text
Request 2
Key = abc123
```

The payment system can recognize that both requests represent the same operation and avoid creating a duplicate charge.

Idempotency is very useful for reliable APIs, especially for operations involving money or other important side effects.

---

## 15. Retries

Temporary failures happen.

For example:

```text
Application
     |
     v
Service
     |
     X
Temporary failure
```

The application may retry:

```text
Application
     |
     v
Service
     |
     X
Retry
     |
     v
Service
     |
     v
Success
```

Retries can improve reliability when failures are temporary.

But retries can also make a bad situation worse.

Suppose a service is already overloaded:

```text
100 requests
     |
     v
Service overloaded
     |
     v
100 failures
     |
     v
100 retries
```

Now the service receives even more traffic.

This can create a **retry storm**.

So retries should normally have:

- A maximum number of attempts
- Timeouts
- Exponential backoff
- Jitter

---

## 16. Exponential Backoff

Instead of retrying immediately every time, the client waits longer between attempts.

For example:

```text
1st retry → wait 100ms
2nd retry → wait 200ms
3rd retry → wait 400ms
4th retry → wait 800ms
```

The exact values depend on the system.

The idea is to give the failing service some time to recover.

---

## 17. Timeouts

A service shouldn't wait forever for another service.

For example:

```text
Application
    |
    v
Payment Service
    |
    X
No response
```

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

With a timeout:

```text
Request
   |
   v
Payment Service
   |
   X
Timeout
```

The application can then decide what to do next.

Timeouts are a basic building block of reliable distributed systems.

---

## 18. Circuit Breaker

A **circuit breaker** prevents an application from repeatedly calling a service that is failing.

For example:

```text
Application
     |
     v
Payment Service
     |
     X
Failing
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

Requests can fail quickly or use a fallback instead of continuously hitting the failing service.

After some time, the circuit breaker can allow a few requests through to check whether the service has recovered.

---

## 19. Graceful Degradation

A reliable system doesn't always need every feature to work.

Suppose a shopping application has:

```text
Product Search
Cart
Checkout
Recommendations
```

The recommendation service fails:

```text
Recommendations → Unavailable
```

The application can still provide:

```text
Product Search → Available
Cart           → Available
Checkout       → Available
Recommendations → Unavailable
```

This is **graceful degradation**.

The important parts of the system continue working even though one feature has failed.

> **If one feature fails, don't let the entire application fail unless you have to.**

---

## 20. Isolation and Blast Radius

A failure in one component should not unnecessarily spread to other components.

For example:

```text
Product Service
Order Service
Payment Service
Notification Service
```

If notifications fail:

```text
Notification Service → Failed
```

we don't want:

```text
Product Service → Failed
Order Service → Failed
Payment Service → Failed
```

Isolation limits the **blast radius** of a failure.

This can be achieved through:

- Separate resources
- Queues
- Timeouts
- Circuit breakers
- Bulkheads
- Service boundaries

---

## 21. Bulkhead Pattern

The **bulkhead pattern** separates resources so one part of the system cannot consume everything.

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

If notification traffic suddenly becomes huge, it should not consume every available connection.

Payment processing still has its own resources.

The idea is:

> **Isolate resources so one failure doesn't bring down unrelated parts of the system.**

---

## 22. Message Queues and Reliability

Message queues can make systems more resilient.

Instead of directly depending on another service:

```text
Application
     |
     v
Worker Service
```

we can introduce a queue:

```text
Application
     |
     v
Message Queue
     |
     v
Worker
```

If the worker is temporarily unavailable, messages can remain in the queue and be processed later.

This can help absorb temporary failures and traffic spikes.

But queues introduce their own concerns:

- Duplicate messages
- Message ordering
- Failed messages
- Retries
- Consumer failures
- Dead-letter queues

---

## 23. Dead-Letter Queues

Sometimes a message keeps failing.

For example:

```text
Message
   |
   v
Worker
   |
   X
Failure
   |
   v
Retry
   |
   X
Failure
   |
   v
Retry
```

We don't want to retry the same broken message forever.

After a certain number of attempts, it can be moved to a **Dead-Letter Queue (DLQ)**:

```text
Message
   |
   v
Worker
   |
   X
Repeated failures
   |
   v
Dead-Letter Queue
```

Engineers can inspect the message later and determine what went wrong.

---

## 24. Reliable Deployments

A system can be reliable today and become unreliable after a deployment.

That's why deployment strategy matters.

A simple deployment might replace everything at once:

```text
Old Version
     |
     v
New Version
```

If the new version has a serious bug, many users can be affected.

More careful approaches include:

### Rolling deployment

Update instances gradually:

```text
Old Old Old Old
 ↓
New Old Old Old
 ↓
New New Old Old
 ↓
New New New Old
 ↓
New New New New
```

### Blue-Green deployment

Maintain two environments:

```text
Blue  → Current
Green → New
```

Traffic can be switched to Green after testing.

### Canary deployment

Send a small percentage of traffic to the new version first:

```text
95% → Old version
5%  → New version
```

If the new version behaves correctly, increase its traffic gradually.

These approaches reduce the risk of a bad deployment affecting everyone.

---

## 25. Observability

Reliable systems need to be observable.

Observability helps engineers understand what is happening inside a system.

Three common areas are:

### Metrics

Numerical measurements:

```text
CPU usage
Request rate
Error rate
Latency
Queue depth
```

### Logs

Detailed records of events:

```text
2026-08-31 18:20:10
Payment request failed
Order ID: 12345
```

### Traces

Follow a request across multiple services:

```text
Client
  |
  v
API Gateway
  |
  v
Order Service
  |
  v
Payment Service
  |
  v
Database
```

Together, these make production problems easier to investigate.

---

## 26. Testing for Reliability

A system should not only be tested when everything is working normally.

Failure scenarios should be tested too.

For example:

```text
What happens if:

Database goes down?
Server crashes?
Network becomes slow?
External API fails?
Queue becomes full?
Messages are duplicated?
A deployment goes wrong?
```

Testing these cases helps find problems before users encounter them.

---

## 27. Chaos Engineering

At larger scales, teams may intentionally introduce controlled failures.

This is called **chaos engineering**.

For example:

```text
Take one server offline
        |
        v
Observe the system
        |
        v
Does traffic move elsewhere?
        |
        v
Does the system recover?
```

The purpose is to verify that the system behaves as expected during failures.

It should be done in a controlled way with clear safety boundaries.

---

## 28. Backups and Recovery

Reliable systems should have a way to recover lost or corrupted data.

For example:

```text
Production Database
        |
        v
      Backup
        |
        v
   Backup Storage
```

Backups protect against problems such as:

- Accidental deletion
- Data corruption
- Hardware failure
- Disaster scenarios

But:

> **A backup is not the same as high availability.**

A backup helps you recover.

High availability tries to keep the system running despite failures.

---

## 29. Recovery

When something goes wrong, the system needs a recovery process.

A simple flow is:

```text
Failure
   |
   v
Detect
   |
   v
Contain
   |
   v
Recover
   |
   v
Verify
   |
   v
Learn and improve
```

A mature reliability process doesn't stop after restoring the service.

The next question is:

> **Why did it happen, and what can we change so it is less likely to happen again?**

---

## 30. Incident Management

When a serious failure occurs, teams need a clear process.

A typical flow is:

```text
Detect incident
      |
      v
Understand impact
      |
      v
Mitigate
      |
      v
Restore service
      |
      v
Investigate
      |
      v
Improve the system
```

After an incident, teams often perform a **postmortem**.

A good postmortem focuses on learning and improving the system rather than simply blaming someone.

---

## 31. Reliability Metrics

Reliability needs to be measured.

Useful metrics include:

### Error rate

```text
Failed requests / Total requests
```

### Success rate

```text
Successful requests / Total requests
```

### MTTD — Mean Time To Detect

How long it takes to detect a problem.

### MTTR — Mean Time To Recovery

How long it takes to restore the system after a failure.

These measurements help teams understand whether reliability is improving.

---

## 32. SLI, SLO and SLA

These terms are common in production systems.

### SLI — Service Level Indicator

A measurement of actual system behavior.

For example:

```text
99.95% of requests succeeded
```

### SLO — Service Level Objective

The target you want to achieve.

For example:

```text
99.9% successful requests
```

### SLA — Service Level Agreement

A formal agreement with customers about service expectations, often including consequences if the target isn't met.

Easy way to remember:

```text
SLI → What we measure

SLO → What we aim for

SLA → What we promise
```

---

## 33. Error Budgets

Suppose the SLO is:

```text
99.9% availability
```

The remaining:

```text
0.1%
```

is the allowed failure or downtime budget.

This is called an **error budget**.

The idea is that reliability has a cost.

A team doesn't necessarily need to eliminate every tiny failure.

Instead:

```text
SLO
 ↓
Error Budget
 ↓
Balance reliability and development speed
```

If the team is consuming the error budget too quickly, it may need to focus more on reliability work and be more careful with risky changes.

---

## 34. Reliability at Scale

As a system grows, reliability becomes harder.

A small application might have:

```text
1 Application
1 Database
```

A large system might have:

```text
Many services
Many servers
Multiple databases
Caches
Queues
External APIs
Multiple regions
```

There are now many more things that can fail.

```text
More components
      |
      v
More possible failure points
      |
      v
More complex failure handling
```

This is one reason distributed systems require careful reliability engineering.

---

## 35. Reliability Is a System Property

Reliability is not just the responsibility of one component.

Consider:

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
Cache
  |
  v
Database
  |
  v
External Service
```

If an important dependency behaves incorrectly, the overall user experience can be affected.

So reliability needs to be considered across the entire request path.

---

## 36. A Practical Reliability Checklist

When designing a system, ask:

```text
[ ] What can fail?
[ ] What happens when it fails?
[ ] Are there single points of failure?
[ ] Do we have redundancy?
[ ] Can data be recovered?
[ ] Are important operations idempotent?
[ ] Do requests have timeouts?
[ ] Are retries controlled?
[ ] Do retries use backoff?
[ ] Can failures spread to other services?
[ ] Can the system degrade gracefully?
[ ] Are important resources isolated?
[ ] Do we have monitoring?
[ ] Do we have useful alerts?
[ ] Can we roll back a bad deployment?
[ ] Are backups tested?
[ ] Have we tested failure scenarios?
[ ] Do we have an incident response process?
[ ] Do we know our SLOs?
[ ] Are RTO and RPO defined where needed?
```

---

## 37. How to Think About Reliability

When designing a system, don't only ask:

> "Will this work?"

Also ask:

> **"What happens when it doesn't work?"**

A useful thought process is:

```text
Normal operation
       |
       v
What can fail?
       |
       v
How will we detect it?
       |
       v
How will we handle it?
       |
       v
Can we recover?
       |
       v
Can the failure spread?
       |
       v
How can we reduce the chance of it happening again?
```

This mindset is at the heart of reliability engineering.

---

## 38. Example: Reliable Payment System

Suppose we are designing a payment service.

A basic flow:

```text
User
  |
  v
Payment API
  |
  v
Payment Service
  |
  v
Payment Provider
```

To make it more reliable, we might consider:

### Timeout

Don't wait forever for the payment provider.

### Retry

Retry temporary failures carefully.

### Idempotency

Don't charge the customer twice if the request is repeated.

### Database transaction

Record payment state correctly.

### Queue

Use asynchronous processing where appropriate.

### Monitoring

Track payment failures and latency.

### Reconciliation

Compare our payment records with the provider's records and investigate mismatches.

The important point is that reliability comes from many design decisions working together.

---

## 39. Key Mental Model

Remember reliability as:

```text
Something will eventually go wrong
              |
              v
       Expect the failure
              |
              v
       Detect the failure
              |
              v
       Limit its impact
              |
              v
        Recover safely
              |
              v
       Verify correctness
              |
              v
       Learn and improve
```

The main idea:

> **Reliable systems are designed with failure in mind, not built on the assumption that everything will always work.**

---

## 40. Common Interview Questions

### Beginner

**1. What is reliability?**

Reliability is the ability of a system to consistently perform its intended function correctly over time.

**2. What is the difference between reliability and availability?**

Availability asks whether the system is accessible. Reliability asks whether it consistently works correctly.

**3. What can make a system unreliable?**

Software bugs, hardware failures, network problems, bad deployments, data corruption, dependency failures, and configuration mistakes are some examples.

**4. What is fault tolerance?**

The ability of a system to continue operating despite certain component failures.

---

### Intermediate

**5. How can you improve reliability?**

Common approaches include:

- Redundancy
- Replication
- Timeouts
- Controlled retries
- Idempotency
- Circuit breakers
- Graceful degradation
- Backups
- Monitoring
- Testing

**6. Why are retries dangerous?**

Retries can increase load on an already failing service and potentially create a retry storm.

**7. What is idempotency and why is it useful?**

It allows repeated requests to produce the same intended result, which is particularly useful when requests are retried.

**8. What is graceful degradation?**

Keeping important functionality working even when some less-critical functionality fails.

**9. What is the purpose of a circuit breaker?**

To stop repeatedly calling a failing dependency and prevent the failure from spreading.

---

### Advanced

**10. How would you design a reliable distributed system?**

Start by identifying failure points and dependencies. Then use appropriate techniques such as redundancy, replication, timeouts, controlled retries, idempotency, isolation, graceful degradation, observability, tested recovery procedures, and safe deployments.

**11. What are SLI, SLO and SLA?**

```text
SLI → What we measure

SLO → The target

SLA → The formal commitment
```

**12. What is an error budget?**

The amount of failure or unreliability allowed by an SLO.

**13. How do you prevent one service failure from affecting the entire system?**

Use techniques such as:

- Timeouts
- Circuit breakers
- Bulkheads
- Queues
- Isolation
- Graceful degradation

**14. Why is idempotency important in distributed systems?**

Requests can be retried or delivered more than once. Idempotency helps prevent duplicate side effects.

**15. How do you test reliability?**

Test normal behavior as well as failure scenarios, recovery procedures, deployments, dependency failures, data recovery, and, where appropriate, controlled failure injection.

---

## 41. Summary

- **Reliability** means consistently doing the correct thing over time.
- **Availability** means being accessible when users need the system.
- A system can be available but unreliable if it returns incorrect results.
- Failures are inevitable, so reliable systems are designed around failure.
- **Redundancy** reduces dependence on individual components.
- **Replication** provides additional copies of data.
- **Idempotency** prevents retries from causing duplicate side effects.
- **Timeouts** prevent requests from waiting forever.
- **Retries** can help with temporary failures but must be controlled.
- **Exponential backoff and jitter** help prevent retry storms.
- **Circuit breakers** stop repeated calls to failing dependencies.
- **Graceful degradation** keeps important functionality working during partial failures.
- **Bulkheads and isolation** limit the blast radius of failures.
- **Queues** can help absorb temporary failures and traffic spikes.
- **Dead-letter queues** handle messages that repeatedly fail.
- Safe deployment strategies reduce the risk of introducing failures.
- **Observability** helps engineers understand and debug production problems.
- **Backups and recovery procedures** protect against data loss and disasters.
- **SLIs, SLOs, SLAs and error budgets** help measure and manage reliability.
- Reliability is a property of the **whole system**, not just one server.

> **The main idea: Reliability is not about building a system where nothing ever fails. It is about building a system that behaves correctly, handles failures safely, and recovers when things go wrong.**
