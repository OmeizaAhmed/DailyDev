# Process vs Thread: What’s Really Running Your Application?

When your application is running, it isn’t just “executing code.”

The operating system is managing **processes** and **threads** behind the scenes.

Understanding the difference matters because it affects performance, memory usage, concurrency, and how applications communicate.

## What Is a Process?

A **process** is a running instance of a program.

For example, when you open Chrome, your operating system creates one or more processes to run it.

A process has its own:

* Memory space
* Resources
* File handles
* Security context
* Threads

Think of a process as a **house**.

The house has its own resources and is separated from other houses.

```text
Process A
├── Memory
├── Resources
└── Threads
```

If Process A crashes, Process B generally isn't directly affected because they have separate memory spaces.

## What Is a Thread?

A **thread** is an execution path inside a process.

A process can contain multiple threads that execute work concurrently.

```text
Process
├── Thread 1
├── Thread 2
└── Thread 3
```

Using the house analogy:

> A process is the house.
> Threads are the people working inside it.

They share the house's resources, but each person can work on a different task.

## The Biggest Difference

The most important difference is **memory**.

Processes have separate memory spaces.

Threads within the same process share memory.

```text
Process A                 Process B
┌───────────────┐         ┌───────────────┐
│ Shared by     │         │ Shared by     │
│ its threads   │         │ its threads   │
│               │         │               │
│ Thread 1      │         │ Thread 1      │
│ Thread 2      │         │ Thread 2      │
└───────────────┘         └───────────────┘
```

Threads in Process A cannot simply access Process B's memory.

But Thread 1 and Thread 2 inside Process A can access the same process memory.

That's useful for communication, but it also introduces problems such as **race conditions** and the need for synchronization.

## Process vs Thread

| Process                        | Thread                          |
| ------------------------------ | ------------------------------- |
| Independent running program    | Execution unit inside a process |
| Has its own memory space       | Shares process memory           |
| More expensive to create       | Cheaper to create               |
| Communication is more involved | Communication is easier         |
| Better isolation               | Less isolation                  |
| Can contain multiple threads   | Belongs to a process            |

## A Practical Example

Imagine a web server receiving requests.

Instead of processing everything sequentially:

```text
Request 1 → Process
Request 2 → Process
Request 3 → Process
```

A process can use multiple threads:

```text
Web Server Process
├── Thread 1 → Request 1
├── Thread 2 → Request 2
└── Thread 3 → Request 3
```

Now multiple pieces of work can be handled concurrently.

Modern applications often go further and use **thread pools**, asynchronous programming, processes, or a combination of these rather than manually creating a new thread for every request.

## What Happens When a Thread Crashes?

This is another important distinction.

A problem in one thread can potentially affect the entire process because threads share the process's resources.

A process provides stronger isolation.

For example:

```text
Process A              Process B
   │                      │
 Thread 1              Thread 1
 Thread 2              Thread 2
   │
   X Crash
```

A failure in Process A doesn't automatically mean Process B crashes.

That's one reason operating systems use processes as an important isolation boundary.

## When Should You Think About Processes vs Threads?

Think about **processes** when isolation and independent resources matter.

Think about **threads** when multiple tasks need to execute within the same application and share data efficiently.

But in modern development, you usually won't manually choose between them for every task.

Frameworks and runtimes such as .NET provide abstractions like:

* `Task`
* `async/await`
* ThreadPool
* Background services

These allow you to handle concurrent work without manually managing every operating-system thread.

## Key Takeaway

A **process is an isolated running program with its own memory space.**

A **thread is an execution unit inside a process that shares the process's memory.**

The simplest way to remember it:

> **Process = container. Thread = worker inside the container.**

Once you understand that relationship, concepts like concurrency, parallelism, thread safety, synchronization, and async programming become much easier to reason about.
