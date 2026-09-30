# IEnumerable vs IQueryable: Where Does Your Query Actually Run?

You write a simple LINQ query, and it looks almost identical whether you're using `IEnumerable` or `IQueryable`.

But there's a major difference:

**With `IEnumerable`, the filtering usually happens in your application. With `IQueryable`, the query can be translated and executed by the data source itself.**

That difference matters a lot when you're working with databases.

## IEnumerable: Work With Data Already in Memory

`IEnumerable<T>` is designed for iterating over data in memory.

For example:

```csharp
IEnumerable<User> users = db.Users.ToList();

var result = users
    .Where(u => u.Age > 18)
    .ToList();
```

Here, `ToList()` executes the database query first and loads the users into memory.

Then:

```csharp
.Where(u => u.Age > 18)
```

runs against the in-memory collection.

So if your database contains 100,000 users, you could potentially load all 100,000 users before filtering them.

## IQueryable: Let the Data Source Do the Work

`IQueryable<T>` represents a query that can be translated into a query language understood by the underlying data source.

For example:

```csharp
IQueryable<User> users = db.Users;

var result = users
    .Where(u => u.Age > 18)
    .ToList();
```

The `Where` isn't immediately executed.

Instead, Entity Framework Core builds an expression tree representing the query.

When `ToList()` is called, EF Core can translate it into SQL similar to:

```sql
SELECT *
FROM Users
WHERE Age > 18;
```

The database performs the filtering and returns only the matching records.

## The Important Difference

The key difference isn't simply:

> "`IEnumerable` is for collections and `IQueryable` is for databases."

It's about **where the query logic is executed**.

With `IEnumerable`:

```text
Database → Application → Filter
```

With `IQueryable`:

```text
Database → Filter → Application
```

This becomes especially important when you're working with large datasets.

## A Common Mistake

Consider this:

```csharp
var users = db.Users.ToList();

var adults = users
    .Where(u => u.Age > 18)
    .ToList();
```

The database query has already executed at `ToList()`.

The filtering happens afterward in memory.

Compare that with:

```csharp
var adults = db.Users
    .Where(u => u.Age > 18)
    .ToList();
```

Now the filtering can be translated into SQL and performed by the database.

## What About `AsEnumerable()`?

This is where things can get interesting.

```csharp
var users = db.Users
    .Where(u => u.Age > 18)
    .AsEnumerable()
    .Where(u => SomeCustomMethod(u));
```

The first `Where` can be translated to SQL.

But after `AsEnumerable()`, LINQ-to-Objects takes over.

So the query effectively becomes:

```text
Database filtering
        ↓
Results returned
        ↓
Application filtering
```

This can be useful when you need to perform operations that the database provider cannot translate.

## When Should You Use Each?

Use **`IQueryable`** when you're building queries against a data source such as a database and want filtering, projection, sorting, and other supported operations to be executed by that source.

Use **`IEnumerable`** when you're already working with data in memory or intentionally want LINQ-to-Objects processing.

The important thing is not to blindly choose one interface over the other.

Understand **when execution happens and where the work is being performed.**

## Key Takeaway

`IEnumerable` and `IQueryable` can look almost identical in your code, but they can behave very differently.

**`IEnumerable` generally means your LINQ operations are performed in memory. `IQueryable` allows the query to be translated and executed by the underlying data source.**

When working with databases, understanding that difference can help you avoid unnecessary data retrieval and improve application performance.
