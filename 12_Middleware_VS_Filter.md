# Middleware vs Filter in ASP.NET Core: What’s the Difference?

**Middleware and filters can both run before or after your request logic, but they operate at different levels of the ASP.NET Core pipeline.**

Understanding that difference helps you put code in the right place instead of turning your application into a collection of random checks.

## What Is Middleware?

Middleware is part of the **HTTP request pipeline**.

It can inspect, modify, or short-circuit an HTTP request before it reaches your endpoint. It can also process the response on the way back.

A simple middleware might log every incoming request:

```csharp
public async Task InvokeAsync(HttpContext context)
{
    Console.WriteLine($"Request: {context.Request.Path}");

    await _next(context);

    Console.WriteLine($"Response: {context.Response.StatusCode}");
}
```

Middleware is useful for concerns that apply broadly across your application.

Common examples:

* Authentication
* Exception handling
* Logging
* CORS
* Request/response manipulation
* Rate limiting

Think of middleware as a **checkpoint for the entire HTTP pipeline**.

---

## What Is a Filter?

Filters are part of the **ASP.NET Core MVC/controller pipeline**.

They run at specific stages around controller actions.

For example, an authorization filter can check whether a user is allowed to execute an action before the controller action runs.

```csharp
public class AdminOnlyFilter : IAuthorizationFilter
{
    public void OnAuthorization(AuthorizationFilterContext context)
    {
        if (!context.HttpContext.User.IsInRole("Admin"))
        {
            context.Result = new ForbidResult();
        }
    }
}
```

Filters are useful when the behavior is specifically related to MVC/controller actions.

Common filter types include:

* Authorization filters
* Resource filters
* Action filters
* Exception filters
* Result filters

Think of a filter as a **checkpoint specifically around controller execution**.

---

## Middleware vs Filter

| Middleware                                              | Filter                                       |
| ------------------------------------------------------- | -------------------------------------------- |
| Works at the HTTP pipeline level                        | Works inside the MVC/controller pipeline     |
| Can apply to almost every request                       | Mainly applies to MVC/controller endpoints   |
| Runs before routing or at specific middleware positions | Runs around controller/action execution      |
| Good for global concerns                                | Good for controller/action-specific concerns |
| Uses `HttpContext`                                      | Has access to MVC-specific context           |

The biggest difference is **scope**.

Middleware doesn't need to care whether the request eventually reaches a controller, Razor Page, or another endpoint.

A filter is more closely connected to the MVC execution process.

---

## A Simple Example

Imagine you want to log every request entering your API.

**Middleware** is a good choice:

```text
Request
   ↓
Logging Middleware
   ↓
Authentication
   ↓
Authorization
   ↓
Controller
```

But suppose you want to run some logic only before a specific controller action.

A **filter** makes more sense:

```text
Request
   ↓
Middleware
   ↓
Controller
   ↓
Action Filter
   ↓
Controller Action
```

The decision comes down to **where the behavior belongs in the pipeline**.

---

## Can You Use Both?

Absolutely.

A typical ASP.NET Core application might use middleware for global concerns:

```text
HTTP Request
     ↓
Exception Middleware
     ↓
Logging Middleware
     ↓
Authentication Middleware
     ↓
Authorization Middleware
     ↓
MVC Pipeline
     ↓
Action Filter
     ↓
Controller Action
```

They aren't competing technologies.

They solve problems at different levels.

## The Key Takeaway

**Use middleware when the concern belongs to the HTTP request pipeline. Use filters when the concern is specifically tied to MVC/controller execution.**

A simple rule to remember:

> **Middleware is broader. Filters are more specific.**

Knowing where your logic belongs makes your ASP.NET Core applications cleaner, easier to maintain, and easier to reason about.
