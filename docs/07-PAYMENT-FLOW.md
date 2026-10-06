# 07 — Payment Flow

## Principle
Frontend success is never authoritative. Backend/provider verification is authoritative.

## Providers
eSewa, Khalti and Cash on Delivery through a provider abstraction.

## Payment states
PENDING -> PROCESSING -> SUCCEEDED
or PENDING/PROCESSING -> FAILED

Payment status is separate from order status.

## Online flow
Create Order -> Create Payment -> Provider Attempt -> Provider -> callback/webhook -> verify signature/reference/amount/currency -> idempotency -> store event -> update Payment -> update Order -> publish follow-up.

## Webhooks
Verify signature, event type, amount, currency and merchant/reference IDs. Store a stable provider event ID where available.

Duplicate events must not duplicate payment/order effects.

## Amount
Provider amount must equal backend expected amount. Mismatch requires failure/investigation.

## COD
COD is a separate payment path. It is not treated as an online payment success until collection is actually recorded.

## Refund
Validate refundable state and amount -> create refund -> provider refund if required -> update states -> ledger -> audit.

## Reconciliation
Detect provider-paid/local-missing, local-success/provider-mismatch, duplicates, amount mismatch and failed refunds.

Never log provider secrets, raw credentials, full card data or tokens.
