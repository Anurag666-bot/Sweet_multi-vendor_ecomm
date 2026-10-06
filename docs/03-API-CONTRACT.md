# 03 — API Contract

## Base
`/api/v1/`

JSON by default. Multipart only where required.

## Conventions
UUID internal IDs, human-readable order numbers, ISO-8601 timestamps, integer minor money, explicit currency, pagination, stable errors.

## Success
```json
{"data":{"id":"uuid","status":"ACTIVE"},"meta":{}}
```

## Error
```json
{"error":{"code":"ORDER_NOT_FOUND","message":"Order was not found.","details":{}}}
```

Never expose SQL, stack traces, secrets or internal paths.

## HTTP statuses
200 success, 201 created, 202 accepted, 204 no content, 400 invalid request, 401 unauthenticated, 403 forbidden, 404 unavailable/not found, 409 conflict/idempotency/state conflict, 422 semantic validation where adopted, 429 rate limit, 500 unexpected error.

## Core endpoints
Auth:
POST /auth/login
POST /auth/refresh
POST /auth/logout
GET /auth/me

Catalog:
GET /products
GET /products/{id}
GET /categories
GET /stores/{store_id}/products

Seller:
GET /seller/me
GET /seller/stores
POST /seller/stores
GET /seller/products
POST /seller/products
PATCH /seller/products/{id}

Cart:
GET /cart
POST /cart/items
PATCH /cart/items/{id}
DELETE /cart/items/{id}

Checkout:
POST /checkout
GET /checkout/{id}
POST /checkout/{id}/confirm

Orders:
GET /orders
GET /orders/{id}
POST /seller/orders/{id}/accept
POST /seller/orders/{id}/status

Payments:
POST /payments/{id}/initialize
POST /payments/{id}/retry
POST /webhooks/{provider}

Delivery:
GET /deliveries
POST /deliveries/{id}/assign
POST /deliveries/{id}/status

Payouts:
GET /seller/payouts
GET /seller/ledger
POST /seller/payouts/request

## Idempotency
Use `Idempotency-Key` on retryable mutations. Store key, actor/scope, endpoint, request hash, result/status and expiry. Same key with different request body is rejected.

## Pagination
Use one consistent pagination strategy across the API.

## Versioning
Breaking API changes require explicit versioning/migration strategy.

## OpenAPI
All production endpoints must be documented and reviewed before frontend integration.
