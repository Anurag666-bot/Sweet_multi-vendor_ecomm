# 14 — Testing

## Levels
Unit, service/business, API, integration, frontend component, end-to-end, security and migration tests.

## Mandatory backend cases
- seller A cannot access seller B;
- store A cannot access store B;
- inventory cannot oversell;
- duplicate checkout does not duplicate order;
- duplicate webhook does not duplicate payment;
- payment mismatch is rejected;
- refund cannot exceed refundable amount;
- invalid order transition is rejected;
- payout cannot exceed payable balance.

## Authentication
Test login, invalid password, refresh, rotation, logout, expiry, concurrent refresh, CORS/CSRF and rate limits.

## Frontend
Test forms, validation, loading/error states, authentication, catalog, cart, checkout, seller screens and responsive UI.

## E2E golden path
browse -> select store -> add product -> cart -> checkout -> payment -> order -> seller accepts -> prepares -> delivery -> delivered.

## Negative cases
Out of stock, payment failure/timeout, duplicate callback, seller suspension, store closure, invalid address and unauthorized object access.

## Migration
Test clean database, representative database, constraints/indexes and recovery/rollback strategy.

## CI
PRs should run backend tests, lint/format, frontend lint/typecheck/build, migration checks and configured security/static checks.

## Definition of Done
Implementation + tests + review + documentation + no known critical regression.
