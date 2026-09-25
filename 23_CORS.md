# CORS Explained: Why Your Browser Blocks Your API

You build an API.

Your frontend sends a request.

The API responds successfully.

And somehow, the browser still says:

> **Blocked by CORS policy.**

What just happened?

Let's break it down.

## What Is CORS?

**CORS (Cross-Origin Resource Sharing)** is a browser security mechanism that controls whether a web page can make requests to a different **origin**.

An origin is made up of:

* Protocol
* Domain
* Port

For example:

```text
http://localhost:3000
```

and

```text
http://localhost:5000
```

are different origins because their ports are different.

So if your React application runs on:

```text
http://localhost:3000
```

and your ASP.NET API runs on:

```text
http://localhost:5000
```

the browser treats the request as cross-origin.

## Why Does CORS Exist?

Imagine a user is logged into their banking website.

Now imagine another website could freely make requests to that banking API using the user's browser.

That could create serious security problems.

The browser therefore follows the **same-origin policy** by default.

CORS provides a controlled way for servers to say:

> "Requests from this particular origin are allowed."

## A Simple Example

Suppose your frontend does this:

```javascript
fetch("https://api.example.com/users")
```

But your frontend is running on:

```text
https://app.example.com
```

The browser sees two different origins:

```text
Frontend:
https://app.example.com

API:
https://api.example.com
```

The API needs to explicitly allow the frontend's origin.

A server might respond with:

```http
Access-Control-Allow-Origin: https://app.example.com
```

The browser sees this header and knows that the frontend is allowed to access the response.

## CORS in ASP.NET Core

In ASP.NET Core, you can configure CORS using a policy.

For example:

```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("FrontendPolicy", policy =>
    {
        policy
            .WithOrigins("https://app.example.com")
            .AllowAnyHeader()
            .AllowAnyMethod();
    });
});
```

Then apply the policy:

```csharp
app.UseCors("FrontendPolicy");
```

Now the API allows requests from:

```text
https://app.example.com
```

## What About `AllowAnyOrigin()`?

You might see this:

```csharp
policy
    .AllowAnyOrigin()
    .AllowAnyHeader()
    .AllowAnyMethod();
```

This means requests from any origin can access the API's CORS-enabled responses.

It's convenient during development, but you should be deliberate about using it in production.

If your application only needs to communicate with one frontend, explicitly specify that origin:

```csharp
.WithOrigins("https://app.example.com")
```

## What Is a Preflight Request?

Sometimes the browser sends an **OPTIONS** request before the actual request.

This is called a **preflight request**.

For example, your frontend wants to send:

```http
POST /api/users
Authorization: Bearer ...
Content-Type: application/json
```

The browser may first ask the server:

```http
OPTIONS /api/users
```

with headers describing the intended request.

The server responds with something like:

```http
Access-Control-Allow-Origin: https://app.example.com
Access-Control-Allow-Methods: POST
Access-Control-Allow-Headers: Authorization, Content-Type
```

If the browser is satisfied with the response, it sends the actual `POST` request.

So when debugging CORS, don't only look at the request you expected. Check whether an **OPTIONS** request is failing first.

## CORS Is a Browser Restriction

This is one of the most important things to understand.

CORS is primarily enforced by browsers.

For example:

```text
React → Browser → API
```

The browser checks the CORS rules.

But if you make the same request using:

```text
Postman → API
```

you may not see the same CORS error.

That's because Postman isn't enforcing browser CORS rules in the same way.

So:

> **"It works in Postman but not in my frontend"**

is often a strong clue that you're dealing with CORS.

## CORS ≠ Authentication

CORS doesn't determine whether a user is authenticated.

These are different concerns.

**Authentication** asks:

> Who are you?

**Authorization** asks:

> Are you allowed to access this resource?

**CORS** asks:

> Is this browser-based origin allowed to access the response?

For example, your API can correctly validate a JWT and still reject the browser's access because the CORS configuration doesn't allow the frontend's origin.

## A Common Mistake

Developers sometimes see:

```text
CORS error
```

and immediately add:

```csharp
.AllowAnyOrigin()
```

That may hide the immediate problem, but it doesn't necessarily explain what caused it.

Instead, check:

1. Is the frontend origin correct?
2. Is the API returning the correct CORS headers?
3. Is the OPTIONS preflight succeeding?
4. Are the required headers allowed?
5. Are the required HTTP methods allowed?
6. Is middleware configured in the correct order?

Understanding the request flow is much more useful than simply disabling the restriction.

## Key Takeaway

CORS isn't your API randomly refusing requests.

It's the browser enforcing a security boundary between different origins.

Once you understand **origin, same-origin policy, preflight requests, and CORS response headers**, those confusing:

```text
Blocked by CORS policy
```

errors become much easier to debug.

**Don't just add `AllowAnyOrigin()` because the error disappeared. Understand why the browser rejected the request in the first place.**
