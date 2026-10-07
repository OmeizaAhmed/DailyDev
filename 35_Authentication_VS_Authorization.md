# Authentication vs Authorization: Who Are You, and What Are You Allowed to Do?

**Authentication answers “Who are you?” Authorization answers “What are you allowed to do?”**

These two concepts are often mentioned together, but they solve completely different problems.

Understanding the difference is essential when building APIs, web applications, and secure systems.

## Authentication: Who Are You?

Authentication is the process of **verifying a user's identity**.

When you log into an application with an email and password, the system checks whether those credentials belong to you.

For example:

```text
Email: omeiza@example.com
Password: ********
```

If the credentials are valid, the application knows:

> “This user is Omeiza.”

Other common authentication methods include:

- Passwords
- JWT tokens
- Session cookies
- OAuth
- Multi-factor authentication
- Biometric authentication

Authentication happens **before** the system can make meaningful authorization decisions.

## Authorization: What Can You Do?

Once the system knows who you are, it needs to determine what you're allowed to access.

That's authorization.

Imagine you're logged into an admin dashboard.

The system knows you're Omeiza, but that doesn't automatically mean you're allowed to:

- Delete users
- Access financial reports
- Change system settings
- Promote another user to admin

Authorization determines whether you have permission to perform those actions.

For example:

```text
User: Omeiza
Role: Admin

Action: Delete User
Result: Allowed
```

But:

```text
User: John
Role: Customer

Action: Delete User
Result: Forbidden
```

## The Difference in One Example

Think about entering an office building.

**Authentication:**

The security guard checks your ID and confirms:

> “Yes, this is Omeiza.”

**Authorization:**

The guard then checks your access level:

> “Omeiza can enter the office, but not the server room.”

Authentication establishes **identity**.

Authorization determines **permissions**.

## How They Work Together

A typical API request might look like this:

```text
Client
  ↓
Login
  ↓
Authentication
  ↓
JWT Token
  ↓
API Request
  ↓
Authorization
  ↓
Access Granted / Denied
```

For example, a user logs in and receives a JWT:

```http
Authorization: Bearer <token>
```

The API validates the token to authenticate the user.

Then it checks the user's role or permissions:

```text
Is the user authenticated?
        ↓
       Yes
        ↓
Does the user have permission?
        ↓
       Yes
        ↓
   Execute request
```

If the user isn't authenticated, the API typically returns:

```http
401 Unauthorized
```

If the user is authenticated but doesn't have permission, the API typically returns:

```http
403 Forbidden
```

This distinction is important.

**401:** “I don't know who you are.”

**403:** “I know who you are, but you're not allowed to do this.”

## A Common Mistake

A common security mistake is assuming that authentication is enough.

For example:

```csharp
[Authorize]
public IActionResult DeleteUser(int id)
{
    // Delete user
}
```

This may ensure that the user is logged in, but it doesn't necessarily mean every authenticated user should be able to delete users.

You might instead require a specific role or permission:

```csharp
[Authorize(Roles = "Admin")]
public IActionResult DeleteUser(int id)
{
    // Delete user
}
```

Now authentication answers:

> “Is this user logged in?”

Authorization answers:

> “Is this user an admin?”

## The Key Takeaway

**Authentication identifies the user. Authorization controls what that user can do.**

A secure application needs both.

Think of it this way:

> **Authentication = Who are you?**  
> **Authorization = What are you allowed to do?**

Once you understand that distinction, many security concepts in APIs and web applications become much easier to reason about.