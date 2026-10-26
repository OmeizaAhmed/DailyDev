# Test-Driven Development (TDD): Write the Test Before the Code

Most developers write code first and test it afterward.

But what if you reversed that process?

Instead of writing an implementation and then checking whether it works, you start by defining what the code should do through a test.

That's the idea behind **Test-Driven Development (TDD)**.

## What Is Test-Driven Development?

Test-Driven Development is a software development approach where developers write automated tests before implementing the functionality those tests describe.

The goal isn't simply to have more tests.

It's to use tests to guide how software is designed and implemented.

TDD follows a simple cycle called **Red-Green-Refactor**.

### 1. Red: Write a Failing Test

Start by writing a test that describes the expected behavior of a feature.

The test should fail because the functionality hasn't been implemented yet.

This confirms that your test can detect the missing behavior.

### 2. Green: Make the Test Pass

Write the simplest code necessary to satisfy the test.

Don't worry about making everything perfect immediately.

Focus on getting the expected behavior working.

### 3. Refactor: Improve the Code

Once the test passes, improve the implementation without changing its behavior.

You might remove duplication, improve naming, or simplify complex logic.

Run the tests again to ensure everything still works.

Then repeat the cycle for the next requirement.

## Practical Example: TDD in C#

Imagine you're building an e-commerce application that needs to calculate discounts.

A customer purchasing an item worth $200 should pay $180 after a 10% discount.

**Step 1: Write the test first (Red)**

Using xUnit:

```csharp
[Fact]
public void ApplyDiscount_ShouldReturnDiscountedPrice()
{
    // Arrange
    decimal price = 200m;
    decimal discount = 10m;

    // Act
    var result = DiscountCalculator.ApplyDiscount(
        price, discount);

    // Assert
    Assert.Equal(180m, result);
}
```

At this stage, the test fails because the discount functionality hasn't been implemented.

**Step 2: Implement the functionality (Green)**

```csharp
public static class DiscountCalculator
{
    public static decimal ApplyDiscount(
        decimal price,
        decimal percentage)
    {
        return price - (price * percentage / 100m);
    }
}
```

Run the test again.

It passes because the calculation now produces the expected result.

**Step 3: Refactor**

We can make the calculation slightly more readable.

```csharp
public static decimal ApplyDiscount(
    decimal price,
    decimal percentage)
{
    var discountAmount = price * percentage / 100m;

    return price - discountAmount;
}
```

The behavior remains unchanged, and the test should still pass.

From here, we can introduce additional tests for edge cases like a 0% discount, a 100% discount, or invalid discount percentages.

Each new requirement starts with another failing test.

That's TDD in action.

## Why Does TDD Matter?

**1. Encourages Better Code Design**

Writing tests first forces you to think about how your code will be used before deciding how to implement it.

This can encourage smaller methods, clearer responsibilities, and loosely coupled components.

**2. Catches Bugs Earlier**

Instead of discovering unexpected behavior much later, you get feedback while implementing each requirement.

**3. Makes Refactoring Safer**

Automated tests help detect when changes accidentally break existing behavior.

**4. Clarifies Requirements**

Tests provide executable examples of what the application is expected to do.

They can also help other developers understand existing functionality.

## Does TDD Have Drawbacks?

TDD isn't a silver bullet.

Writing tests first can initially slow development, particularly when requirements are unclear or developers are unfamiliar with testing practices.

Tests can also become expensive to maintain when they're tightly coupled to implementation details.

And passing unit tests doesn't guarantee that an entire system works correctly.

Integration tests, security testing, and other validation techniques still matter.

The key is to test meaningful behavior rather than every internal implementation detail.

## When Should You Use TDD?

TDD is particularly useful when:

- Implementing business rules and calculations.
- Building services with clearly defined behavior.
- Fixing bugs that should never reappear.
- Developing features with complex edge cases.
- Refactoring existing functionality with a reliable test suite.

It may be less practical during early exploration when requirements and interfaces are changing rapidly.

## Key Takeaway

**TDD isn't about writing tests for the sake of testing. It's about using tests to drive development decisions.**

Instead of asking, "How do I test this code I've written?"

You start by asking, "What should this code do, and how will I know it works?"

That shift can lead to more predictable, maintainable, and reliable software.