# Pub/Sub Messaging: Let One Event Trigger Many Actions

An order is placed. You need to send a confirmation email, update inventory, and notify the warehouse.

You could make the order service call every system directly. But each new integration adds another dependency—and another way for order processing to fail.

Pub/Sub offers a different approach: publish what happened and let interested systems react.

## What Is Pub/Sub?

**Pub/Sub stands for publish/subscribe.** It is a messaging pattern with three main parts:

- **Publisher:** Produces a message.
- **Topic:** A named channel through which a broker routes messages.
- **Subscriber:** Receives messages through a subscription to that topic.

The publisher does not need to know which systems consume its messages.

For example, an order service publishes this message to an `orders` topic:

```json
{
  "eventId": "evt_123",
  "type": "order.created",
  "orderId": "ord_456",
  "customerId": "cus_789"
}
```

The email, inventory, and warehouse services each have their own subscription. Each can react to the same event independently.

## Why This Helps

### Add features without changing the publisher

Suppose you introduce an analytics service.

With direct calls, you may need to update the order service to call it. With Pub/Sub, you can add a subscription that receives relevant order events.

The publisher still needs to maintain a stable message contract, but it does not need to manage every consumer.

### Handle work independently

An email service can process messages at a different pace from the inventory service.

With durable subscriptions and suitable retention settings, a temporarily unavailable subscriber can catch up later. That behavior depends on the messaging system and its configuration.

### Keep slow work out of the request

The order service can publish an event without waiting for every downstream action to finish.

That makes processing asynchronous: an accepted order does not necessarily mean its confirmation email has already been sent.

## Pub/Sub vs a Work Queue

The key difference is **who should receive the message**.

A work queue typically distributes each message to one worker in a group. Pub/Sub routes a message to each interested subscription.

For an `order.created` event:

- Email and inventory need separate subscriptions because both must react.
- Multiple email workers can share one subscription to divide the email workload.

Pub/Sub enables fan-out across subscriptions while allowing workers within a subscription to share the work.

## The Catch: Delivery Needs Care

Pub/Sub reduces direct dependencies, but it introduces delivery concerns.

**Duplicate messages:** Many systems offer at-least-once delivery. A subscriber should safely handle the same event more than once—for example, by recording processed event IDs.

**Failed processing:** Use controlled retries and a dead-letter destination for messages that repeatedly fail.

**Ordering:** Do not assume global message order. Check the broker’s guarantees and configure ordering where your workflow requires it.

**Consistency:** Saving an order and publishing its event are separate operations. An outbox pattern can help prevent an order from being saved without its event eventually being published.

## Key Takeaway

**Pub/Sub lets one event reach multiple independent consumers without making the publisher coordinate them all.**

Use it when several systems need to react to the same event. Design for duplicates, failures, and delayed processing from the start.