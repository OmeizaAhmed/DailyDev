# Memoization: Stop Doing the Same Work Twice

Imagine calling a function with the exact same input 100 times.

If that function performs an expensive calculation, why should your application calculate the same result 100 times?

It shouldn't.

**That's where memoization comes in.**

## What Is Memoization?

Memoization is an optimization technique where you **store the result of an expensive function call and reuse it when the same input appears again.**

Think of it like a notebook.

The first time you solve a problem, you write down the answer. Next time you encounter the exact same problem, you look at your notes instead of solving it again.

For example:

```csharp
int Square(int number)
{
    return number * number;
}
```

Without memoization:

```text
Square(10) → calculate → 100
Square(10) → calculate → 100
Square(10) → calculate → 100
```

With memoization:

```text
Square(10) → calculate → 100 → store

Square(10) → return stored 100
Square(10) → return stored 100
```

The second and third calls don't need to perform the calculation again.

---

## How Does It Work?

Memoization usually involves a **cache**.

The basic flow is:

```text
Function called
      ↓
Have we seen this input before?
      ↓
   Yes ─────→ Return cached result
      │
      No
      ↓
Calculate result
      ↓
Store result
      ↓
Return result
```

Here's a simple C# example:

```csharp
Dictionary<int, int> cache = new();

int Square(int number)
{
    if (cache.TryGetValue(number, out var result))
        return result;

    result = number * number;
    cache[number] = result;

    return result;
}
```

Now, once `Square(10)` has been calculated, the result is stored.

Future calls can retrieve it directly.

---

## Memoization vs Caching

These concepts are closely related, but they're not exactly the same.

**Caching** is the broader concept of storing data so it can be retrieved faster later.

**Memoization** specifically refers to caching the **result of a function based on its inputs**.

For example:

```text
Caching:
Database query → store result → reuse result

Memoization:
calculate(10) → store result for input 10 → reuse result
```

You can think of memoization as a specific form of caching.

---

## A Classic Example: Fibonacci

Memoization becomes much more useful when a function repeatedly calculates the same values.

Consider Fibonacci:

```csharp
int Fibonacci(int n)
{
    if (n <= 1)
        return n;

    return Fibonacci(n - 1) + Fibonacci(n - 2);
}
```

For something like:

```csharp
Fibonacci(5)
```

the function calculates some values multiple times.

For larger values, the number of repeated calculations grows rapidly.

With memoization:

```csharp
Dictionary<int, int> cache = new();

int Fibonacci(int n)
{
    if (n <= 1)
        return n;

    if (cache.TryGetValue(n, out var result))
        return result;

    result = Fibonacci(n - 1) + Fibonacci(n - 2);

    cache[n] = result;

    return result;
}
```

Now, once `Fibonacci(3)` has been calculated, we don't calculate it from scratch every time we need it.

We simply reuse the stored result.

---

## When Should You Use Memoization?

Memoization is useful when:

* A function is expensive to execute.
* The same inputs occur repeatedly.
* The function is deterministic.
* The result doesn't change for the same input.

For example:

```text
Input: 5
Output: 120
```

If `5` will always produce `120`, storing that result makes sense.

Memoization is especially common in:

* Recursive algorithms
* Dynamic programming
* Mathematical calculations
* Parsing and processing
* Expensive transformations
* Applications with repeated computations

---

## When Memoization Can Be a Bad Idea

Memoization isn't free.

You're trading **memory for speed**.

Every cached result takes memory, and if the inputs are constantly changing, the cache may provide very little benefit.

For example:

```text
calculate(1)
calculate(2)
calculate(3)
calculate(4)
calculate(5)
...
calculate(1,000,000)
```

If every input is unique, you're storing lots of results without getting many cache hits.

You also need to think about **stale data** if the underlying result can change.

---

## The Key Takeaway

Memoization is a simple idea:

> **If you've already done the work for the same input, don't do it again.**

Store the result.

Reuse it.

Save computation.

But remember the trade-off:

**Memoization improves performance by spending memory.**

The real skill isn't just knowing how to memoize.

It's knowing **when repeated computation is expensive enough to justify caching the result.**
