# Supply Chain Attacks: When Trusted Dependencies Become a Security Risk

You can write secure code, validate every input, enforce authentication, and follow security best practices.

But what happens when the library your application depends on is compromised?

That's the danger of a **software supply chain attack**.

Sometimes, attackers don't need to break into your application directly. They only need to compromise something your application already trusts.

## What Is a Supply Chain Attack?

A software supply chain attack happens when an attacker compromises a component, tool, or process involved in building, distributing, or deploying software.

Instead of attacking the final application directly, attackers target its dependencies or development infrastructure.

These targets can include:

- Third-party packages from NuGet, npm, or PyPI.
- Open-source libraries and their maintainers.
- CI/CD pipelines and build tools.
- Container images and deployment artifacts.
- Package registries and update mechanisms.

The dangerous part is that compromised code can enter an application through a seemingly legitimate update.

## How Do Supply Chain Attacks Work?

Imagine you're developing an ASP.NET Core application that uses a popular NuGet package for logging.

Everything works correctly, and the package has thousands of downloads.

One day, an attacker compromises the package maintainer's account and publishes a malicious update.

Your team upgrades the dependency.

The malicious version contains code designed to steal sensitive information.

Depending on how the package is constructed, that code might execute during the build process or when your application runs.

The result?

**Your application becomes compromised without the attacker ever accessing your source repository directly.**

## Common Types of Supply Chain Attacks

### 1. Compromised Dependencies

An attacker gains control of a legitimate package and publishes a malicious version.

Developers install the update because they already trust the package.

### 2. Dependency Confusion

An attacker publishes a public package that conflicts with the name of an internal dependency.

If the package manager is misconfigured, the build might download the attacker's package instead of the intended private package.

### 3. Typosquatting

Attackers publish packages with names that closely resemble popular libraries.

A simple spelling mistake can lead developers to install malicious code.

### 4. CI/CD Pipeline Compromise

Attackers target build pipelines, automation tools, or credentials.

A compromised pipeline can produce malicious artifacts even when the application's source code remains unchanged.

## How Can Developers Prevent Supply Chain Attacks?

Completely eliminating the risk isn't realistic, but several practices significantly reduce exposure.

**1. Audit your dependencies.**

For .NET 10 projects, you can check for known vulnerabilities using:

`dotnet package list --vulnerable --include-transitive`

For .NET 9 or earlier, use the `dotnet list package` command form.

**2. Lock dependency versions.**

Use dependency lock files such as `packages.lock.json` and locked-mode restores to prevent unexpected dependency changes.

**3. Review package updates.**

Don't blindly approve every dependency update. Examine the changes, package ownership, and release history.

**4. Secure your CI/CD pipeline.**

Apply least-privilege permissions, protect secrets, and pin external build actions to trusted immutable references.

**5. Verify your build artifacts.**

Use artifact signing and build provenance where appropriate to help verify where and how software was produced.

**6. Continuously monitor dependencies.**

A package considered safe today might be compromised tomorrow.

One important distinction: vulnerability scanners identify known security issues. They cannot guarantee that a package contains no newly introduced malicious code.

## Key Takeaway

Modern software development depends heavily on code we didn't write.

Every third-party library, build tool, and deployment integration introduces an additional trust relationship.

**Supply chain security isn't just about protecting your code. It's about protecting everything your code depends on.**

Before trusting a dependency, ask yourself:

*Do I know where this code came from, who controls it, and what happens if it gets compromised?*