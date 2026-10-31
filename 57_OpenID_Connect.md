# OpenID Connect: What “Sign in with Google” Actually Adds to OAuth

A user clicks **Sign in with Google**, returns to your app, and is logged in. OAuth is involved—but OAuth alone doesn’t define how your app confirms who the user is. That’s the role of OpenID Connect (OIDC).

## OAuth grants access; OIDC adds identity

OAuth 2.0 lets an application obtain permission to access a resource, such as a user’s calendar or profile API. OIDC builds an identity layer on top of OAuth 2.0, so a client can verify that a user has authenticated and receive information about them.

The key addition is the **ID token**. It carries claims about the authentication, including who issued it and the user’s subject identifier. An **access token** is for calling an API; it is not a substitute for the ID token when your app needs to establish a login.

## What happens during a typical sign-in

Imagine a web app that lets users sign in with an identity provider:

1. The app redirects the user to the provider’s authorization endpoint.
2. The provider authenticates the user and returns an authorization code.
3. The app exchanges the code at the token endpoint.
4. The app validates the returned ID token, then uses its claims to identify the signed-in user.

OIDC discovery metadata can tell the app where the provider’s endpoints and signing keys are, so the app doesn’t need to hard-code each endpoint.

## Use the right claim as the user key

A common mistake is to treat an email address as the user’s permanent identity. Email addresses can change. OIDC provides a `sub` (subject) claim intended to identify the user at that issuer. In practice, use the combination of the token’s issuer (`iss`) and subject (`sub`) to associate an external identity with a local account.

Also, don’t just decode an ID token and trust its contents. Validate it using a maintained OIDC library. Validation includes checks such as the issuer and audience, as well as the token’s signature and time-related claims.

## Key takeaway

OIDC is the identity layer that lets an application verify a login using OAuth 2.0 flows. Use the ID token to understand the authentication, and the access token to call the APIs it authorizes.