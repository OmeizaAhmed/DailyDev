# SOLID Design Principles: The 5 Rules That Make Your Code Easier to Maintain

Good code isn't just code that works.

It's code that can be changed, tested, and extended without breaking everything around it.

That's where **SOLID** comes in.

SOLID is a set of five object-oriented design principles that help you write software that is easier to maintain and scale.

The five principles are:

* **S** — Single Responsibility Principle
* **O** — Open/Closed Principle
* **L** — Liskov Substitution Principle
* **I** — Interface Segregation Principle
* **D** — Dependency Inversion Principle

Let's break them down with practical examples.

---

## 1. Single Responsibility Principle — One Class, One Job

A class should have **one responsibility** and therefore one reason to change.

Imagine this:

```csharp
public class UserService
{
    public void RegisterUser(User user)
    {
        // Register user
    }

    public void SendWelcomeEmail(User user)
    {
        // Send email
    }

    public void GenerateUserReport(User user)
    {
        // Generate report
    }
}
```

This class is doing too much.

User registration, email sending, and report generation are three different responsibilities.

A better design would separate them:

```csharp
public class UserService
{
    public void RegisterUser(User user)
    {
        // Register user
    }
}

public class EmailService
{
    public void SendWelcomeEmail(User user)
    {
        // Send email
    }
}

public class ReportService
{
    public void GenerateUserReport(User user)
    {
        // Generate report
    }
}
```

Now each class has a clear purpose.

**Think: One class, one job.**

---

## 2. Open/Closed Principle — Extend, Don't Keep Modifying

Software entities should be **open for extension but closed for modification**.

Suppose you have a payment service:

```csharp
public class PaymentService
{
    public void Pay(string method)
    {
        if (method == "Card")
        {
            // Pay with card
        }
        else if (method == "PayPal")
        {
            // Pay with PayPal
        }
    }
}
```

What happens when you add bank transfer?

You have to keep modifying the existing class.

Instead, define an abstraction:

```csharp
public interface IPaymentMethod
{
    void Pay();
}
```

Then create implementations:

```csharp
public class CardPayment : IPaymentMethod
{
    public void Pay()
    {
        // Card payment
    }
}

public class PayPalPayment : IPaymentMethod
{
    public void Pay()
    {
        // PayPal payment
    }
}
```

Now you can add another payment method without modifying the existing implementations.

```csharp
public class BankTransferPayment : IPaymentMethod
{
    public void Pay()
    {
        // Bank transfer
    }
}
```

**Add new behavior without constantly changing existing code.**

---

## 3. Liskov Substitution Principle — Don't Break Expectations

The Liskov Substitution Principle says that a child class should be usable wherever its parent or abstraction is expected **without breaking the program's behavior**.

A classic example is a bird:

```csharp
public class Bird
{
    public virtual void Fly()
    {
        // Fly
    }
}
```

Now imagine:

```csharp
public class Penguin : Bird
{
    public override void Fly()
    {
        throw new Exception("Penguins can't fly");
    }
}
```

The problem is that `Penguin` is technically a `Bird`, but it cannot fulfill the behavior promised by `Bird`.

A better design is:

```csharp
public abstract class Bird
{
}

public interface IFlyingBird
{
    void Fly();
}

public class Eagle : Bird, IFlyingBird
{
    public void Fly()
    {
        // Fly
    }
}

public class Penguin : Bird
{
}
```

The abstraction now matches the actual behavior.

**Inheritance shouldn't create surprises.**

---

## 4. Interface Segregation Principle — Don't Force Classes to Implement What They Don't Need

A class shouldn't be forced to depend on methods it doesn't use.

For example:

```csharp
public interface IWorker
{
    void Work();
    void Eat();
}
```

What if you have a robot?

```csharp
public class Robot : IWorker
{
    public void Work()
    {
        // Work
    }

    public void Eat()
    {
        // Robot doesn't eat
    }
}
```

The interface is too large.

Instead, split it:

```csharp
public interface IWorkable
{
    void Work();
}

public interface IEatable
{
    void Eat();
}
```

Now:

```csharp
public class Robot : IWorkable
{
    public void Work()
    {
        // Work
    }
}
```

And a human can implement both.

**Prefer small, focused interfaces over one giant interface.**

---

## 5. Dependency Inversion Principle — Depend on Abstractions

High-level code shouldn't depend directly on low-level implementations.

Consider:

```csharp
public class OrderService
{
    private readonly MySqlOrderRepository _repository;

    public OrderService()
    {
        _repository = new MySqlOrderRepository();
    }
}
```

`OrderService` is tightly coupled to MySQL.

If you later move to PostgreSQL, you have to modify the service.

Instead:

```csharp
public interface IOrderRepository
{
    void Save(Order order);
}
```

Then:

```csharp
public class MySqlOrderRepository : IOrderRepository
{
    public void Save(Order order)
    {
        // Save to MySQL
    }
}
```

And the service depends on the abstraction:

```csharp
public class OrderService
{
    private readonly IOrderRepository _repository;

    public OrderService(IOrderRepository repository)
    {
        _repository = repository;
    }
}
```

Now the service doesn't care whether the implementation uses MySQL, PostgreSQL, or something else.

This is also where **Dependency Injection** becomes useful: the dependency can be supplied from outside rather than created inside the class.

**Depend on what something does, not how it does it.**

---

## How the Five Principles Work Together

SOLID isn't about creating more interfaces and classes just for the sake of it.

It's about managing **change and dependencies**.

Think of it this way:

* **SRP:** Keep responsibilities focused.
* **OCP:** Add new behavior without constantly modifying existing code.
* **LSP:** Make sure implementations honor the behavior of their abstractions.
* **ISP:** Keep interfaces small and relevant.
* **DIP:** Keep high-level logic independent from implementation details.

When these principles are applied properly, your code becomes easier to:

* Test
* Maintain
* Extend
* Refactor
* Understand

But don't blindly apply SOLID everywhere.

A five-line class doesn't need three interfaces and six abstractions.

**Use SOLID when it solves a real design problem, not because the acronym looks good in a code review.**

## Key Takeaway

SOLID isn't a collection of rules designed to make your code more complicated.

It's a way to **reduce coupling, control change, and keep responsibilities clear**.

The goal isn't to write more code.

The goal is to make the code you already have **easier to change without fear.**
