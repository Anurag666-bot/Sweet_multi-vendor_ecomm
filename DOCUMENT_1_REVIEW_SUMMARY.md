# Document 1 Review Summary: Multi-Vendor E-Commerce Database Design

## Executive Summary

After a thorough table-by-table review of Document 1 (the database authority for the Sweet multi-vendor e-commerce project), I've identified:

✅ **Strong Foundations**: The document correctly identifies many critical architectural needs
⚠️ **Critical Issues Requiring Resolution**: Several key decisions must be made before freezing the schema
🔑 **Clear Path Forward**: Specific, actionable changes to achieve a production-ready design

## Core Ownership Model Review & Required Changes

### 1. Users Table
**Issue**: Direct `seller_id` field prevents users from belonging to multiple sellers
**Fix**: Remove `seller_id`; handle membership through `seller_members` junction table

### 2. SellerMembers Table  
**Issue**: Contains `role` field creating dual source of truth with `user_roles`
**Fix**: Remove `role`; this table should only manage membership (user-seller belonging)

### 3. Products Table
**Issue**: Contains `store_id` making products belong to only one store (too restrictive)
**Fix**: Remove `store_id`; products belong to sellers, stores list products via new table

### 4. Required New Tables
- `store_products`: Many-to-many between stores and products (enables multi-store selling)
- `store_product_variants` (conditional): For store-specific pricing/attributes if needed

### 5. Inventory Items
**Issue**: Current `store_id` + `variant_id` creates ambiguous ownership
**Fix**: Reference `store_product_variant_id` for unambiguous inventory tracking

## Key Business Decisions Required Before Schema Freeze

### Decision 1: Multi-Store Selling Model (MOST CRITICAL)
- **Option A (Recommended)**: Sellers can have multiple stores
  - Products belong to Sellers
  - Stores list Products via `store_products`
  - Enables true marketplace flexibility
- **Option B**: Seller = exactly one Store
  - Would require major redesign later
  - **Not recommended** per document analysis

### Decision 2: Price Model
- **Option A (Uniform)**: Price at `product_variants` level (same everywhere)
- **Option B (Store-Specific)**: Price at `store_product_variants` level
  - Required if stores need different prices for same product
  - **Decision Needed**: Will individual stores set different prices?

### Decision 3: Money Representation (OPEN in Document)
- **NUMERIC/DECIMAL approach**: `DECIMAL(12,2)` for currency values
  - Pros: Clear semantics, prevents floating-point errors, widely supported
  - Cons: Slightly more storage than integers
- **Integer minor units approach**: Store cents as integers
  - Pros: More storage efficient
  - Cons: Requires constant conversion, error-prone
  - **Recommendation**: Use `DECIMAL(12,2)` unless processing extreme volumes

## Revised Ownership Chain (Post-Changes)

```
USER
 │
 ├─────────────── Platform RBAC (user_roles where seller_id=NULL, store_id=NULL)
 │
 └── SELLER_MEMBER
          │
          ▼
        SELLER
          │
          ├──────────────┐
          │              │
          ▼              ▼
       PRODUCT         STORE
          │              │
          ▼              ▼
       VARIANT      STORE_PRODUCT
                         │
                         ▼
                 STORE_PRODUCT_VARIANT
                         │
                         ▼
                     INVENTORY

Commerce Flow:
USER → CART → CHECKOUT → ORDER 
                     │
                     ├── SELLER_ORDER → ORDER_ITEM
                     │
                     ├── PAYMENT
                     │
                     └── DELIVERY_JOB
```

## Financial Models Review

### Payments Table
- Correctly belongs to Order (not seller-specific)
- Requires idempotency via unique provider payment IDs
- Should track: provider, amount, status, timestamps

### Refunds Table
- Needs idempotency via provider refund IDs
- Should reference both payment and order for convenience

### Payouts & Ledger
- **Ledger Entries**: Complete audit trail of all financial movements per seller
- **Payouts**: Disbursements to sellers, should reconcile with ledger
- Critical for marketplace economics and seller trust

## Critical Technical Considerations

