# Cursor Pagination vs Offset Pagination: Which One Should You Use?

Pagination looks simple until your dataset gets large.

At first, `page=2&limit=20` seems perfectly fine. But as your application grows, pagination strategy can start affecting **query performance, consistency, and user experience**.

Two common approaches are **offset pagination** and **cursor pagination**.

Let’s break down how they work and when to use each.

## Offset Pagination

Offset pagination works by telling the database:

> Skip these records and give me the next batch.

For example:

```http
GET /api/products?page=3&limit=20
```

The database might translate this into:

```sql
SELECT *
FROM Products
ORDER BY Id
OFFSET 40 ROWS
FETCH NEXT 20 ROWS ONLY;
```

The first page skips `0` records, the second skips `20`, and the third skips `40`.

### The problem with large offsets

As the offset gets larger, the database may have to scan and skip a lot of rows before returning the records you actually want.

Imagine requesting:

```http
GET /api/products?page=50000&limit=20
```

The database potentially has to work through a huge number of records before returning just 20.

This can become expensive with large datasets.

There is another problem: **changing data**.

Imagine you're viewing page 2 while new records are inserted at the beginning of the dataset.

Records can shift between pages, meaning you might:

* See the same record twice
* Miss a record
* Get inconsistent results between requests

---

## Cursor Pagination

Cursor pagination takes a different approach.

Instead of saying:

> Skip 40 records.

You say:

> Give me the records after this specific position.

For example:

```http
GET /api/products?limit=20&cursor=eyJpZCI6NDA...
```

The cursor represents a position in the dataset.

A simplified query could look like:

```sql
SELECT *
FROM Products
WHERE Id > 40
ORDER BY Id
LIMIT 20;
```

The response might contain:

```json
{
  "data": [
    // 20 products
  ],
  "nextCursor": "eyJpZCI6NjA..."
}
```

The client uses `nextCursor` to request the next batch.

```http
GET /api/products?limit=20&cursor=eyJpZCI6NjA...
```

The database can efficiently find records after the cursor, especially when the ordering column is properly indexed.

---

## The Key Difference

The simplest way to remember it:

**Offset pagination asks:**

> "How many records should I skip?"

**Cursor pagination asks:**

> "Where should I continue from?"

That difference becomes important as your dataset grows.

|                                   | Offset | Cursor |
| --------------------------------- | ------ | ------ |
| Simple to implement               | ✅      | ⚠️     |
| Jump directly to page 50          | ✅      | ❌      |
| Good for large datasets           | ⚠️     | ✅      |
| Handles changing datasets well    | ⚠️     | ✅      |
| Works well for infinite scrolling | ⚠️     | ✅      |
| Requires a stable ordering        | Yes    | Yes    |
| Easy page numbers                 | ✅      | ❌      |

## When Should You Use Offset Pagination?

Offset pagination is often a good choice for:

* Admin dashboards
* Search results
* Small-to-medium datasets
* Interfaces where users need page numbers
* Applications where jumping to a specific page matters

For example:

```http
GET /api/users?page=5&limit=25
```

It's simple, familiar, and often perfectly adequate.

Don't introduce cursor pagination just because your application uses a database.

---

## When Should You Use Cursor Pagination?

Cursor pagination becomes more useful when:

* Your dataset is large
* Data changes frequently
* You're building infinite scrolling
* You're building feeds or timelines
* Consistent traversal matters
* You need efficient pagination deep into a dataset

For example, social media feeds are a natural fit.

Instead of asking:

```text
Give me page 50.
```

The client asks:

```text
Give me the next 20 posts after this cursor.
```

---

## One Important Detail: Your Cursor Needs an Order

Cursor pagination isn't simply:

```sql
WHERE Id > cursor
```

and you're done.

Your ordering needs to be **stable and deterministic**.

For example, if you're ordering by:

```sql
CreatedAt DESC
```

multiple records could have the same timestamp.

A common solution is using a combination such as:

```sql
ORDER BY CreatedAt DESC, Id DESC
```

The cursor can then contain both values:

```json
{
  "createdAt": "2026-09-23T12:30:00Z",
  "id": 4821
}
```

This gives the database an unambiguous position to continue from.

In production systems, cursors are also commonly encoded or signed so clients don't have to manipulate internal pagination values directly.

---

## So Which Should You Choose?

Don't choose based on which one sounds more advanced.

Choose based on the requirements of your application.

If you need **simple page navigation**, offset pagination may be enough.

If you need **efficient traversal through a large, frequently changing dataset**, cursor pagination is often a better fit.

### Key Takeaway

**Offset pagination is about pages. Cursor pagination is about position.**

Offset pagination is simpler and gives you familiar page-based navigation.

Cursor pagination requires more thought, but it can provide more consistent and efficient traversal as your dataset grows.

The best pagination strategy isn't the most sophisticated one.

It's the one that fits the way your users actually consume your data.
