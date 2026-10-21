# DRY vs WET Code: When Repeating Yourself Is Actually Better

Every developer has heard the advice: **"Don't Repeat Yourself."**

But what if following that principle too aggressively makes your code harder to maintain?

Sometimes, duplicating a few lines of code is better than creating an abstraction that complicates everything.

This is where **DRY vs WET** becomes interesting.

## What Is DRY Code?

**DRY (Don't Repeat Yourself)** is a software development principle that encourages developers to avoid duplicating knowledge or business logic across a codebase.

The idea is simple: **Every piece of knowledge should have a single, authoritative representation in a system.**

Consider this C# example:

```csharp
public decimal CalculateProductTax(decimal price)
{
    return price * 0.075m;
}

public decimal CalculateServiceTax(decimal price)
{
    return price * 0.075m;
}
```

Both methods implement the same tax calculation.

If the tax rate changes, you must update both methods. Forgetting one could introduce inconsistent behavior.

### Applying DRY

We can extract the shared logic:

```csharp
public decimal CalculateTax(decimal price)
{
    return price * 0.075m;
}
```

Now both product and service calculations can reuse the same implementation.

**Benefits of DRY:**
- Less duplicated business logic.
- Easier maintenance.
- More consistent behavior.
- Changes can be made in one place.

But there is a catch.

**Not everything that looks similar should be combined.**

## What Is WET Code?

**WET** is commonly interpreted as "Write Everything Twice" or "We Enjoy Typing."

It describes code that contains repetition rather than extracting every similarity into a reusable abstraction.

Although WET is often used negatively, a little duplication can sometimes make code easier to understand.

Consider two validation methods:

```csharp
public bool ValidateCustomer(Customer customer)
{
    return !string.IsNullOrEmpty(customer.Email);
}

public bool ValidateEmployee(Employee employee)
{
    return !string.IsNullOrEmpty(employee.Email);
}
```

Both methods perform the same check.

A developer focused entirely on DRY might immediately introduce a shared interface, generic validator, or abstraction.

But should they?

Not necessarily.

Customer and employee validation might have different business requirements in the future.

If they evolve independently, combining them too early creates unnecessary coupling.

Keeping them separate allows each implementation to change without affecting the other.

**This is where a little WET code makes sense.**

## The Hidden Danger of DRY: Premature Abstraction

Imagine you have three services performing similar operations.

You decide to combine them into one reusable method.

A few weeks later, their requirements change.

Suddenly, your reusable method starts looking like this:

```csharp
public void Process(
    bool isCustomer,
    bool isEmployee,
    bool requiresApproval)
{
    // Multiple conditional branches
}
```

Instead of reducing complexity, the abstraction has introduced more of it.

Now, changing one workflow risks affecting another.

The problem isn't DRY itself.

**The problem is abstracting code before understanding what genuinely belongs together.**

## The Rule of Three

One practical approach is the **Rule of Three**.

- **First occurrence:** Write the code.
- **Second occurrence:** Notice the similarity, but don't rush to abstract.
- **Third occurrence:** Evaluate whether a shared abstraction makes sense.

This is a heuristic, not a strict rule.

If two pieces of code represent the same business rule, centralizing that rule immediately may be appropriate.

But if they only happen to look alike, keeping them separate can be the better design.

## DRY vs WET: Which Should You Choose?

**Choose DRY when:**

- Multiple components share the same business rule.
- Duplicated logic must always remain consistent.
- Changes should propagate across all consumers.
- The abstraction has a clear responsibility.

**Choose WET when:**

- Similar code serves different business purposes.
- Requirements are likely to evolve independently.
- Extracting shared logic introduces unnecessary complexity.
- A little duplication makes the code easier to understand.

## The Key Takeaway

Good software engineering isn't about eliminating every duplicated line.

It's about balancing **reusability, readability, maintainability, and flexibility**.

DRY helps prevent duplicated knowledge, while deliberate WET code can help avoid premature abstractions.

Remember:

**Duplicated code is not always duplicated knowledge.**

Sometimes, repeating yourself today is better than maintaining the wrong abstraction tomorrow.