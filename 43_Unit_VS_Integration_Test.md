# Unit Tests vs Integration Tests: What's the Difference, and Why Do You Need Both?

Your code passes every unit test. Everything looks perfect.

Then you deploy, and suddenly your application starts failing.

The database connection isn't working. An API returns unexpected data. Two components that work perfectly on their own fail when communicating.

**This is why passing unit tests doesn't guarantee a working application.**

Let's understand the difference between Unit Tests and Integration Tests, and when to use each.

## 1. What Are Unit Tests?

Unit tests verify that individual pieces of code work correctly in isolation.

A unit could be a method, function, or class.

The goal is simple: **Does this specific piece of code behave as expected?**

For example, imagine you're building a banking application with a method that calculates transaction fees.

```csharp
public decimal CalculateFee(decimal amount)
{
    return amount * 0.01m;
}
```

A unit test using xUnit might look like this:

```csharp
[Fact]
public void CalculateFee_ShouldReturnOnePercent()
{
    var service = new TransactionService();

    var fee = service.CalculateFee(1000m);

    Assert.Equal(10m, fee);
}
```

This test checks one thing: whether the calculation produces the expected result.

It doesn't need a database, external API, or running server.

**Advantages of Unit Tests:**
- Fast to execute
- Easy to debug
- Help catch logic errors early
- Provide quick feedback during development
- Encourage modular, maintainable code

**Limitation:** Unit tests cannot guarantee that independently tested components will work correctly together.

## 2. What Are Integration Tests?

Integration tests verify that multiple components work correctly when connected.

Instead of testing a single method, you're testing how different parts of your application interact.

For example, consider a banking API that processes transfers.

A transfer might involve:

1. Receiving an HTTP request.
2. Validating the transaction.
3. Checking the account balance.
4. Updating the database.
5. Returning a response.

Each component might pass its unit tests, but what happens when they work together?

That's what integration tests help verify.

Here's a simplified ASP.NET Core integration test using xUnit and `WebApplicationFactory`:

```csharp
public class TransferTests :
    IClassFixture<WebApplicationFactory<Program>>
{
    private readonly HttpClient _client;

    public TransferTests(
        WebApplicationFactory<Program> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task Transfer_ShouldRejectInvalidAmount()
    {
        var response = await _client.PostAsJsonAsync(
            "/api/transfers",
            new { Amount = -100 }
        );

        Assert.Equal(
            HttpStatusCode.BadRequest,
            response.StatusCode
        );
    }
}
```

This test sends an HTTP request through the application's test host and checks whether the API rejects an invalid amount.

It verifies more than a single method, including HTTP routing, model binding, and relevant request-processing components.

A broader integration test could also verify database persistence using an isolated test database.

**Advantages of Integration Tests:**
- Detect communication problems between components
- Verify API and database interactions
- Catch configuration and dependency-injection issues
- Provide confidence that connected components behave correctly

**Limitations:**
- Generally slower than unit tests
- Require more setup
- Failures can be harder to diagnose
- May depend on infrastructure such as databases or message brokers

## 3. Unit Tests vs Integration Tests: Key Differences

**Unit Tests**

- **Focus:** Individual methods, functions, or classes
- **Scope:** Small and isolated
- **Speed:** Usually very fast
- **Dependencies:** Typically replaced with mocks or fakes
- **Debugging:** Easier to pinpoint failures
- **Example:** Checking whether a transaction fee is calculated correctly

**Integration Tests**

- **Focus:** Interactions between components
- **Scope:** Multiple connected components
- **Speed:** Generally slower
- **Dependencies:** Often use real components or test infrastructure
- **Debugging:** May require investigating several layers
- **Example:** Verifying that a transfer API correctly updates account balances in a database

## 4. When Should You Use Each?

Use **Unit Tests** when testing business rules, calculations, validation logic, or methods that can be isolated from external dependencies.

Use **Integration Tests** when testing database operations, API endpoints, service communication, authentication pipelines, or messaging infrastructure.

For example, in a financial application:

- Unit test that a transfer cannot exceed the available balance.
- Integration test that a successful transfer correctly updates both accounts in the database.
- Unit test that a transaction fee is calculated correctly.
- Integration test that the API returns the correct response when a transfer is rejected.

Neither type of test replaces the other.

## 5. Why You Need Both

Imagine building an application using only unit tests.

Your business logic might be perfectly tested, but you could still have broken database queries, incorrect API configurations, or failed service integrations.

Now imagine relying only on integration tests.

You might catch those problems, but your tests could become slower and make simple logic bugs harder to identify.

A practical testing strategy combines both:

**Unit Tests → Integration Tests → End-to-End Tests**

Unit tests provide fast feedback on individual components.

Integration tests verify that components work together.

End-to-end tests validate complete user workflows across the system.

The goal isn't to write the largest number of tests. It's to build confidence that your application behaves correctly.

## Key Takeaway

**Unit tests verify that individual components work correctly. Integration tests verify that those components work correctly together.**

Good software needs both.

Because working code is important, but working systems are what users actually depend on.