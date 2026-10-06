# 01 — Database / ERD

## Database rules
Production database: PostgreSQL.
All money is integer minor units. NPR uses paisa; 100 paisa = 1 NPR. Never use floating point for money.

## Identity
### users
id, email, phone, password_hash, first_name, last_name, status, is_verified, last_login_at, created_at, updated_at.

### addresses
id, user_id, label, recipient_name, phone, address_line, area, city, latitude, longitude, delivery_instructions, is_default, timestamps.

Customer addresses are separate from store addresses.

## RBAC
### roles
id, code, name, scope_type(PLATFORM/SELLER/STORE), description, is_system, timestamps.

### permissions
id, code, name, description, resource, action.

### role_permissions
role_id, permission_id, unique(role_id, permission_id).

### user_roles
id, user_id, role_id, seller_id nullable, store_id nullable, status, timestamps.

Rules:
- platform role: seller_id NULL, store_id NULL
- seller role: seller_id required, store_id NULL
- store role: seller_id and store_id required

Do not duplicate authorization with seller_members.role.

## Seller
### sellers
id, legal_name, display_name, slug, status, verification_status, payout_status, timestamps.

### seller_members
id, seller_id, user_id, status, invited_at, joined_at, removed_at, timestamps. Membership only; authorization comes from RBAC.

### stores
id, seller_id, name, slug, status, phone, email, preparation_time_minutes, timestamps.

### store_addresses
id, store_id, address fields, latitude, longitude, service metadata, timestamps.

## Catalog
### categories
id, parent_id nullable, name, slug, description, status, sort_order, timestamps.

### products
id, seller_id, category_id, name, slug, description, short_description, status, preparation information, timestamps.

Products belong to sellers, not stores. Do not add products.store_id.

### product_variants
id, product_id, name, sku, description, unit_type, unit_value, status, timestamps.

### product_options
id, product_id, name, selection_type, required, min_selection, max_selection, timestamps.

### product_option_values
id, option_id, name, value, price_delta_minor, timestamps.

### product_media
id, product_id, media_type, storage_key, alt_text, sort_order, status, timestamps.

## Store catalog
### store_products
id, store_id, product_id, status, preparation_override_minutes nullable, availability_status, timestamps. Unique(store_id, product_id).

### store_product_variants
id, store_product_id, variant_id, price_minor, currency, sku_override nullable, status, timestamps. Unique(store_product_id, variant_id).

Store-specific variant pricing belongs here.

## Inventory
### inventory_items
id, store_product_variant_id, on_hand, reserved, unit_type, reorder_level, status, timestamps.

available_to_sell = on_hand - reserved.

### stock_ledger
Immutable movements: id, inventory_item_id, movement_type, quantity, reference_type, reference_id, before/after values, actor, created_at.

### inventory_reservations
id, inventory_item_id, checkout/order reference, quantity, status, expires_at, released_at, consumed_at, timestamps.

Reservation lifecycle:
AVAILABLE -> RESERVED -> CONSUMED
RESERVED -> RELEASED
RESERVED -> EXPIRED -> RELEASED

Use PostgreSQL transactions and row locking for concurrent reservations.

## Cart / Checkout
### carts
id, user_id, status, currency, timestamps.

### cart_items
id, cart_id, store_product_variant_id, quantity, selected_options_snapshot, timestamps.

### checkout_sessions
id, user_id, cart_id, status, address_snapshot, pricing_snapshot, expires_at, idempotency_key, timestamps.

### checkout_items
Immutable checkout calculation inputs/snapshots.

## Orders
### orders
id, user_id, order_number, status, payment_status, currency, subtotal_minor, delivery_fee_minor, discount_minor, tax_minor, total_minor, address_snapshot, pricing_snapshot, timestamps.

### seller_orders
id, order_id, seller_id, store_id, status, subtotal_minor, seller_delivery_fee_minor, discount_minor, commission_minor, seller_net_minor, timestamps.

### order_items
id, seller_order_id, product_id nullable, variant_id nullable, product_name_snapshot, variant_name_snapshot, sku_snapshot, unit_price_minor, quantity, option_snapshot, line_total_minor, timestamps.

Order snapshots are immutable historical values.

## Payments
### payments
id, order_id, provider, status, amount_minor, currency, provider_payment_id, timestamps.

Provider reference unique by (provider, provider_payment_id).

### payment_attempts
id, payment_id, provider, provider request/reference, status, amount_minor, failure_code, timestamps.

### webhook_events
id, provider, event_id, event_type, signature_valid, payload_hash, processing_status, processed_at, timestamps.

Webhook handling must be idempotent.

## Refunds
refunds: id, payment_id, order_id, amount_minor, reason, status, provider_ref, timestamps.
refund_items: id, refund_id, order_item_id, quantity, amount_minor, timestamps.

## Delivery
delivery_zones; delivery_fee_rules; delivery_jobs; delivery_assignments; tracking_events; proof_of_delivery.

Delivery jobs reference the appropriate seller fulfillment/order.

## Settlement
### commission_rules
id, seller_id nullable, category_id nullable, rate_bps, effective_from, effective_to, status.
10000 bps = 100%; 1250 bps = 12.5%.

### seller_ledger_entries
id, seller_id, seller_order_id nullable, payout_id nullable, entry_type, amount_minor, balance_after_minor, reference, created_at.

### payouts
id, seller_id, amount_minor, status, payout_reference, period_start, period_end, timestamps.

### payout_items
id, payout_id, seller_ledger_entry_id, amount_minor.

Financial chain:
Order -> Payment -> SellerOrder -> Commission -> SellerLedger -> Payout.

## Supporting domains
promotions, coupons, coupon_redemptions, reviews, review_media, review_reports, support_tickets, support_messages, notification_templates, notifications, notification_deliveries, audit_logs, outbox_events.

## Constraints
Use PK/FK, unique constraints, check constraints and indexes. Index seller/store foreign keys and common scoped queries.

## Deletion
Do not casually delete financial/order history. Prefer inactive/soft-delete policies and immutable snapshots.

## Critical transactions
Checkout confirmation, inventory reservation, payment confirmation, order transitions, refunds, ledger posting and payouts must be transactionally protected.

## Migration order
identity -> RBAC -> seller/store -> catalog -> store catalog -> inventory -> cart/checkout -> orders -> payments/refunds -> delivery -> settlement -> supporting modules.

No destructive production migration without explicit approval and recovery plan.
