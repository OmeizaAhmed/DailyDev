# Deadlocks: When Your Threads Are Waiting Forever

Your application is running. No exception. No crash.

But two requests are stuck indefinitely.

Welcome to a **deadlock**.

A deadlock happens when two or more threads are waiting for resources held by each other, so none of them can continue.

---

## What Is a Deadlock?

Imagine two developers:

* Developer A has **Resource 1** and needs **Resource 2**.
* Developer B has **Resource 2** and needs **Resource 1**.

Both are waiting.

Neither can move forward.

That's essentially what happens with threads and locks.

```text
Thread A → holds Lock 1 → waiting for Lock 2
Thread B → holds Lock 2 → waiting for Lock 1

Result: DEADLOCK
```

The important part is that **both threads are waiting for each other**.

---

## A Simple C# Example

Consider this:

```csharp
object lockA = new();
object lockB = new();

void MethodA()
{
    lock (lockA)
    {
        Thread.Sleep(100);

        lock (lockB)
        {
            Console.WriteLine("Method A");
        }
    }
}

void MethodB()
{
    lock (lockB)
    {
        Thread.Sleep(100);

        lock (lockA)
        {
            Console.WriteLine("Method B");
        }
    }
}
```

If two threads execute these methods at the same time:

```text
Thread 1:
Locks A
↓
Waits for B

Thread 2:
Locks B
↓
Waits for A
```

Neither thread can acquire the lock it needs.

The application can remain stuck indefinitely.

---

## Why Do Deadlocks Happen?

Deadlocks usually involve a combination of these conditions:

### 1. Mutual Exclusion

A resource can only be used by one thread at a time.

```text
Thread A → owns Resource X
Thread B → must wait
```

### 2. Hold and Wait

A thread holds one resource while waiting for another.

```text
Thread A:
Holding X
Waiting for Y
```

### 3. No Preemption

A resource cannot simply be taken away from the thread holding it.

The thread must release it.

### 4. Circular Wait

Thread A waits for Thread B, while Thread B waits for Thread A.

```text
A → waiting for B
B → waiting for A
```

This circular dependency is what ultimately creates the deadlock.

---

## How Do You Prevent Deadlocks?

One of the simplest strategies is to **always acquire locks in the same order**.

Instead of:

```csharp
// Method A
lock (lockA)
{
    lock (lockB)
}
```

and:

```csharp
// Method B
lock (lockB)
{
    lock (lockA)
}
```

Make both follow the same order:

```csharp
lock (lockA)
{
    lock (lockB)
    {
        // Work
    }
}
```

Now both threads agree:

```text
Lock A → Lock B
```

There is no circular waiting.

---

## Other Ways to Reduce the Risk

### Keep Lock Scope Small

Don't hold a lock longer than necessary.

```csharp
lock (resource)
{
    // Only critical work
}
```

Avoid performing slow operations such as network calls or database requests while holding a lock.

### Avoid Unnecessary Multiple Locks

The more locks you need to coordinate, the more opportunities you create for circular dependencies.

### Use Async Carefully

Async code can introduce its own synchronization problems.

Avoid blocking asynchronous operations with:

```csharp
.Result
```

or:

```csharp
.Wait()
```

Prefer:

```csharp
await SomeOperationAsync();
```

This doesn't automatically eliminate every deadlock, but it avoids an important class of blocking problems.

---

## Deadlock vs Race Condition

These problems are often confused.

A **race condition** happens when multiple threads access shared data and the result depends on the timing of their execution.

A **deadlock** happens when threads are stuck waiting for each other.

Think of it this way:

**Race condition:**
"Who gets there first?"

**Deadlock:**
"Nobody can move."

---

## Key Takeaway

A deadlock isn't necessarily caused by a bug that crashes your application.

Sometimes the more dangerous bug is the one that makes your application **wait forever**.

When working with multiple locks, remember:

> **Acquire locks consistently, keep lock scope small, and avoid unnecessary blocking.**

Good concurrency isn't just about making multiple things run at once. It's also about making sure they can actually **finish**.
