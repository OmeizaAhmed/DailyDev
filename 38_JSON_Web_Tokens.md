# JSON Web Tokens: What Your API Should Actually Trust

A user logs in, receives a token, and sends it with every request.

Simple enough. But decoding that token doesn’t prove it’s trustworthy.

That distinction is central to understanding JSON Web Tokens.

### What is a JWT?

A JSON Web Token, or JWT, is a compact format for carrying **claims** between systems. Claims are statements such as who the user is, who issued the token, and when it expires.

JWTs can be signed, encrypted, or both. The signed version commonly used in APIs has three parts:

`header.payload.signature`

- **Header:** Identifies the signing algorithm and token type.
- **Payload:** Contains the claims.
- **Signature:** Lets the recipient verify integrity using the appropriate key.

The header and payload are Base64url-encoded. **Encoding does not hide their contents.** A signed JWT’s payload is readable by anyone holding it.

### How does it work in an API?

Consider an order management application:

1. The user submits their login credentials.
2. The authentication service verifies them and issues a signed JWT.
3. The client sends the token with subsequent requests.
4. The API validates the token before applying its access rules.

A typical request includes:

`Authorization: Bearer <token>`

The token might identify the user as `user-42`. The API can then use that identity when checking whether they may view a particular order.

### Decoding is not validation

Decoding a JWT only reveals its contents. Anyone can construct a payload claiming to be an administrator.

Before trusting a token, the API must verify its cryptographic protection, restrict accepted algorithms, and check relevant claims—including the expected issuer, intended audience, and expiration.

Use a maintained validation library with explicit configuration. Don’t let an incoming token decide which algorithms your API trusts.

### A valid token still needs permission checks

Imagine a valid token belongs to `user-42`, but the requested order belongs to `user-99`.

The token establishes an identity. Your application still needs to check ownership or other authorization rules.

**“This token is valid” does not mean “this request is allowed.”**

### What about logout?

Deleting the token from the client stops that client from sending it. It does not invalidate a copy held elsewhere.

If an API relies only on token validation, a copied token can remain usable until expiration. Immediate revocation requires an additional mechanism, such as a server-side revocation check.

This is a practical tradeoff to consider when designing your authentication flow.

### Key takeaway

**JWTs carry claims; validation establishes whether to trust them; authorization determines what the request may do.**

Keep those responsibilities clear, and your authentication flow becomes easier to understand and secure.