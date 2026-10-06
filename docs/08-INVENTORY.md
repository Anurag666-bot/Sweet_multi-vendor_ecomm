# 08 — Inventory

## Ownership
Seller -> Store -> StoreProduct -> StoreProductVariant -> InventoryItem.

## Quantity
Every inventory item has explicit unit semantics: pieces, grams, kilograms, boxes, litres, etc.

## Available-to-sell
`available_to_sell = on_hand - reserved`

## Reservation
Lock inventory row -> calculate available -> validate quantity -> increment reserved -> create reservation -> commit.

## Consume
On successful confirmed order:
- reservation CONSUMED;
- on_hand decreases;
- reserved decreases;
- stock ledger records movement.

## Release
Payment failure/cancellation/expiry:
- reservation RELEASED;
- reserved decreases;
- on_hand unchanged.

## Adjustments
Authorized staff only. Require reason, actor, before/after and audit record.

## Concurrency
PostgreSQL transaction/row locking prevents two buyers from consuming the same last unit.

## Ledger
Append-only. Corrections are new movements, not edits to history.

## Reconciliation
Compare inventory totals, ledger, active reservations and consumed orders.

## Perishables
Future batch/expiry/FEFO support should be introduced only when required by business policy.
