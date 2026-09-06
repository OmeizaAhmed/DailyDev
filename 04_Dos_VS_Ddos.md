# DoS vs DDoS: One Attacker, Many Machines, Same Goal

Your application can have solid code, strong authentication, and encrypted data—and still become unavailable.

Sometimes, an attacker doesn't need to break into your system.

They just need to overwhelm it.

This is where **Denial of Service (DoS)** and **Distributed Denial of Service (DDoS)** attacks come in.

## What Is a DoS Attack?

A **DoS attack** happens when a single source tries to make a system, server, or network unavailable to legitimate users.

Imagine your API can handle 1,000 requests per second.

An attacker sends 50,000 requests per second from one machine. Your server spends its resources trying to handle the traffic until legitimate users can no longer access the application.

The goal is simple:

> Make the service unavailable.

A basic example:

```text
One attacker
      ↓
Massive number of requests
      ↓
Server resources become exhausted
      ↓
Application becomes unavailable
```

Because the attack usually comes from one source, it can sometimes be easier to detect and block using techniques such as rate limiting or IP blocking.

## What Is a DDoS Attack?

A **DDoS attack** has the same goal, but the attack comes from **multiple systems at the same time**.

Instead of one attacker sending traffic, thousands—or even millions—of compromised devices may send requests to the target.

```text
Device 1 ──┐
Device 2 ──┤
Device 3 ──┼──→ Target Server
Device 4 ──┤
Device 5 ──┘
```

These devices may be part of a **botnet**—a network of compromised computers or devices controlled by an attacker.

This makes DDoS attacks more difficult to defend against.

Why?

Blocking one IP address does almost nothing when the traffic is coming from thousands of different IP addresses.

## The Main Difference

The easiest way to remember it is:

* **DoS:** One source attacks the target.
* **DDoS:** Multiple distributed sources attack the target.

Both aim to exhaust resources and make a service unavailable.

But DDoS attacks are generally more powerful because the attack traffic is distributed.

## A Simple Comparison

Imagine a small shop.

With a **DoS attack**, one person repeatedly walks in and blocks the entrance.

With a **DDoS attack**, hundreds of people surround every entrance at the same time.

The result is the same: real customers can't get in.

But the second situation is much harder to control.

## How Developers Can Reduce the Risk

You may not be able to prevent every attack, but you can make your application more resilient.

Some common protections include:

* **Rate limiting** to restrict excessive requests.
* **Web Application Firewalls (WAFs)** to filter suspicious traffic.
* **CDNs and DDoS protection services** to absorb and filter large amounts of traffic.
* **Load balancing** to distribute traffic across multiple servers.
* **Caching** to reduce unnecessary pressure on your backend.
* **Monitoring and alerting** to detect unusual traffic spikes quickly.

For example, rate limiting can stop a single client from making thousands of requests to your login endpoint within seconds.

However, if thousands of different machines are making requests, rate limiting by IP alone may not be enough. That is where broader infrastructure-level DDoS protection becomes important.

## Key Takeaway

**DoS and DDoS attacks have the same objective: make a service unavailable. The difference is where the attack comes from.**

A DoS attack typically comes from one source.

A DDoS attack comes from many distributed sources, making it significantly harder to block.

As a developer, security is not only about protecting data from unauthorized access. It is also about making sure legitimate users can access your application when they need it.

A secure application that nobody can use is still a problem.
