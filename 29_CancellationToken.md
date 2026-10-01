# CancellationToken in C#: Stop Wasting Resources on Work Nobody Needs

Imagine a user sends a request to your API, but halfway through, they close the browser.

Your server doesn't automatically know that the work is no longer needed. It might continue querying the database, processing data, or calling external services.

That's where **`CancellationToken`** comes in.

It allows your application to detect cancellation requests and stop unnecessary work, helping you build more efficient and responsive applications.

## What Is CancellationToken?

`CancellationToken` is a .NET struct that allows a method to monitor whether an operation has been cancelled.

It doesn't forcibly terminate a running operation. Instead, it provides a cancellation signal that your code can check and respond to.

Cancellation is typically managed through `CancellationTokenSource`, which creates and controls the token.

Here's a simple example:

```csharp
public async Task ProcessDataAsync(
    CancellationToken cancellationToken)
{
    for (int i = 0; i < 10; i++)
    {
        cancellationToken.ThrowIfCancellationRequested();

        Console.WriteLine($"Processing item {i}");

        await Task.Delay(1000, cancellationToken);
    }
}
```

In this example:

* `ThrowIfCancellationRequested()` checks whether cancellation has been requested and throws an `OperationCanceledException` if it has.
* `Task.Delay()` accepts the token and can stop waiting when cancellation is requested.
* The loop stops processing when cancellation is detected.

## How Does CancellationToken Work?

Let's see how `CancellationTokenSource` and `CancellationToken` work together.

```csharp
var cts = new CancellationTokenSource();

var task = ProcessDataAsync(cts.Token);

// Request cancellation after 3 seconds
await Task.Delay(3000);
cts.Cancel();

try
{
    await task;
}
catch (OperationCanceledException)
{
    Console.WriteLine("Operation cancelled.");
}
```

Here's what happens:

1. `CancellationTokenSource` creates a cancellation token.
2. The token is passed to the asynchronous operation.
3. After three seconds, `cts.Cancel()` signals cancellation.
4. The running operation observes the signal and stops.
5. The caller handles the cancellation exception.

Notice that calling `Cancel()` only sends a signal. The operation must cooperate by observing the token.

## CancellationToken in ASP.NET Core

One of the most practical uses of `CancellationToken` is in ASP.NET Core APIs.

ASP.NET Core can automatically provide a request-aborted token to your controller action. This token is signalled when the client disconnects or the request is otherwise aborted.

For example:

```csharp
[HttpGet]
public async Task<IActionResult> GetProducts(
    CancellationToken cancellationToken)
{
    var products = await _dbContext.Products
        .ToListAsync(cancellationToken);

    return Ok(products);
}
```

ASP.NET Core automatically binds the `CancellationToken` parameter to the request's cancellation token.

If the request is aborted while the database query is running, Entity Framework Core can pass the cancellation request to the database provider.

This gives the database operation an opportunity to stop instead of doing unnecessary work.

You can also pass the same token through your service layer:

```csharp
public async Task<List<Product>> GetProductsAsync(
    CancellationToken cancellationToken)
{
    return await _repository.GetProductsAsync(
        cancellationToken);
}
```

Passing the token through each layer helps cancellation propagate throughout your application.

## Best Practices

**1. Pass tokens to asynchronous operations**

Don't just accept a `CancellationToken` parameter and ignore it. Pass it to supported methods such as `HttpClient.SendAsync()`, EF Core's asynchronous query methods, and `Task.Delay()`.

**2. Propagate cancellation through your application**

If your controller calls a service, and that service calls a repository, pass the token through each layer.

Otherwise, cancellation may stop at the first method that doesn't forward it.

**3. Don't swallow cancellation exceptions**

Avoid catching `OperationCanceledException` and treating it as an ordinary success.

Handle cancellation deliberately. If you catch it for cleanup or logging, make sure you don't accidentally hide the cancellation from the caller.

**4. Use cancellation for long-running operations**

Cancellation is particularly useful for database queries, external HTTP requests, background processing, and other operations that may take significant time.

However, not every operation needs a cancellation token. Use it where cancellation is meaningful and supported.

## Key Takeaway

`CancellationToken` is more than an optional parameter in your asynchronous methods. It's a way to make your application responsive to changing circumstances.

When a request is no longer needed, your application should have a way to stop doing unnecessary work.

**Write asynchronous code that knows not only how to start work, but also when to stop.**
