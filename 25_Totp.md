# TOTP Explained: How Authenticator Apps Generate Login Codes

What if your authentication code could be generated **without your server sending an SMS or email?**

That’s essentially what **TOTP (Time-Based One-Time Password)** does.

It’s the technology behind the 6-digit codes you see in apps like Google Authenticator, Microsoft Authenticator, and Authy.

## What is TOTP?

TOTP stands for **Time-Based One-Time Password**.

It generates a temporary password using two things:

* A **shared secret**
* The **current time**

The basic idea looks like this:

```text
Shared Secret + Current Time
              ↓
        TOTP Algorithm
              ↓
          6-digit code
```

For example:

```text
482913
```

After a short period, usually **30 seconds**, a new code is generated.

## How Does TOTP Work?

When a user enables two-factor authentication, your application generates a secret key.

For example:

```text
JBSWY3DPEHPK3PXP
```

The secret is shared with the user's authenticator app, usually through a QR code.

Now both sides have the same secret:

```text
Your Server                 Authenticator App
    │                              │
    │       Shared Secret          │
    └──────────────┬───────────────┘
                   │
             Current Time
                   │
                   ↓
             TOTP Algorithm
                   │
                   ↓
              6-digit code
```

Because they use the same secret and synchronized time, they independently generate the same code.

No code needs to be sent from your server to the user's phone.

## Why Does the Code Change?

The "time-based" part is important.

TOTP divides time into fixed intervals, commonly 30-second windows.

Conceptually:

```text
12:00:00 → 381924
12:00:30 → 729105
12:01:00 → 146832
```

The exact code is determined by the secret and the current time interval.

That's why an old code quickly becomes useless.

## TOTP vs SMS OTP

Both provide one-time passwords, but they work differently.

| TOTP                                  | SMS OTP                           |
| ------------------------------------- | --------------------------------- |
| Generated locally                     | Sent through SMS                  |
| Doesn't require cellular service      | Requires SMS delivery             |
| Doesn't require a message to be sent  | Depends on telecom infrastructure |
| Uses a shared secret                  | Uses a server-generated code      |
| Commonly used with authenticator apps | Common for phone verification     |

TOTP is particularly useful when you want an MFA mechanism that doesn't depend on SMS delivery.

## What Happens During Login?

Imagine a user has enabled TOTP-based MFA.

They enter:

```text
Email: user@example.com
Password: ********
```

The password is correct, so your application asks for the MFA code.

The user opens their authenticator app:

```text
Google Authenticator

MyApp
482913
```

They enter:

```text
482913
```

Your server takes the user's stored secret and the current time and calculates the expected TOTP.

If the generated value matches the submitted code, authentication succeeds.

```text
User Code
   │
   ↓
Server calculates TOTP
   │
   ↓
Codes match?
  /   \
Yes    No
 │      │
Login   Reject
```

## A Developer Detail: HOTP vs TOTP

TOTP is actually based on another standard called **HOTP**.

HOTP uses a counter:

```text
Secret + Counter → Code
```

TOTP replaces the counter with a time-based value:

```text
Secret + Time Counter → Code
```

So you can think of TOTP as **HOTP driven by time**.

## What Should Developers Be Careful About?

### 1. Never store the TOTP secret in plain text if you can avoid it

The secret is effectively the credential that allows TOTP codes to be generated.

Protect it appropriately.

### 2. Account for small clock differences

The user's phone and your server may not have perfectly synchronized clocks.

Implement a small validation window where appropriate.

For example, you might accept the current 30-second interval and an adjacent interval.

Don't make the window unnecessarily large.

### 3. Protect the setup process

Enabling MFA is sensitive.

An attacker who can attach their own authenticator to an account can potentially bypass the purpose of MFA.

Require appropriate authentication before allowing TOTP enrollment.

### 4. Provide recovery options

Users can lose their phone or authenticator app.

Your application should have a secure recovery mechanism, such as properly protected recovery codes.

## The Big Picture

TOTP is surprisingly simple at its core:

```text
Shared Secret
      +
Current Time
      ↓
TOTP
      ↓
Temporary Code
```

The important part isn't the 6-digit number itself.

It's the fact that **both the client and server can independently generate the same short-lived code without transmitting the code between them.**

## Key Takeaway

**TOTP turns a shared secret and the current time into a temporary authentication code.**

For developers building MFA, understanding TOTP gives you a foundation for implementing authenticator-based two-factor authentication without relying on SMS delivery.
