# Encryption vs Hashing: They’re Not the Same Thing

If you’re storing passwords, protecting sensitive data, or building an authentication system, you’ve probably heard of **encryption** and **hashing**.

They both transform data into something that doesn’t look like the original.

But there’s one fundamental difference:

> **Encryption is designed to be reversed. Hashing is designed to be one-way.**

Understanding that difference can prevent serious security mistakes.

## What Is Encryption?

**Encryption converts readable data (plaintext) into unreadable data (ciphertext) using an encryption key.**

The data can later be decrypted back into its original form using the appropriate key.

For example:

```text
Plaintext:
Hello Ahmed

        ↓ Encryption + Key

Ciphertext:
8fA92xK7... 

        ↓ Decryption + Key

Plaintext:
Hello Ahmed
```

This is useful when you **need to recover the original data**.

Common examples include:

* Encrypting sensitive files
* Protecting data stored in databases
* Encrypting communication
* Protecting API secrets and credentials
* Securing financial or personal information

A common modern approach is **AES**, a symmetric encryption algorithm where the same secret key is used to encrypt and decrypt the data.

## What Is Hashing?

Hashing takes data and produces a fixed-length value called a **hash**.

```text
Input:
Hello Ahmed

        ↓ Hash function

Hash:
a8f5f167f44f4964...
```

The important part is that you don't normally "decrypt" a hash.

You hash the input again and compare the result.

For example, when a user logs in:

```text
User enters:
MyPassword123

        ↓ Hash

Hash:
abc123...

        ↓ Compare with stored hash

Stored hash:
abc123...

        ✓ Password matches
```

This is why passwords should **not be stored as plaintext**.

## Encryption vs Hashing

The easiest way to remember the difference is:

**Encryption:**

> "I need to get the original data back."

**Hashing:**

> "I only need to verify or identify the data."

For example, imagine you're building a banking application.

A user's account number or personal information may need to be **encrypted**, because the application may need to retrieve the original value.

A user's password should be **hashed**, because the application doesn't need to know the user's actual password. It only needs to verify that the password they entered is correct.

## What About Password Hashing?

There is an important detail here.

Don't use a fast general-purpose hash like SHA-256 directly for passwords.

Attackers can attempt millions or billions of guesses very quickly with specialized hardware.

Instead, use a **password hashing algorithm** designed to make brute-force attacks expensive.

Good options include:

* Argon2id
* bcrypt
* scrypt
* PBKDF2

These algorithms are intentionally computationally expensive and should be used with a unique **salt** for each password.

Conceptually:

```text
Password + Salt
       ↓
Password Hashing Algorithm
       ↓
Stored Password Hash
```

## Can You Hash Encrypted Data?

Technically, you can hash almost anything.

But hashing and encryption solve different problems.

Think of it this way:

**Encryption**

```text
Data → Encrypt → Ciphertext
Ciphertext → Decrypt → Data
```

**Hashing**

```text
Data → Hash → Hash
```

There is no normal operation like:

```text
Hash → Decrypt → Original Data
```

That's not what hashing is designed for.

## A Simple Real-World Analogy

Think of encryption like putting a document inside a **locked box**.

You can open the box if you have the key.

Hashing is more like creating a **fingerprint of the document**.

You can compare fingerprints to determine whether something matches, but the fingerprint doesn't give you the original document.

## Common Developer Mistake

One of the biggest mistakes is thinking:

> "Passwords are sensitive, so I should encrypt them."

That sounds reasonable, but it's usually the wrong approach.

If your database is compromised and your passwords are encrypted, an attacker who obtains the encryption key may be able to decrypt every password.

With properly implemented password hashing, there is no equivalent decryption key that reveals all passwords.

## When Should You Use Each?

Use **encryption** when:

* You need to retrieve the original data.
* Data must remain confidential.
* You have a secure way to manage encryption keys.

Use **hashing** when:

* You need to verify data without storing the original.
* You need password verification.
* You need to detect whether data has changed.
* You need a deterministic fingerprint of data.

## Key Takeaway

Don't think of encryption and hashing as competing security techniques.

They solve **different problems**.

> **Encryption protects data that you need to recover. Hashing protects data that you only need to verify.**

For developers, remembering that single distinction will help you make much better security decisions.
