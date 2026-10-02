# Records vs Classes in C#: When Should You Use Each?

**Not every object needs to be compared by its identity. Sometimes, what matters is simply the data it holds.**

That distinction is exactly what separates **records from classes** in C#.

Both can hold data, define methods, and support object-oriented programming. However, they behave differently when it comes to equality, immutability, and how you model your application.

Let's break down the differences and see when to use each.

## 1. What Is a Class?

A class is a reference type commonly used to represent objects with identity, behavior, and potentially changing state.

For example, consider a user account:

```csharp
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }

    public void UpdateName(string name)
    {
        Name = name;
    }
}
```

You can create two user objects with identical properties:

```csharp
var user1 = new User { Id = 1, Name = "Ahmed" };
var user2 = new User { Id = 1, Name = "Ahmed" };

Console.WriteLine(user1 == user2); // False
```

Why?

Because classes use **reference equality by default**. Even though both objects contain the same data, they are two separate instances.

This makes classes suitable for entities whose identity matters, such as users, orders, and products.

## 2. What Is a Record?

A record is a type designed to represent data, particularly when equality should be based on its values rather than its identity.

Here's the same example using a record:

```csharp
public record User(int Id, string Name);
```

Now, create two instances:

```csharp
var user1 = new User(1, "Ahmed");
var user2 = new User(1, "Ahmed");

Console.WriteLine(user1 == user2); // True
```

Unlike a regular class, a record automatically provides **value-based equality**.

If two records have the same values, they are considered equal.

Records also support convenient features such as `with` expressions, which make it easy to create modified copies:

```csharp
var updatedUser = user1 with { Name = "John" };
```

The original record remains unchanged.

By default, positional records also expose init-only properties, making them useful for immutable-style data modeling.

## 3. Records vs Classes: The Key Differences

| Feature               | Class                                    | Record                                        |
| --------------------- | ---------------------------------------- | --------------------------------------------- |
| Equality              | Reference equality by default            | Value-based equality                          |
| Mutability            | Supports mutable properties              | Supports mutable or init-only properties      |
| `with` expressions    | Not supported by default                 | Supported                                     |
| Inheritance           | Supports class inheritance               | Record classes support record inheritance     |
| Best suited for       | Objects with identity and changing state | Data models where value equality matters      |
| Object initialization | Flexible                                 | Supports positional and object initialization |

One important detail: records are not automatically immutable in every situation. You can define mutable properties inside a record, and records can also contain references to mutable objects.

Similarly, records are not inherently faster or more memory-efficient than classes.

## 4. When Should You Use a Class?

Use a class when the identity of an object matters or when it represents something whose state changes over time.

For example, an order in an e-commerce application:

```csharp
public class Order
{
    public int Id { get; set; }
    public string Status { get; set; }

    public void Complete()
    {
        Status = "Completed";
    }
}
```

Two orders with the same status and other property values are not necessarily the same order.

Their identities matter because they represent separate transactions.

Classes are also a natural fit for many services, repositories, and other components that encapsulate behavior.

## 5. When Should You Use a Record?

Use a record when you're modeling data where the values matter more than the identity.

For example, a payment request:

```csharp
public record PaymentRequest(
    decimal Amount,
    string Currency,
    string Reference
);
```

Two payment requests with identical values can be considered equal.

Records are particularly useful for:

* DTOs (Data Transfer Objects)
* API request and response models
* Configuration objects
* Event messages
* Immutable data structures

They reduce boilerplate and make value-based comparisons straightforward.

However, using a record for a DTO doesn't automatically make it immutable throughout your application. Choose your property definitions and nested types carefully.

## 6. A Common Mistake: Assuming Records Are Always Immutable

Consider this example:

```csharp
public record Product
{
    public string Name { get; set; }
    public decimal Price { get; set; }
}
```

Although `Product` is a record, its properties can still be modified:

```csharp
var product = new Product
{
    Name = "Laptop",
    Price = 1200
};

product.Price = 1500;
```

This is perfectly valid.

If you want to encourage immutability, use init-only properties instead:

```csharp
public record Product(
    string Name,
    decimal Price
);
```

Now, you can create a modified copy with `with`:

```csharp
var updatedProduct = product with { Price = 1500 };
```

Remember that this creates a shallow copy. If a record contains a mutable reference-type property, the copied record may still share that underlying object.

---

## Key Takeaway

**Classes are generally suited to objects where identity and changing state matter. Records are designed for data where value-based equality and convenient copying are useful.**

Neither is universally better.

The real skill is understanding what you're modeling and choosing the type that expresses its behavior and purpose most clearly.
