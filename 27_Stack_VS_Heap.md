# Stack vs Heap in .NET: Where Does Your Data Actually Live?

When you write C# code, you create variables, objects, method calls, and data structures.

But where does all that data actually go?

The answer usually comes down to two areas of memory: **the stack and the heap**.

Understanding the difference helps you reason about memory usage, object lifetime, performance, and garbage collection in .NET.

## The Stack

The **stack** is used primarily for managing method calls and their associated data.

When a method is called, a stack frame is created. When the method returns, that frame is removed.

For example:

```csharp
void Calculate()
{
    int number = 10;
    int result = number * 2;
}
```

The method's execution data is associated with its stack frame.

The stack is fast because memory is managed in a predictable last-in, first-out (LIFO) manner.

Think of it like a stack of plates:

* The last plate you put on is the first one you remove.
* Method calls work in a similar way.

## The Heap

The **managed heap** is where objects are allocated.

For example:

```csharp
var user = new User
{
    Name = "Ahmed"
};
```

The `User` object is allocated on the managed heap.

The variable `user` holds a reference to that object.

When the object is no longer reachable, the .NET **Garbage Collector (GC)** can eventually reclaim its memory.

This is one of the major differences from languages where developers manually allocate and free memory.

## Value Types vs Reference Types

This is where the topic often gets oversimplified.

You may have heard:

> "Value types go on the stack, and reference types go on the heap."

That's a useful beginner's rule, but it isn't always correct.

Consider:

```csharp
int age = 25;

User user = new User();
```

`int` is a value type, while `User` is a reference type.

But the actual memory location depends on **where the variable is stored and how it is being used**.

For example, a value-type field inside a class is part of the object and therefore lives with that object on the heap.

```csharp
class User
{
    public int Age { get; set; }
}
```

Here, `Age` is a value type, but it is part of a heap-allocated `User` object.

So don't think:

**Value type = stack**

**Reference type = heap**

Instead, think about **storage context and object lifetime**.

## What About Strings?

Strings are reference types:

```csharp
string name = "Ahmed";
```

The string object is managed by the .NET runtime and is generally allocated on the managed heap.

Strings are also immutable. When you appear to modify a string, you are typically creating another string object rather than changing the existing one.

```csharp
string name = "Ahmed";

name = name + " Omeiza";
```

The original string isn't modified.

A new string is created.

## What Does the Garbage Collector Do?

The heap is managed by the .NET Garbage Collector.

When objects are no longer reachable, the GC can reclaim their memory.

For example:

```csharp
void CreateUser()
{
    var user = new User();
}
```

After the method finishes, the local reference is gone.

If nothing else references the `User` object, it eventually becomes eligible for garbage collection.

Importantly, **eligible for collection doesn't mean immediately deleted**.

The GC decides when collection should occur.

## Why Should Developers Care?

Because memory management affects application performance.

Creating large numbers of short-lived objects can increase garbage collection activity.

For example:

```csharp
for (int i = 0; i < 1_000_000; i++)
{
    var user = new User();
}
```

This creates many objects that will eventually need to be collected.

That doesn't automatically mean the code is bad. Modern .NET is very good at handling allocations.

The important lesson is to understand where allocations happen and avoid unnecessary allocations when performance actually matters.

## Stack vs Heap

A simple mental model:

**Stack**

* Stores method execution data.
* Has a predictable lifetime.
* Automatically cleaned up as methods return.
* Generally very fast.

**Managed Heap**

* Stores objects and other dynamically allocated data.
* Objects can survive beyond the method that created them.
* Managed by the Garbage Collector.
* Allocation and collection have runtime costs.

But remember: **the simplified "stack = value types, heap = reference types" rule is not a reliable description of how .NET actually works.**

## The Key Takeaway

The stack and heap aren't just two places where .NET randomly puts variables.

They are parts of a broader memory-management model.

The stack is closely tied to **method execution and scoped data**, while the managed heap is primarily where **objects are allocated and managed by the Garbage Collector**.

As a .NET developer, understanding this distinction helps you reason about **allocations, object lifetimes, garbage collection, and application performance**.
