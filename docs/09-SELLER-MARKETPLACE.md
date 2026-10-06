# 09 — Seller Marketplace

## Model
Seller = legal/business account.
Store = customer-facing fulfillment location.
One seller may have multiple stores.

## Onboarding
Application -> verification -> approval -> owner/member roles -> store -> address/service area -> products -> store listings -> store-specific prices -> inventory -> go live.

## Product ownership
Products belong to sellers, not stores.

## Store listing
store_products controls store availability.
store_product_variants controls store variant availability and price.

## Isolation
Seller A cannot access Seller B products, stores, inventory, orders, earnings or payouts.

## Staff
Seller owner assigns minimum necessary scoped permissions. Store staff is restricted to assigned stores.

## Multi-seller order
Customer sees parent order. Each seller sees only its SellerOrder and permitted information.

## Commission
Calculate from active commission rules using basis points.

## Settlement
Order -> Commission -> Seller Ledger -> Payout.

Ledger entries are immutable.

## Suspension
Block new selling activity while preserving historical orders/financial records. Existing order handling follows explicit operational policy.
