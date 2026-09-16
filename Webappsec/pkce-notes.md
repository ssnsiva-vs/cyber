# PKCE in Token Acquisition for Web Applications

**PKCE** (Proof Key for Code Exchange, pronounced "pixy") is an extension to the OAuth 2.0 Authorization Code flow, defined in RFC 7636. It was originally designed to protect mobile/native apps but is now recommended for **all** clients, including web apps — especially those that can't securely store a client secret (SPAs) but increasingly for confidential clients too, as defense-in-depth.

## The Problem It Solves

In the standard Authorization Code flow, a malicious app or network observer could intercept the authorization code (e.g., via a shared redirect URI scheme, browser history, or a man-in-the-middle) and exchange it for tokens itself. This is called an **authorization code interception attack**. Public clients (no client secret) are especially vulnerable since there's nothing to prove the token request came from the legitimate app.

## How It Works

1. **Client generates a `code_verifier`**
   - A high-entropy random string (43–128 characters, URL-safe).
2. **Client derives a `code_challenge`**
   - `code_challenge = BASE64URL-ENCODE(SHA256(code_verifier))` (method `S256`, preferred)
   - Or plain: `code_challenge = code_verifier` (method `plain`, discouraged — offers no real protection)
3. **Authorization Request**
   - Client sends `code_challenge` + `code_challenge_method` to the `/authorize` endpoint.
   - Authorization server stores the challenge alongside the issued auth code.
4. **User authenticates, auth code returned**
   - Same as standard flow — code sent to redirect URI.
5. **Token Request**
   - Client sends the original `code_verifier` (not the challenge) to the `/token` endpoint along with the auth code.
6. **Server verifies**
   - Server hashes the received `code_verifier` and compares it to the stored `code_challenge`.
   - Match → tokens issued. Mismatch → request rejected.

## Why This Prevents Interception

Even if an attacker steals the authorization code, they don't have the `code_verifier` (it never leaves the original client until the token request, and it's never sent in the front-channel/browser redirect). Without it, they can't complete the token exchange.

## Application to Web Apps

| App Type | PKCE Relevance |
|---|---|
| **SPA (Single Page App)** | Mandatory in modern best practice. No backend to hold a client secret, so PKCE is the primary protection. Public client. |
| **Traditional server-rendered web app** | Has a confidential client secret already, but PKCE is still recommended (OAuth 2.1 mandates it for all clients) as an additional layer against code interception, especially over insecure networks or misconfigured redirect URIs. |
| **BFF (Backend-for-Frontend) pattern** | Backend performs the code exchange; PKCE still recommended between backend and auth server. |

## Key Implementation Notes

- **Always use S256**, never `plain`, unless the client genuinely cannot compute SHA-256 (rare in browsers today — `crypto.subtle.digest` is standard).
- `code_verifier` should be generated fresh per authorization request and kept only in memory or session storage — never in a way that persists across the redirect insecurely (e.g., avoid `localStorage` for SPAs where possible; many libraries use `sessionStorage`).
- PKCE **replaces the need for a client secret in public clients** — but doesn't replace the `state` parameter (CSRF protection) or proper `redirect_uri` validation. Use all three together.
- **OAuth 2.1** (the consolidated draft spec) makes PKCE **mandatory** for all authorization code flows, regardless of client type.
- Common libraries (e.g., `oauth4webapi`, MSAL, Auth0 SDK, `oidc-client-ts`) handle PKCE generation/verification automatically — rarely need to implement the crypto by hand.

## Typical Flow Diagram (conceptual)

```
Client                          Auth Server
  |--(1) verifier + challenge------|
  |--(2) /authorize?challenge=...->|
  |<--(3) redirect w/ auth code----|
  |--(4) /token + code + verifier->|
  |<--(5) access/id/refresh tokens-|
```
