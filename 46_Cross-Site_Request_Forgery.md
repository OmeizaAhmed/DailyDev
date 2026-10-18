# Cross-Site Request Forgery (CSRF): When a Trusted Browser Becomes an Attack Tool

Imagine logging into your banking application, then visiting a malicious website in another browser tab.

Without your knowledge, that website triggers a request to transfer money from your account.

You didn't authorize the transaction, but the banking application might process it because your browser automatically includes your authentication cookies.

That's the problem **Cross-Site Request Forgery (CSRF)** exploits.

## What Is Cross-Site Request Forgery?

Cross-Site Request Forgery (CSRF) is a web security vulnerability that tricks an authenticated user's browser into performing an unwanted action on a trusted application.

The attacker doesn't necessarily need to steal your password or session cookie.

Instead, they exploit the fact that browsers can automatically attach authentication cookies to requests.

**The core problem:** The server trusts the user's authenticated session without adequately verifying that the request was intentionally initiated through the legitimate application.

## How Does CSRF Work?

Consider an application with an endpoint for transferring money:

```http
POST /api/transfer
Cookie: sessionId=abc123

amount=50000&recipient=attacker
```

Normally, the application expects an authenticated user to initiate this request.

But an attacker could create a malicious website containing:

```html
<form action="https://bank.example/api/transfer"
      method="POST">
  <input type="hidden"
         name="amount"
         value="50000">

  <input type="hidden"
         name="recipient"
         value="attacker">
</form>

<script>
  document.forms[0].submit();
</script>
```

If the victim visits this page while authenticated, the browser may include the banking application's session cookie, depending on its cookie settings.

If the server doesn't implement sufficient CSRF protection, the request could succeed.

The attacker never needed direct access to the victim's session cookie.

They simply exploited how the browser handles authenticated requests.

## Why Is CSRF Dangerous?

CSRF can cause serious problems when applications allow authenticated users to perform sensitive operations without validating request authenticity.

Potential consequences include:

- Unauthorized financial transactions.
- Changes to account passwords or email addresses.
- Modification of security settings.
- Unauthorized changes to application data.
- Creation or deletion of resources.

The impact depends on the permissions of the authenticated user and the actions exposed by the application.

## How Can Developers Prevent CSRF?

### 1. Use Anti-CSRF Tokens

An anti-CSRF token is a securely generated value associated with a user's session or request context.

The application includes the token in legitimate requests, and the server validates it before processing sensitive operations.

An attacker operating from another website generally cannot obtain a valid token because of browser security restrictions.

### 2. Configure SameSite Cookies

The `SameSite` cookie attribute controls when browsers send cookies with cross-site requests.

```http
Set-Cookie: sessionId=abc123;
            Secure;
            HttpOnly;
            SameSite=Lax
```

Common settings include:

- **Strict:** Provides stronger restrictions on cross-site cookie transmission.
- **Lax:** Allows cookies in certain cross-site navigations while restricting many cross-site requests.
- **None:** Allows cross-site cookie transmission when paired with `Secure`.

SameSite provides an important defense layer, but it should not always be treated as a replacement for anti-CSRF tokens.

### 3. Validate Request Origins

Applications can validate the `Origin` header or, where appropriate, the `Referer` header to verify that sensitive requests originate from an approved origin.

This adds another layer of protection, especially when combined with token validation.

### 4. Use Framework Security Features

Modern frameworks provide built-in CSRF protections.

For example, ASP.NET Core supports antiforgery token validation:

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public IActionResult Transfer(
    TransferRequest request)
{
    // Process authorized transfer
    return Ok();
}
```

However, this protection requires a valid antiforgery token to be generated, submitted, and validated.

ASP.NET Core's antiforgery middleware and related framework features can help developers implement this correctly.

**Important:** Applications using bearer tokens supplied explicitly in authorization headers generally have a different CSRF risk profile from applications using automatically submitted authentication cookies. The authentication mechanism matters.

## CSRF vs XSS: What's the Difference?

Although both vulnerabilities involve web applications, they exploit different weaknesses.

**Cross-Site Scripting (XSS)** allows attackers to execute malicious scripts within the context of a trusted website.

**Cross-Site Request Forgery (CSRF)** tricks a browser into sending unauthorized requests to a trusted website using the user's existing authentication context.

XSS exploits trust in the content rendered by a website.

CSRF exploits the server's trust in authenticated requests.

Both require different security controls, and a serious XSS vulnerability can sometimes undermine CSRF defenses.

## Final Takeaway

Authentication proves which user a request is associated with.

It doesn't necessarily prove that the user intended to perform the requested action.

That's why protecting sensitive endpoints requires more than checking whether a session is valid.

**A secure application must verify both authentication and request authenticity.**