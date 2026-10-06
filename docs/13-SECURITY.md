# 13 — Security

## Principles
Deny by default, validate input, enforce authorization server-side, never trust client financial values, protect secrets, minimize sensitive data, audit security actions and fail safely.

## Authentication
Short-lived access, secure refresh, rotation/revocation, password hashing, rate limits.

## Authorization
Identity + permission + object scope + business state.

## Seller isolation
Never accept seller ownership from request body. Resolve from authenticated scope.

## Input
Validate types, length, format, ranges, enums, files and ownership.

## Uploads
Allowlist extensions, validate MIME/content, size/dimension limits, generated storage keys and traversal protection.

## Rate limits
Authentication, password reset, payments/refunds and sensitive administration.

## Secrets
Environment/secret manager. Never commit DB passwords, JWT secrets, payment keys or API keys.

## Production
DEBUG=False, HTTPS, Secure cookies, security headers, controlled CORS/CSRF, restricted DB access and least privilege.

## Web security
Use Django ORM/parameterized queries, safe output encoding, CSRF protection and explicit trusted origins.

## Payments
Verify webhooks, amount and references. Make state changes idempotent.

## Concurrency
Protect inventory, payments, order transitions and ledger/payout operations with transactions/locking.

## Backups
Automated, encrypted, retained and periodically restored in tests.

## Incident response
Detect -> contain -> investigate -> eradicate -> recover -> prevent recurrence.
