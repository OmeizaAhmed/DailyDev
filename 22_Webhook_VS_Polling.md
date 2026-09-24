# Webhooks vs Polling: How Should Your Systems Communicate?

Your application needs to know when something happens.

A payment succeeds.
An order is shipped.
A GitHub issue is created.

The question is: **should your application keep asking for updates, or should the other system notify you when something happens?**

That’s the difference between **polling and webhooks**.

## Polling: “Has anything happened yet?”

With polling, your application repeatedly asks another system for updates.

For example:

```http
GET /api/payment/123
```

Your application might make this request every 5 seconds:

```text
Is the payment complete?
    ↓
    No
    ↓
Wait 5 seconds
    ↓
Is the payment complete?
    ↓
    No
    ↓
Wait 5 seconds
    ↓
Is the payment complete?
```

Eventually, the payment changes to `successful`.

### The problem

Most of those requests may return the same answer.

If you have thousands of users doing this, you're generating a lot of unnecessary requests.

Polling is simple, but it can be wasteful.

---

## Webhooks: “I’ll tell you when it happens.”

With a webhook, the receiving application provides an endpoint that another system can call when an event occurs.

For example:

```http
POST /webhooks/payment
```

When the payment succeeds, the payment provider sends:

```json
{
  "event": "payment.success",
  "paymentId": "123",
  "amount": 50000
}
```

Instead of asking repeatedly:

> “Did the payment succeed?”

Your application gets notified:

> “The payment succeeded.”

This is an **event-driven approach**.

---

## Webhooks vs Polling

|               | Polling                                | Webhooks                             |
| ------------- | -------------------------------------- | ------------------------------------ |
| Communication | Client asks repeatedly                 | Server sends event                   |
| Timing        | Based on polling interval              | Usually near real-time               |
| Requests      | Can generate many unnecessary requests | Only sends when an event occurs      |
| Complexity    | Simpler to implement                   | Requires webhook endpoint            |
| Reliability   | Easy to retry requests                 | Requires handling retries/duplicates |
| Best for      | Periodic checks                        | Event-driven updates                 |

---

## When should you use polling?

Polling makes sense when:

* The external system doesn't support webhooks.
* You need to periodically synchronize data.
* You don't need real-time updates.
* You want a simple implementation.

For example, your application might check an external API every 10 minutes for new records.

---

## When should you use webhooks?

Webhooks are useful when you need to react to events quickly.

Common examples include:

* Payment notifications
* Order status changes
* GitHub events
* Email delivery events
* Subscription changes
* CI/CD events

For example:

```text
Customer pays
     ↓
Payment provider
     ↓
Webhook
     ↓
Your API
     ↓
Update order
     ↓
Send confirmation
```

No constant checking required.

---

## One important webhook problem

Webhooks aren't automatically reliable just because they're event-driven.

The sender might retry an event if your server doesn't respond successfully.

That means you could receive the same webhook more than once.

Your application should therefore handle **idempotency**.

For example:

```text
payment.success
payment.success
payment.success
```

Your system should still process the payment only once.

A common approach is to store the webhook's unique event ID and ignore events that have already been processed.

You should also verify webhook signatures so that arbitrary clients can't impersonate the service sending your events.

---

## The key takeaway

**Polling asks, “Has anything changed?”**

**Webhooks say, “Something changed.”**

If you need periodic synchronization or the external service doesn't provide events, polling can be perfectly reasonable.

But when you're reacting to well-defined events, webhooks can reduce unnecessary requests and provide a much more responsive architecture.

Choose based on the communication pattern your system actually needs—not simply because one approach sounds more modern.
