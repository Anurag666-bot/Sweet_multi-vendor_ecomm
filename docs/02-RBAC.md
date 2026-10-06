# 02 — RBAC

## Authorization formula
ALLOW = authenticated AND permission_exists AND object_is_in_scope AND business_rule_allows

## Actors
Guest, Customer, Seller Owner, Seller Staff, Rider, Support, Operations, Finance, Catalog Admin, Super Admin.

## Scope types
- PLATFORM: no seller/store restriction
- SELLER: seller_id required
- STORE: seller_id + store_id required

## Example roles
Platform: SUPER_ADMIN, OPERATIONS, SUPPORT, CATALOG_ADMIN, FINANCE, RIDER.
Seller: SELLER_OWNER, SELLER_MANAGER, SELLER_CATALOG, SELLER_INVENTORY, SELLER_ORDER_MANAGER, SELLER_FINANCE.
Store: STORE_MANAGER, STORE_STAFF.

Roles are mapped to permissions. Do not hard-code authorization around role names when a permission model is available.

## Permission naming
resource.action
Examples:
products.view, products.create, products.update, products.archive
inventory.view, inventory.adjust
orders.view, orders.accept, orders.cancel
payments.view
refunds.create
payouts.view, payouts.request
sellers.view
stores.manage
audit.view

## Request pipeline
Request -> Authentication -> Rate Limit -> Permission -> Object Scope -> Business Rule -> Action -> Audit.

## Seller isolation
A seller may only access its own products, stores, inventory, orders, earnings and payouts.

Never trust a client-provided seller_id. Resolve scope from authenticated role assignments and server-side relationships.

## Example
Seller A calls an endpoint with Seller B's order ID:
- identity valid;
- permission may exist;
- object scope fails;
- no Seller B data is disclosed.

## Frontend
The frontend may hide unavailable actions for usability. The backend must always enforce authorization.

## Role changes
Role assignment changes require:
- authorization;
- valid scope;
- transaction;
- audit record;
- authorization cache invalidation/re-evaluation if caching is used.

## Rules
Deny by default. Least privilege. Financial actions require dedicated permissions. Super-admin actions are audited.
