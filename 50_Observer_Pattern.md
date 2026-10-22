# Observer Pattern: How to Keep Components in Sync Without Tight Coupling

Imagine building an e-commerce application.

Whenever a customer places an order, several things need to happen:

- Send an order confirmation email.
- Update inventory.
- Notify the shipping service.
- Record the activity for analytics.

One approach is to make the `OrderService` call every other service directly.

It works. But what happens when you need to add another action, like sending an SMS?

You modify the `OrderService` again.

This is the problem the **Observer Pattern** helps solve.

## What Is the Observer Pattern?

The Observer Pattern is a behavioral design pattern that defines a **one-to-many relationship between objects**.

When one object changes state or publishes an event, all registered observers are automatically notified.

The pattern involves two main components:

**1. Subject (Publisher)**

The object being observed. It maintains a collection of observers and notifies them when something happens.

**2. Observers (Subscribers)**

Objects interested in receiving notifications from the subject. Each observer decides how to react.

The important idea is that the subject doesn't need to know what each observer does.

It only knows how to notify them.

## A Practical Example in C#

Let's implement a simple order notification system.

**Step 1: Define the Observer Contract**

```csharp
public interface IOrderObserver
{
    void OnOrderPlaced(int orderId);
}
```

Every observer implements this interface.

**Step 2: Create the Observers**

```csharp
public class EmailObserver : IOrderObserver
{
    public void OnOrderPlaced(int orderId)
    {
        Console.WriteLine(
            $"Sending email for order {orderId}");
    }
}

public class InventoryObserver : IOrderObserver
{
    public void OnOrderPlaced(int orderId)
    {
        Console.WriteLine(
            $"Updating inventory for order {orderId}");
    }
}
```

Each observer has a single responsibility.

**Step 3: Implement the Subject**

```csharp
public class OrderService
{
    private readonly List<IOrderObserver> _observers = new();

    public void Subscribe(IOrderObserver observer)
    {
        _observers.Add(observer);
    }

    public void Unsubscribe(IOrderObserver observer)
    {
        _observers.Remove(observer);
    }

    public void PlaceOrder(int orderId)
    {
        Console.WriteLine($"Order {orderId} placed");

        foreach (var observer in _observers)
        {
            observer.OnOrderPlaced(orderId);
        }
    }
}
```

Notice something important.

The `OrderService` doesn't reference `EmailObserver` or `InventoryObserver` directly.

It only depends on the `IOrderObserver` abstraction.

**Step 4: Put Everything Together**

```csharp
var orderService = new OrderService();

orderService.Subscribe(new EmailObserver());
orderService.Subscribe(new InventoryObserver());

orderService.PlaceOrder(101);
```

Output:

```text
Order 101 placed
Sending email for order 101
Updating inventory for order 101
```

Now imagine adding an SMS notification.

You create an `SmsObserver`, implement the interface, and subscribe it.

No changes to the existing `OrderService` are necessary.

That's the real benefit.

## Where Is the Observer Pattern Used?

You've probably encountered the Observer Pattern without realizing it.

**User Interfaces:** Button-click handlers and UI events notify registered listeners.

**Real-Time Dashboards:** Components update when underlying application data changes.

**Notification Systems:** Multiple subscribers react to an event, such as a completed payment.

**.NET Applications:** C# events and delegates, along with `IObservable<T>` and `IObserver<T>`, provide mechanisms for observer-style communication.

## When Should You Avoid It?

The Observer Pattern isn't always the right choice.

As your application grows, having many observers can make event flows harder to trace.

There are also a few things to consider:

- A slow observer can delay other observers when notifications execute synchronously.
- An exception in one observer can interrupt notifications to the remaining observers.
- Forgotten subscriptions can cause memory leaks when a long-lived subject retains references to observers.
- The order in which observers execute can introduce unexpected dependencies.

For critical workflows like payment processing, durable messaging or an outbox-based approach may be more appropriate.

The basic Observer Pattern provides notifications, not guaranteed message delivery.

## Observer Pattern vs. Pub/Sub

These concepts are closely related, but they aren't identical.

In a traditional Observer implementation, the subject typically maintains direct references to its observers through an interface.

In Publish/Subscribe architectures, publishers and subscribers are often separated by an event broker or message bus.

The Observer Pattern commonly operates within a single process, while Pub/Sub systems can support communication across multiple services.

## Final Thoughts

The Observer Pattern is about more than sending notifications.

It's about reducing dependencies between components.

Instead of forcing one service to know everything that should happen after an event, let interested components subscribe and respond independently.

**The takeaway:** When multiple components need to react to the same change, consider the Observer Pattern before filling your application with tightly coupled service calls.

Good software design isn't about eliminating dependencies entirely. It's about managing them deliberately.