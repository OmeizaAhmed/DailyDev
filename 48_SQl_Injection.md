# SQL Injection: When User Input Becomes a Database Command

Imagine building a login system that checks a user's email and password against a database.

Everything works perfectly until someone enters something unexpected into the login form.

Suddenly, your application is executing SQL commands you never intended.

That's the danger of **SQL Injection (SQLi)**, one of the oldest and most dangerous vulnerabilities in web application security.

## What Is SQL Injection?

SQL Injection is a security vulnerability that occurs when an application allows untrusted input to change the structure of a SQL query.

Instead of treating user input as data, the database ends up interpreting parts of it as SQL instructions.

Depending on the vulnerability and database permissions, attackers might be able to:

- Access sensitive information.
- Bypass authentication.
- Modify or delete records.
- Execute unauthorized database operations.

The underlying problem is simple: **the application fails to separate data from executable SQL.**

## How Does SQL Injection Work?

Consider a C# application that retrieves a user from a database.

```csharp
var query = $"SELECT * FROM Users WHERE Email = '{email}'";
```

At first glance, this looks reasonable.

If the user enters:

```text
john@example.com
```

The resulting query becomes:

```sql
SELECT * FROM Users
WHERE Email = 'john@example.com';
```

Everything works as expected.

But what happens if someone enters:

```sql
' OR 1=1 --
```

Your application constructs:

```sql
SELECT * FROM Users
WHERE Email = '' OR 1=1 --';
```

Here's what happens:

- The first `'` closes the string.
- `OR 1=1` introduces a condition that is always true.
- `--` comments out the remaining SQL.

Instead of retrieving one specific user, the database may return every user.

And if an application's authentication logic incorrectly trusts that result, the consequences can be even worse.

## How Do You Prevent SQL Injection?

### 1. Use Parameterized Queries

The most important defense is to separate SQL commands from user-supplied values.

Instead of concatenating strings:

```csharp
var query = $"SELECT * FROM Users WHERE Email = '{email}'";
```

Use parameters:

```csharp
using var command = new NpgsqlCommand(
    "SELECT * FROM Users WHERE Email = @email",
    connection
);

command.Parameters.AddWithValue("email", email);

using var reader = await command.ExecuteReaderAsync();
```

Now, even if someone enters malicious SQL syntax, the database treats it as a value rather than executable SQL.

**The query structure stays unchanged.**

### 2. Use an ORM Like Entity Framework Core

If you're working with .NET, Entity Framework Core makes it easier to write queries safely.

```csharp
var user = await context.Users
    .FirstOrDefaultAsync(u => u.Email == email);
```

EF Core translates this LINQ expression into a parameterized SQL query.

However, using an ORM doesn't automatically eliminate every SQL Injection risk.

For example, this is still dangerous:

```csharp
var users = await context.Users
    .FromSqlRaw(
        $"SELECT * FROM Users WHERE Email = '{email}'"
    )
    .ToListAsync();
```

The safer approach is:

```csharp
var users = await context.Users
    .FromSqlInterpolated(
        $"SELECT * FROM Users WHERE Email = {email}"
    )
    .ToListAsync();
```

Here, EF Core converts the interpolated value into a database parameter.

**Important:** `FromSqlRaw` can also be safe when used correctly with explicit parameters. The vulnerability comes from inserting untrusted values directly into SQL text.

### 3. Apply the Principle of Least Privilege

Your application shouldn't connect to the database using an account with unnecessary administrative permissions.

For example, if a service only needs to read records, its database account shouldn't have permission to delete tables.

This doesn't prevent SQL Injection itself, but it can significantly reduce the damage if a vulnerability is exploited.

### 4. Validate Input, but Don't Rely on Validation Alone

Input validation is important.

You can validate email formats, enforce length limits, and reject unexpected values.

However, validation is not a replacement for parameterized queries.

Attackers can find ways around weak validation rules, but properly parameterized values cannot alter the SQL query structure.

## The Key Takeaway

SQL Injection isn't really about attackers knowing clever SQL commands.

It's about an application allowing user-controlled data to become executable instructions.

Whether you're using raw SQL, Dapper, ADO.NET, or Entity Framework Core, the principle remains the same:

**Never build SQL queries by concatenating untrusted input.**

Use parameterized queries, limit database permissions, and treat every external input as untrusted.

Because sometimes, the difference between a secure application and a database breach is how you construct a single SQL query.