# 04 — System Architecture

## High-level
Customer/Seller/Admin Browser
-> Next.js Web
-> Django REST API
-> PostgreSQL
-> Redis
-> Celery
-> Object Storage
-> Payment/Notification/Delivery providers

## Style
Modular monolith first. Small team, simpler deployment, shared transactions and easier debugging. Modules must have clear boundaries so extraction remains possible later.

## Backend modules
accounts, sellers, stores, catalog, inventory, cart, checkout, orders, payments, delivery, payouts, reviews, notifications, audit.

## Dependency direction
accounts -> sellers/stores -> catalog -> inventory -> cart/checkout -> orders -> payments/delivery/payouts.

Avoid circular domain dependencies.

## Request path
Browser -> API client -> authentication -> throttling -> validation -> permission -> object scope -> service/business logic -> transaction -> database -> response.

## Service layer
Use focused services for complex operations:
CheckoutService, InventoryReservationService, OrderService, PaymentService, RefundService, PayoutService.

## Async
Celery for email, notifications, webhook follow-up, expiry, reconciliation and non-critical post-transaction work. Core financial consistency remains transactional.

## Outbox
Write the domain change and outbox event in one DB transaction. A worker publishes/processes the event and marks it processed.

## Cache
Redis for rate limits, catalog caching and short-lived coordination. Never make cache the financial source of truth.

## Storage
Validated product media, delivery proof and other uploads use controlled object storage.

## Observability
Structured logs, audit logs, error monitoring, latency metrics, queue health, DB health, payment/webhook monitoring and inventory anomaly monitoring.

## Scaling
Start as modular monolith. Scale API workers, Celery, Redis, PostgreSQL and storage independently. Do not introduce microservices without an actual operational reason.
