# Salting vs Peppering Passwords: What’s the Difference?

If a password database is stolen, attackers can try to guess passwords offline. Salting and peppering can make that attack harder, but they solve different problems.

## Salting: a unique value for each password

A **salt** is a random value added to a password before it is processed by a password-hashing algorithm. Each password gets its own salt, and the salt is stored alongside the resulting hash.

The salt makes identical passwords produce different stored hashes. It also prevents attackers from efficiently reusing precomputed tables of password hashes. A salt does not need to be secret.

Use a password-hashing algorithm designed to slow down guessing, such as Argon2id, with suitable parameters. A fast general-purpose hash such as SHA-256 is not a substitute.

## Peppering: a secret kept outside the database

A **pepper** is a secret value used in addition to the password and salt. Unlike a salt, it must be kept separate from the password database—for example, in a secrets manager or hardware security module.

If an attacker obtains only the database, the pepper may make it harder to verify password guesses. But if the pepper is stored beside the hashes, it offers little extra protection.

## A simplified example

The following is conceptual pseudocode, not a specific .NET API:

```text
salt = secureRandomBytes()
pepperedInput = HMAC_SHA256(pepperKey, password)
storedHash = Argon2id(pepperedInput, salt, parameters)

store(salt, storedHash, algorithm, parameters)
```

The application keeps `pepperKey` in a separate secrets store. When a user signs in, it repeats the same steps and compares the resulting hash using a safe verification method. In practice, use a well-maintained password-hashing library that handles salts, parameters, and verification correctly.

## The trade-off

A pepper adds operational responsibility. If the key is lost, the application cannot verify passwords protected with it; users may need to reset them. Rotating a pepper also requires care: after a successful login, the application can verify with the old key and rehash using the new one. Accounts that never sign in may need a password reset before the old key can be retired.

## Key takeaway

Use a unique salt and a slow password-hashing algorithm for every password. Treat peppering as an optional extra layer: keep the pepper outside the database, and plan how you will recover from key loss and handle rotation.