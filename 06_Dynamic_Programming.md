# Dynamic Programming: Stop Solving the Same Problem Twice

Ever written an algorithm that keeps calculating the same thing over and over again?

That is exactly the kind of problem **Dynamic Programming (DP)** is designed to solve.

The idea is simple:

> **Solve a problem once, remember the result, and reuse it.**

## What Is Dynamic Programming?

Dynamic programming is an algorithmic technique for solving complex problems by breaking them into smaller subproblems, solving those subproblems, and storing their results.

DP is especially useful when a problem has two characteristics:

* **Overlapping subproblems** — the same smaller problems are solved repeatedly.
* **Optimal substructure** — the solution to the larger problem can be built from solutions to smaller problems.

Let's make this practical.

## The Classic Example: Fibonacci

The Fibonacci sequence looks like this:

```text
0, 1, 1, 2, 3, 5, 8, 13...
```

Each number is calculated using:

```text
F(n) = F(n - 1) + F(n - 2)
```

A simple recursive implementation would be:

```csharp
int Fibonacci(int n)
{
    if (n <= 1)
        return n;

    return Fibonacci(n - 1) + Fibonacci(n - 2);
}
```

It works.

But there is a problem.

The function repeatedly calculates the same values.

For example, when calculating `F(5)`, the program calculates `F(3)` multiple times.

As `n` becomes larger, the amount of repeated work grows rapidly.

## Memoization: Remember What You Already Solved

One way to solve this is **memoization**.

The idea is straightforward:

> If we've already calculated something, don't calculate it again.

```csharp
int Fibonacci(int n, Dictionary<int, int> memo)
{
    if (n <= 1)
        return n;

    if (memo.ContainsKey(n))
        return memo[n];

    memo[n] = Fibonacci(n - 1, memo)
            + Fibonacci(n - 2, memo);

    return memo[n];
}
```

Now, once `Fibonacci(3)` has been calculated, we store it.

The next time we need it, we simply retrieve the stored value.

This eliminates a huge amount of unnecessary work.

## Tabulation: Solve From the Bottom Up

There is another common approach called **tabulation**.

Instead of starting with the big problem and recursively breaking it down, we start with the smallest problems and build toward the answer.

```csharp
int Fibonacci(int n)
{
    if (n <= 1)
        return n;

    int[] dp = new int[n + 1];

    dp[0] = 0;
    dp[1] = 1;

    for (int i = 2; i <= n; i++)
    {
        dp[i] = dp[i - 1] + dp[i - 2];
    }

    return dp[n];
}
```

Here, `dp[i]` stores the answer for the `i`th Fibonacci number.

We calculate each value once and use previously calculated values to build the next one.

So there are two common approaches:

**Memoization**

* Top-down
* Usually recursive
* Stores results as they are needed

**Tabulation**

* Bottom-up
* Usually iterative
* Builds the solution from smaller problems

Both solve the same fundamental problem:

> **Avoid unnecessary repeated work.**

## Why Does DP Matter?

Consider what happens when your algorithm repeatedly solves the same subproblem.

Without optimization:

```text
Solve A
├── Solve B
│   ├── Solve D
│   └── Solve E
└── Solve C
    ├── Solve D
    └── Solve E
```

Notice that `D` and `E` are solved more than once.

With dynamic programming:

```text
Solve D → Store result
Solve E → Store result
        ↓
Reuse them whenever needed
```

You're replacing repeated computation with stored information.

For Fibonacci, the difference is significant:

```text
Naive recursion:       O(2ⁿ)
Dynamic programming:   O(n)
```

That's why recognizing DP can make a huge difference in algorithm performance.

## Where Is Dynamic Programming Used?

Dynamic programming appears in many problems, including:

* 0/1 Knapsack
* Coin Change
* Longest Common Subsequence
* Longest Increasing Subsequence
* Edit Distance
* Shortest Path problems
* Scheduling
* Resource allocation
* Counting combinations

These problems may look completely different, but many share the same underlying pattern:

> **The solution can be built from smaller solutions, and those smaller solutions may be reused.**

## How Do You Recognize a DP Problem?

When you're solving an algorithmic problem, ask yourself:

### 1. Can I break the problem into smaller problems?

If yes, that's a good starting point.

### 2. Do the same smaller problems appear repeatedly?

If yes, DP might be useful.

### 3. Can I store the result of those smaller problems?

If yes, you can potentially avoid repeating the work.

A useful mental model is:

```text
Complex Problem
      ↓
Smaller Problems
      ↓
Solve each problem
      ↓
Store the results
      ↓
Reuse the results
      ↓
Build the final answer
```

## DP Isn't Just "Using an Array"

This is an important distinction.

Simply creating an array called `dp` doesn't make an algorithm dynamic programming.

The important part is the **problem-solving strategy**.

You're identifying smaller subproblems, recognizing that they overlap, storing their solutions, and using those solutions to solve the larger problem.

The array is just one way to store the knowledge you've gained.

## The Bigger Lesson

Dynamic programming can initially feel complicated because many DP problems come with unfamiliar formulas and patterns.

But the underlying idea is much simpler:

> **Don't do the same work twice.**

When you notice your algorithm repeatedly solving the same problem, ask yourself:

**"Can I remember the answer and reuse it?"**

That question is often the beginning of a dynamic programming solution.

## Key Takeaway

Dynamic programming isn't about memorizing dozens of formulas.

It's about recognizing **repeated work**.

Break the problem down.
Solve the smaller problems.
Store their results.
Reuse them.

Once you start seeing problems this way, dynamic programming becomes much less intimidating.
