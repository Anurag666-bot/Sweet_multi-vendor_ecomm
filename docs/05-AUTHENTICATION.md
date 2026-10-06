# 05 — Authentication

## Strategy
Use short-lived access tokens plus refresh tokens in secure HTTP-only cookies. Rotate/revoke refresh tokens.

Never store refresh tokens in localStorage/sessionStorage.

## Login
1. HTTPS request.
2. Validate credentials.
3. Issue access token.
4. Set secure refresh cookie.
5. Return access token for in-memory client use.
6. Audit login.

## Refresh
Validate refresh cookie -> rotate -> revoke old token according to policy -> issue new access token.

## Logout
Revoke/invalidate refresh capability and clear the cookie.

## Cookies
Production should use HttpOnly, Secure, correct SameSite, controlled domain/path and environment-specific settings.

## CSRF
Cookie-authenticated unsafe requests require explicit CSRF protection. CORS and trusted origins must be allowlisted.

## Passwords
Use Django password hashing. Never log passwords or tokens.

## Protection
Rate-limit login, password reset, verification and refresh abuse.

## Frontend refresh concurrency
When multiple requests receive 401 simultaneously, use one refresh operation and queue/retry eligible requests instead of issuing many concurrent refresh calls.

## Authentication vs authorization
Authentication proves identity. RBAC and object scope decide what the identity can do.
