# Secrets Management: Why Your API Keys and Passwords Should Never Live in Your Code

Imagine pushing your application to GitHub, only to realize you've accidentally committed your production database password or payment gateway API key.

Even if you delete it in the next commit, the damage may already be done.

This is why **Secrets Management** is an essential part of building secure applications.

## What Is Secrets Management?

Secrets Management is the process of securely storing, accessing, rotating, and revoking sensitive credentials used by applications.

These secrets include:

- **API keys:** Credentials used to authenticate requests to external services.
- **Database credentials:** Usernames and passwords used to access databases.
- **Encryption keys:** Keys used to encrypt and decrypt sensitive information.
- **Signing keys:** Keys used to generate and verify cryptographic signatures.
- **Access tokens:** Credentials that authorize access to protected resources.

The goal is simple: **Only authorized applications and services should have access to the secrets they need, and only when they need them.**

## The Problem With Hardcoding Secrets

Consider this C# example:

```csharp
var apiKey = "sk_live_123456789";
var connectionString =
    "Host=localhost;Password=SuperSecret123";
```

The application might work perfectly, but the security implications are significant.

If this code gets pushed to a public repository, anyone with access to it could potentially obtain those credentials.

Even private repositories aren't a guarantee of safety. Secrets can leak through logs, build artifacts, compromised developer accounts, or accidentally shared configuration files.

And deleting the credentials from your latest commit doesn't remove them from Git history.

**The better approach is to separate secrets from application code.**

## How Should Applications Manage Secrets?

### 1. Use User Secrets During Development

In ASP.NET Core, Microsoft provides the Secret Manager tool for keeping development credentials outside your project directory.

Initialize it:

```bash
dotnet user-secrets init
```

Store a development API key:

```bash
dotnet user-secrets set \
  "Payments:ApiKey" "your-dev-api-key"
```

Then retrieve it through the configuration system:

```csharp
var apiKey = builder.Configuration[
    "Payments:ApiKey"
] ?? throw new InvalidOperationException(
    "Payments API key is missing");
```

The credential is no longer hardcoded in your application.

However, there's an important distinction: **.NET User Secrets are not encrypted.** They're designed for local development, not production secret storage.

### 2. Use a Secrets Manager in Production

Production environments require stronger protection.

Popular solutions include:

- Azure Key Vault
- AWS Secrets Manager
- HashiCorp Vault
- Google Cloud Secret Manager

Instead of embedding credentials in your application, these services provide controlled access to sensitive information.

For example, an ASP.NET Core application can use Azure Key Vault:

```csharp
builder.Configuration.AddAzureKeyVault(
    new Uri(vaultUri),
    new DefaultAzureCredential());
```

Here, `vaultUri` is a non-secret configuration value, and the required Azure configuration and identity packages must be installed.

With the appropriate permissions and a managed identity in Azure, the application can authenticate to Key Vault without maintaining another long-lived client secret.

### 3. Apply the Principle of Least Privilege

Not every application should access every secret.

If your payment service only needs a payment gateway API key, it shouldn't also have permission to retrieve your database administrator credentials.

Restrict access based on what each application or service actually needs.

This reduces the potential damage if an application is compromised.

### 4. Rotate and Revoke Secrets

Secrets shouldn't be treated as permanent credentials.

Suppose your webhook signing key has been exposed.

Simply encrypting the existing key won't solve the problem. An attacker who already possesses it may still be able to generate valid signatures.

You need to replace the key and revoke the compromised one.

For planned rotations, a system may temporarily support both old and new keys to avoid disrupting existing integrations. A confirmed compromise, however, may require immediate revocation.

**Good secret rotation should minimize exposure without unnecessarily breaking dependent services.**

## Secrets Management vs Encryption vs Hashing

These concepts are related, but they solve different problems.

**Hashing:** Useful when you only need to verify a presented credential without retrieving its original value. For example, a securely generated, high-entropy API token can be stored using an appropriate digest.

**Encryption:** Useful when the original secret must be retrieved, such as a webhook signing key needed to generate an HMAC signature.

**Secrets Management:** Covers the wider lifecycle, including secure storage, access control, auditing, rotation, and revocation.

Encrypting a secret is valuable, but if the decryption key is hardcoded beside it, you've simply moved the security problem somewhere else.

## Common Secrets Management Mistakes

Even experienced developers sometimes overlook these issues:

- Committing `.env` files or sensitive `appsettings.json` values to Git.
- Assuming environment variables are automatically encrypted.
- Logging API keys, authorization headers, or connection strings.
- Sharing production credentials across development and staging environments.
- Using one credential across multiple unrelated services.
- Failing to rotate or revoke exposed credentials.

Secret scanning in your development and CI/CD workflows can also help catch accidental leaks before they become security incidents.

## Key Takeaway

Secrets Management isn't just about hiding passwords or API keys.

It's about controlling **who can access sensitive credentials, how they're protected, and what happens when they're compromised.**

A secure application should never depend on hardcoded production secrets.

Keep development credentials outside your codebase, use dedicated secret stores in production, prefer managed identities where possible, enforce least-privilege access, and build rotation into your security strategy.

**Because the best way to handle a leaked secret is to design your system so that one compromised credential doesn't compromise everything.**