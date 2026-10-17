# Cross-Site Scripting (XSS): When User Input Becomes Executable Code

Imagine building a web application where users can leave comments.

Everything works perfectly until someone submits a comment containing JavaScript instead of ordinary text.

Other users open the page, and suddenly, their browsers execute code they never intended to run.

That's **Cross-Site Scripting (XSS)**, one of the most common web application security vulnerabilities.

## What Is Cross-Site Scripting?

Cross-Site Scripting (XSS) is a security vulnerability that allows attackers to inject malicious scripts into web pages viewed by other users.

It happens when an application treats untrusted data as executable code instead of displaying it safely as text.

An attacker may exploit XSS to:

- Steal sensitive information accessible to JavaScript.
- Perform unauthorized actions using a victim's authenticated session.
- Modify web page content.
- Redirect users to malicious websites.
- Capture information entered into compromised forms.

The important distinction is that **the malicious script executes in the user's browser, within the vulnerable website's security context.**

## How Does XSS Work?

Consider a comment feature that displays user-submitted content.

A legitimate user submits:

```text
This article is really helpful!
```

But an attacker submits:

```html
<script>alert('XSS')</script>
```

If the application inserts this input directly into the page as HTML, the browser may execute the script.

The alert itself is harmless, but it demonstrates how an attacker can execute JavaScript through untrusted input.

The problem isn't JavaScript.

**The problem is allowing user-controlled data to become executable code.**

## The Three Main Types of XSS

### 1. Stored XSS

The malicious script is permanently stored somewhere in the application, such as a database.

For example, an attacker submits a malicious comment. The application saves it and displays it to everyone viewing the post.

Every affected visitor may execute the script.

Stored XSS is particularly dangerous because one malicious input can affect multiple users.

### 2. Reflected XSS

The malicious script comes from an HTTP request and is immediately reflected in the application's response.

For example, a search page takes a query parameter and displays it without proper encoding.

An attacker could create a malicious URL and convince someone to open it.

If the application embeds the query unsafely, the script may execute in the victim's browser.

### 3. DOM-Based XSS

This vulnerability occurs when client-side JavaScript processes untrusted data and writes it into an unsafe DOM location.

Consider:

```javascript
const search = new URLSearchParams(
    window.location.search
).get("q");

document.getElementById("result").innerHTML = search;
```

The problem is `innerHTML`.

It interprets the assigned value as HTML rather than plain text.

A safer alternative is:

```javascript
document.getElementById("result").textContent = search;
```

Now the browser displays the value as text instead of parsing it as HTML.

## How Can Developers Prevent XSS?

### 1. Use Context-Aware Output Encoding

Encode untrusted data appropriately for the context in which it appears: HTML text, attributes, JavaScript, CSS, or URLs.

Modern frameworks such as React and ASP.NET Core Razor automatically encode many values when used through their normal text-rendering mechanisms.

Avoid bypassing those protections without a clear reason.

### 2. Avoid Unsafe DOM APIs

Prefer `textContent` over `innerHTML` when displaying plain text.

When an application genuinely needs to render user-provided HTML, use a well-maintained HTML sanitizer.

### 3. Implement Content Security Policy (CSP)

A properly configured CSP can limit which scripts the browser is allowed to execute.

It adds an important layer of defense, but it should not replace secure output handling.

### 4. Protect Session Cookies

Use `HttpOnly`, `Secure`, and appropriate `SameSite` settings for authentication cookies.

These settings can reduce certain consequences of XSS, although they don't eliminate the vulnerability or prevent every unauthorized action.

## A Practical ASP.NET Core Example

Suppose an application displays user comments using Razor.

**Safer approach:**

```cshtml
<p>@Model.Comment</p>
```

Razor HTML-encodes the value by default.

**Potentially dangerous approach:**

```cshtml
<p>@Html.Raw(Model.Comment)</p>
```

`Html.Raw()` bypasses HTML encoding.

If `Model.Comment` contains attacker-controlled HTML or JavaScript, this can introduce an XSS vulnerability.

Only render raw HTML when its content has been made safe for that purpose.

## Final Takeaway

Cross-Site Scripting isn't simply about attackers writing malicious JavaScript.

It's about applications failing to maintain the boundary between **untrusted data and executable code**.

Validate input for business rules, encode output for its destination, sanitize HTML when necessary, and avoid unsafe DOM operations.

**The safest application is one that never gives untrusted input the opportunity to become executable code.**