### Payment Idempotency
- Database: Unique constraints on provider payment/refund IDs
- Application: Idempotency keys on API endpoints
- Process: Check existence before creating financial records

### Inventory Concurrency
- Use atomic UPDATE with WHERE clause to prevent overselling:
  ```sql
  UPDATE inventory_items 
  SET reserved = reserved + quantity 
  WHERE store_product_variant_id = ? 
    AND (on_hand - reserved) >= quantity
  ```

### Transactional Outbox
- Ensures exactly-once event publishing for distributed systems
- Table: `transactional_outbox` with processed flag and retry mechanism

## Seller Isolation Verification

Isolation must exist in server query layer, not just frontend URLs:
- **Financial Tables**: All queries must verify seller ownership through proper joins
- Example path: Payment → Order → SellerOrder → Seller → verify seller_id matches request
- **Never** rely solely on URL parameters for access control

## Migration Dependency Order

1. Foundation: roles, users
2. Membership: seller_members
3. Core: sellers, stores
4. Catalog: products, product_variants
5. Associations: store_products, store_product_variants (if needed)
6. Inventory: inventory_items
7. Commerce: carts, checkouts, orders, seller_orders, order_items
8. Financial: payments, refunds, ledger_entries, payouts
9. Events: webhooks, transactional_outbox
10. Engagement: reviews, notifications, promotions
11. Operational: delivery, audit

## Database Readiness Checklist

Before declaring Document 1 "FROZEN", verify:
- [ ] All PK/FK relationships properly defined with appropriate ON DELETE rules
- [ ] Indexes on all foreign keys and frequently queried fields
- [ ] Created_at/updated_at timestamps on all tables
- [ ] Status fields use ENUMs or reference tables (not free text)
- [ ] Monetary values use DECIMAL(12,2) or rigorously wrapped integers
- [ ] Unique constraints where needed (provider IDs, slugs, emails, SKUs)
- [ ] Check constraints for data validation (amounts ≥ 0, valid status values)
- [ ] Row Level Security (RLS) policies prepared for seller isolation
- [ ] Backup, recovery, and monitoring procedures documented

## Next Steps Per Document Guidance

**DO NOT START BUILDING YET** - Follow the prescribed sequence:

### Phase 0 — Freeze the Design (Current Focus)
1. ✅ Review every table in Document 1 (COMPLETED IN THIS ANALYSIS)
2. ⬜ Resolve ownership relationships (KEY CHANGES IDENTIFIED ABOVE)
3. ⬜ Resolve money/price model (DECISIONS NEEDED: multi-store model & price approach)
4. ⬜ Finalize inventory model (change to store_product_variant_id reference)
5. ⬜ Review order/payment/refund model (appears sound, needs idempotency focus)
6. ⬜ Review delivery model
7. ⬜ Review seller/payout model 
8. ⬜ Review audit/notification model
9. ⬜ Freeze Document 1 (No more structural DB changes unless explicitly approved)

### Phase 1 — Authorization
Build Document 2 — Complete RBAC matrix with platform/seller/store scoping

### Phase 2 — API Contract  
Build Document 3 — Complete API specification with auth, permissions, business rules

### Phase 3 — Repository Foundation
Then begin actual repository structure and initial implementation

## Conclusion

Document 1 provides an excellent foundation with clear identification of the marketplace's core architectural needs. The **critical path forward** requires resolving two key business decisions:

1. **Confirm Multi-Store Model**: Approve that sellers can have multiple stores (strongly recommended)
2. **Determine Price Model**: Choose between uniform pricing (simpler) or store-specific pricing (more flexible)

Once these decisions are made and the identified schema changes are implemented:
- Remove `seller_id` from `users`
- Remove `role` from `seller_members` 
- Remove `store_id` from `products`
- Add `store_products` table
- Conditionally add `store_product_variants` table based on price decision
- Change `inventory_items` to reference `store_product_variant_id`

...then Document 1 can be frozen, and we can confidently proceed to Phase 1 (RBAC/Authorization) without fear of foundational redesigns delaying development.

The ownership model changes affect nearly every other system component, making this exactly the right focus for review before freezing the schema—as emphasized throughout the document.