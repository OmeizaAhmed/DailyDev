# Rate Limiting: The Traffic Cop Your API Needs

Your API can be perfectly designed and still fail when too many requests hit it at once.

That’s where **rate limiting** comes in.

Rate limiting controls how many requests a client can make to your API within a specific period.

For example:

> A user can make **100 requests per minute**.

Request #101? They have to wait.

## Why Do We Need Rate Limiting?

Without rate limiting, a single client could send thousands of requests to your API and consume resources that should be available to everyone else.

Rate limiting helps protect against:

* Abuse and spam
* Brute-force attacks
* Accidental request floods
* Resource exhaustion
* Excessive API usage

It also helps keep your API predictable under heavy traffic.

## A Simple Example

Imagine you have:

```http
GET /api/products
```

You configure a limit of:

```text
100 requests / minute / client
```

A client makes 100 requests within the minute.

The next request could receive:

```http
429 Too Many Requests
```

The API is basically saying:

> "Slow down. You've reached your limit."

## How Does Rate Limiting Know Who to Limit?

The limit can be applied based on different identifiers:

**IP address**

```text
192.168.1.20 → 100 requests/minute
```

Useful for controlling anonymous traffic.

**User**

```text
User 123 → 1,000 requests/hour
```

Useful when users are authenticated.

**API key**

```text
API_KEY_ABC → 10,000 requests/day
```

Common for public APIs and third-party integrations.

## Common Rate-Limiting Algorithms

There are several ways to implement rate limiting.

### Fixed Window

Requests are counted within fixed time periods.

```text
12:00 - 12:01 → 100 requests
12:01 - 12:02 → 100 requests
```

Simple, but traffic can spike around the boundary between windows.

### Sliding Window

Instead of using fixed time blocks, the system looks at a continuously moving time window.

This provides more accurate control over request bursts.

### Token Bucket

The system maintains a bucket of tokens.

Each request consumes a token.

Tokens are continuously added back at a fixed rate.

This allows controlled bursts while still enforcing an average request rate.

## Rate Limiting in ASP.NET Core

ASP.NET Core provides built-in rate-limiting middleware.

For example:

```csharp
builder.Services.AddRateLimiter(options =>
{
    options.AddFixedWindowLimiter("fixed", limiterOptions =>
    {
        limiterOptions.PermitLimit = 100;
        limiterOptions.Window = TimeSpan.FromMinutes(1);
    });
});
```

Then enable it in the request pipeline:

```csharp
app.UseRateLimiter();
```

You can also apply a policy to specific endpoints instead of limiting your entire API.

## Rate Limiting vs Authentication

These solve different problems.

**Authentication asks:**

> "Who are you?"

**Rate limiting asks:**

> "How many requests are you allowed to make?"

An authenticated user can still abuse an API, so authentication does not replace rate limiting.

## The Key Takeaway

Rate limiting isn't just about blocking excessive requests.

It's about **protecting your API, controlling resource usage, and making sure one client doesn't negatively affect everyone else.**

If your API is exposed to the internet, don't just ask:

> "Can users access this endpoint?"

Also ask:

> **"How often should they be allowed to access it?"**
