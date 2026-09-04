# Cache Avalanche: When Your Cache Fails and Your Database Pays the Price

Your cache is supposed to protect your database.

But what happens when thousands of cached items expire at almost the exact same time?

Your database suddenly receives a flood of requests it was never meant to handle.

That is a **Cache Avalanche**.

## What Is a Cache Avalanche?

A Cache Avalanche happens when a large number of cache entries become unavailable simultaneously.

This can happen because:

* Many cache keys have the same expiration time.
* The cache server goes down.
* A cache cluster becomes unavailable.

When the cache stops serving those requests, they all fall back to the database.

### Example

Imagine your application has 100,000 product records cached.

You set them all to expire after exactly **1 hour**:

```text
product:1   → expires in 1 hour
product:2   → expires in 1 hour
product:3   → expires in 1 hour
...
product:100000 → expires in 1 hour
```

After one hour, a large number of these keys expire together.

Now users request those products.

The cache misses.

The application queries the database.

Thousands of requests suddenly hit the database at once.

The database slows down, requests start timing out, and your application can experience a cascading failure.

The cache problem has now become a system problem.

## How to Prevent It

### 1. Add Random TTL Values

Instead of giving every cache entry the same expiration time, add some randomness.

Instead of this:

```text
TTL = 60 minutes
```

Use something like:

```text
TTL = 60 minutes + random(0–10 minutes)
```

Now your cache entries expire gradually instead of all at once.

This is often called **TTL jitter**.

### 2. Use a Highly Available Cache

If your entire application depends on one cache server, that server becomes a single point of failure.

Use replication, clustering, or a managed cache solution where appropriate.

The goal is simple: one cache failure should not take down your application.

### 3. Protect the Database

Your database should not blindly accept an unlimited flood of requests when the cache fails.

Consider using:

* Rate limiting
* Request throttling
* Circuit breakers
* Queues
* Connection limits

These mechanisms help prevent a cache outage from overwhelming your database.

## Cache Avalanche vs Cache Stampede

These two are related but different.

A **Cache Avalanche** happens when many cache keys become unavailable at the same time.

A **Cache Stampede** happens when many requests try to rebuild the same missing cache entry simultaneously.

For example:

```text
1000 requests → cache miss for product:123
```

Without protection:

```text
1000 requests → 1000 database queries
```

With a locking mechanism, one request rebuilds the cache while the others wait or receive a fallback response.

## The Key Takeaway

A cache is not just about making your application faster.

You also need to think about what happens **when the cache is unavailable**.

If thousands of cache entries can expire at once, or your cache can fail without a fallback strategy, your database may become the next victim.

**Spread out cache expiration, design for cache failures, and always protect your database from sudden traffic spikes.**
