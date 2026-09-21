# OpenTelemetry in .NET: Stop Guessing What Your Application Is Doing

Your API is slow.

But why?

Is the database taking too long?
Is another service failing?
Is an external API responding slowly?

Logs can give you clues, but when you're dealing with a distributed application, logs alone can make debugging painful.

This is where **OpenTelemetry** comes in.

## What is OpenTelemetry?

OpenTelemetry (OTel) is an open-source observability framework for collecting telemetry data from your applications.

It focuses mainly on three types of data:

* **Traces** — What happened during a request?
* **Metrics** — How is the application performing?
* **Logs** — What events are happening?

The goal is simple:

> Understand what your application is doing without guessing.

---

## Why Does This Matter in .NET?

Imagine a request going through your application:

```text
Client
  ↓
ASP.NET Core API
  ↓
Order Service
  ↓
Payment Service
  ↓
Database
```

The user only sees:

```text
Request took 3.2 seconds
```

But where did those 3.2 seconds go?

OpenTelemetry can give you a trace like:

```text
HTTP Request          3.2s
 ├── Order Service    1.1s
 ├── Payment API      1.7s
 └── Database         0.4s
```

Now you have something useful.

Instead of saying:

> "The API is slow."

You can say:

> "The Payment API is responsible for most of the request latency."

That's the difference between logging events and understanding system behavior.

---

## Traces: Following a Request

A **trace** represents the journey of a request through your system.

Each operation within that journey is represented as a **span**.

For example:

```text
Trace
│
├── HTTP GET /orders
│
├── SQL Query
│
└── HTTP POST /payments
```

Each span can contain information such as:

* Duration
* Operation name
* Status
* Attributes
* Errors
* Relationships to other spans

The important part is that these spans can be connected together into one trace.

---

## Metrics: Measuring Performance

Metrics give you numbers about your application.

For example:

```text
HTTP Requests:       15,420
Request Errors:          73
Average Duration:      180ms
Active Requests:          12
```

Metrics are useful when you want to answer questions like:

> "Are errors increasing?"

or:

> "Has API latency gotten worse?"

---

## Logs: Understanding Events

Logs record events happening inside your application.

For example:

```text
Order 4821 created
Payment request started
Payment failed
```

OpenTelemetry can also help correlate logs with traces.

That means you can go from:

```text
Trace → Span → Log
```

and understand what happened during a specific request.

---

## Setting Up OpenTelemetry in ASP.NET Core

Install the required packages:

```bash
dotnet add package OpenTelemetry.Extensions.Hosting
dotnet add package OpenTelemetry.Exporter.OpenTelemetryProtocol
dotnet add package OpenTelemetry.Instrumentation.AspNetCore
dotnet add package OpenTelemetry.Instrumentation.Http
dotnet add package OpenTelemetry.Instrumentation.Runtime
```

Then configure OpenTelemetry in `Program.cs`:

```csharp
builder.Services.AddOpenTelemetry()
    .WithTracing(tracing =>
    {
        tracing
            .AddAspNetCoreInstrumentation()
            .AddHttpClientInstrumentation()
            .AddOtlpExporter();
    })
    .WithMetrics(metrics =>
    {
        metrics
            .AddAspNetCoreInstrumentation()
            .AddRuntimeInstrumentation()
            .AddOtlpExporter();
    });
```

Now your application can automatically collect telemetry from common ASP.NET Core and HTTP operations.

---

## Where Does the Data Go?

OpenTelemetry doesn't try to be your dashboard.

Instead, it collects and exports telemetry to an observability backend.

A common architecture looks like this:

```text
.NET Application
       │
       ▼
 OpenTelemetry
       │
       ▼
   OTLP Exporter
       │
       ▼
Observability Backend
       │
       ▼
    Dashboard
```

You can use tools such as Grafana, Jaeger, Zipkin, or other systems that support OpenTelemetry.

This separation is important.

Your application produces telemetry.

Your observability platform stores, analyzes, and visualizes it.

---

## Automatic vs Manual Instrumentation

OpenTelemetry can automatically instrument many common operations.

For example:

```csharp
.AddAspNetCoreInstrumentation()
.AddHttpClientInstrumentation()
```

This can give you useful telemetry without manually creating spans for every request.

But sometimes you want to track your own business operations.

For example:

```csharp
using var activity = ActivitySource.StartActivity("ProcessOrder");

activity?.SetTag("order.id", orderId);
activity?.SetTag("customer.id", customerId);
```

Now you have a custom span representing your business operation.

This becomes useful when infrastructure-level telemetry isn't enough to understand your application's behavior.

---

## OpenTelemetry Is Not Just Logging

This is one of the most important distinctions.

Traditional logging might tell you:

```text
Payment failed for order 4821
```

A trace can tell you:

```text
Request
  ↓
Create Order
  ↓
Database Query       120ms
  ↓
Payment API          1.8s
  ↓
Payment Failed
```

The second gives you **context**.

That's why observability becomes increasingly important as applications become distributed.

---

## The Key Takeaway

OpenTelemetry gives your .NET applications a standardized way to collect **traces, metrics, and logs**.

Instead of asking:

> "Why is this request slow?"

you can investigate the actual path the request took.

For a small application, you might get away with logs.

For a system with multiple services, databases, queues, and external APIs, observability becomes much harder to ignore.

**Don't just collect logs. Understand the journey of your requests.**
