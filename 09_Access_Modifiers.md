# C# Access Modifiers: Who Gets to Touch Your Code?

One of the easiest ways to create messy C# code is to make everything accessible.

Access modifiers let you control **who can access a class, method, property, or field**.

Think of them as the visibility rules of your code.

---

## What Are Access Modifiers?

An access modifier defines the level of access a type or its members have.

For example:

```csharp
public class User
{
    public string Name { get; set; }

    private string Password { get; set; }
}
```

Here, `Name` can be accessed from outside the class, while `Password` can only be accessed inside `User`.

```csharp
var user = new User();

user.Name = "Ahmed";     // Allowed
user.Password = "1234";  // Not allowed
```

This is **encapsulation** in practice: expose what other parts of your application need and hide what they don't.

---

## The Main Access Modifiers in C#

### `public`

Accessible from anywhere that can access the containing type.

```csharp
public class User
{
    public void Login()
    {
        // ...
    }
}
```

Other classes can call:

```csharp
var user = new User();
user.Login();
```

Use `public` when something is intentionally part of your class's external API.

---

### `private`

Accessible only within the containing type.

```csharp
public class BankAccount
{
    private decimal balance;

    public void Deposit(decimal amount)
    {
        balance += amount;
    }
}
```

Other classes cannot directly modify `balance`.

```csharp
account.balance = 5000; // Not allowed
```

This protects the internal state of the object.

**`private` is the default access level for class members.**

---

### `protected`

Accessible within the containing class and classes that inherit from it.

```csharp
public class Animal
{
    protected string Name = "Animal";
}

public class Dog : Animal
{
    public void ShowName()
    {
        Console.WriteLine(Name);
    }
}
```

`Dog` can access `Name` because it inherits from `Animal`.

But unrelated classes cannot.

---

### `internal`

Accessible anywhere within the **same assembly**.

```csharp
internal class PaymentService
{
    public void ProcessPayment()
    {
    }
}
```

If your application is compiled into one assembly, other code in that same assembly can access `PaymentService`.

But another assembly cannot access it unless you explicitly expose it.

This is particularly useful when building applications with multiple projects.

---

### `protected internal`

This combines `protected` and `internal`.

A member can be accessed:

* From anywhere in the same assembly, **or**
* From a derived class in another assembly.

```csharp
protected internal void Process()
{
}
```

It essentially means:

> "Allow access to members of this assembly or derived classes."

---

### `private protected`

This is more restrictive.

A member can only be accessed by:

* The containing class, or
* Derived classes **within the same assembly**.

```csharp
private protected void Process()
{
}
```

It's useful when you want inheritance-based access but don't want derived classes from other assemblies to access the member.

---

## A Simple Way to Remember Them

Think of your application like a building:

* **`public`** → Anyone with access to the building can enter.
* **`internal`** → Only people working in this building can enter.
* **`protected`** → Family members/inheritors can enter.
* **`private`** → Only you can enter.
* **`protected internal`** → People in the building or your inheritors can enter.
* **`private protected`** → Only you and inheritors working in the same building can enter.

---

## Why Access Modifiers Matter

Without access control, every part of your application could directly manipulate everything.

That makes code harder to maintain and easier to break.

Instead of:

```csharp
public decimal balance;
```

you can hide the state:

```csharp
private decimal balance;

public void Deposit(decimal amount)
{
    if (amount > 0)
        balance += amount;
}
```

Now the class controls **how** its balance can change.

That's the real value of access modifiers.

---

## Don't Make Everything `public`

A common beginner mistake is:

> "If another class needs it, make it public."

Not necessarily.

Start with the **most restrictive access level** that works.

If something only belongs inside a class, make it `private`.

If it's part of the application's internal implementation, consider `internal`.

If derived classes need it, consider `protected`.

Only expose something as `public` when it genuinely needs to be part of the public contract.

---

## Key Takeaway

**Access modifiers aren't just keywords—they are boundaries.**

They define what other parts of your application are allowed to know about and interact with.

Good C# design isn't about exposing everything.

It's about **exposing what is necessary and hiding what isn't.**