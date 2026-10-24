# Cross-Site Request Forgery (CSRF): When Your Browser Becomes the Attacker

Imagine logging into your banking application, then opening another website in a different tab.

Without your knowledge, that website triggers a request to your banking application to perform an action you never authorized.

You didn't initiate the action, but your browser may still send your authentication cookies with the request.

That's the idea behind **Cross-Site Request Forgery (CSRF)**.

## What Is CSRF?

Cross-Site Request Forgery is a web security vulnerability that tricks a user's browser into performing unwanted actions on a website where the user is already authenticated.

The attacker doesn't necessarily need to steal your password or session cookie.

Instead, they exploit the trust between your browser and the application.

**The problem isn't that the application doesn't recognize the user. It's that the application cannot determine whether the user intended the request.**

## How Does CSRF Work?

Consider an application where users can change their email addresses.

1. A user logs into `bank.example` and receives an authentication cookie.
2. The user visits an attacker-controlled website.
3. The malicious website attempts to submit a request to `bank.example` to change the user's email address.
4. If the browser includes the session cookie and the application lacks CSRF protection, the server may process the request.

The server sees a valid authenticated session, but the action originated from an untrusted website.

Modern cookie restrictions such as `SameSite=Lax` can block many cross-site requests, but vulnerable configurations and application behaviors can still expose CSRF risks.

## How Can Developers Prevent CSRF?

**1. Use anti-CSRF tokens**

Generate an unpredictable token and require it on state-changing requests. The server validates the token before processing the action.

An attacker-controlled website shouldn't be able to obtain a valid token from the protected application.

**2. Configure SameSite cookies**

Use `SameSite=Lax` or `SameSite=Strict` where appropriate to restrict when browsers include cookies in cross-site requests.

Treat this as an additional defense rather than assuming it covers every scenario.

**3. Validate request origins**

Use the `Origin` header, with carefully configured fallback checks, or Fetch Metadata headers to reject unexpected cross-site requests.

**4. Protect state-changing endpoints**

Operations such as changing passwords, updating account details, or transferring funds should require appropriate CSRF protection.

Never use GET requests for operations that modify application state.

## CSRF Protection in ASP.NET Core

ASP.NET Core provides built-in antiforgery support.

For MVC applications, developers can use `[ValidateAntiForgeryToken]` on protected actions or configure `[AutoValidateAntiforgeryToken]` to validate unsafe HTTP methods automatically.

Razor Pages also provides built-in antiforgery support for forms.

For APIs using cookie-based authentication, ensure the relevant endpoints have an appropriate CSRF defense.

APIs authenticated exclusively with bearer tokens explicitly supplied through authorization headers are generally not susceptible to traditional cookie-based CSRF.

## Key Takeaway

Authentication verifies **who is making a request**.

CSRF protection helps verify that the request came through an intended, trusted interaction.

A valid session doesn't automatically mean a request is legitimate.

**Secure applications must validate not just the user's identity, but also the trustworthiness of state-changing requests.**