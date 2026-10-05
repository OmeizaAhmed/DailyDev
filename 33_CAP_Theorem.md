# CAP Theorem: The Trade-Off Behind Distributed Systems

What if your database has multiple servers, and they suddenly can’t communicate with each other?

Should the system keep accepting requests, even if some servers might have outdated data?

Or should it reject requests until everything is synchronized?

This is where the **CAP Theorem** comes in.

## What Is CAP Theorem?

CAP states that a distributed system can guarantee at most **two out of three properties**:

* **Consistency**
* **Availability**
* **Partition Tolerance**

Let's break them down.

### 1. Consistency

Every read gets the **most recent write**.

Imagine you update your bank balance from ₦50,000 to ₦40,000.

With strong consistency, every server should immediately return:

> ₦40,000

No server should return the old ₦50,000.

### 2. Availability

Every request receives a response, even when some parts of the system are failing.

If one server goes down, another server should still be able to respond.

The system prioritizes staying operational.

### 3. Partition Tolerance

The system continues working even when communication between servers is interrupted.

For example:

```text
Server A  X  Server B
          ↑
   Network failure
```

Server A and Server B are still running, but they can't communicate.

That's a **network partition**.

## The Important Part: You Can't Ignore Partitions

Here's the part that is often misunderstood.

CAP doesn't simply mean:

> "Pick any two of the three."

In a distributed system, **network partitions can happen**.

When a partition occurs, you have to choose between:

**Consistency (C)**
or
**Availability (A)**

You still need **Partition Tolerance (P)**.

So during a network failure:

```text
C + P → Prefer correct, consistent data
A + P → Prefer staying available
```

## A Simple Example

Imagine an e-commerce system with two servers:

```text
        Database
       /        \
   Server A    Server B
```

A network failure occurs:

```text
   Server A  X  Server B
```

A customer buys the last available product through Server A.

Server B doesn't know about the purchase because the servers can't communicate.

Now another customer requests the same product through Server B.

What should happen?

### Choose Consistency

Server B could reject the request because it cannot guarantee that its data is current.

You sacrifice **availability** to protect **consistency**.

### Choose Availability

Server B could continue accepting requests using its local data.

The system remains available, but users might temporarily see inconsistent information.

You sacrifice **consistency** to protect **availability**.

## CAP Isn't About "Fast vs Slow"

CAP is specifically about **distributed systems experiencing network partitions**.

It's not a general rule that says:

> "You can only have two of the three properties."

In normal operation, a system can provide both strong consistency and high availability.

The trade-off becomes important **when a network partition occurs**.

## Why CAP Matters

CAP helps engineers understand why distributed databases make different design decisions.

Some systems prioritize:

**Consistency**

Useful when incorrect or stale data is unacceptable.

Others prioritize:

**Availability**

Useful when keeping the system operational is more important than immediately having perfectly synchronized data.

Neither choice is automatically better.

It depends on the application.

## Key Takeaway

CAP Theorem is really about what happens when distributed systems **can't communicate**.

When a network partition occurs, you generally have to choose:

**Consistency or Availability.**

Understanding that trade-off helps you make better decisions when designing distributed systems, databases, and highly available applications.
