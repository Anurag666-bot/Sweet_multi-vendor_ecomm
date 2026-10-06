# 06 — Checkout / Order Flow

## Goal
Create a consistent multi-seller order without overselling or losing financial accuracy.

## Flow
Cart -> validate items -> validate store availability -> validate prices -> validate inventory -> validate delivery -> calculate fees/discounts -> checkout snapshot -> reserve inventory -> create Order -> create SellerOrders -> create OrderItems -> create Payment -> provider -> verified confirmation.

## Cart validation
Validate existence, active store listing, active variant, current price, quantity, inventory, serviceability and address.

The client never controls the final price.

## Multi-seller
One checkout may create one parent Order and multiple SellerOrders.

## Initial order state
`PENDING_PAYMENT`

Frontend payment success does not confirm the order.

## Verified payment
`PENDING_PAYMENT -> PAYMENT_CONFIRMED -> CONFIRMED`

## Seller fulfillment
`CONFIRMED -> ACCEPTED -> PREPARING -> READY_FOR_PICKUP -> PICKED_UP -> OUT_FOR_DELIVERY -> DELIVERED -> COMPLETED`

Cancellation, return and refund are exception flows.

## Payment failure
Payment becomes FAILED; release reservation according to policy; order remains/reverts to a retryable state; customer may retry.

## Expiry
Expired unpaid checkout releases inventory reservations.

## Idempotency
Checkout confirmation must not create duplicate orders on retry.

## Historical snapshot
Order items retain product/variant name, SKU, unit price, quantity, options and totals so later catalog changes do not alter history.

## Cancellation
Validate state -> release inventory if applicable -> update order/seller order -> audit -> notify.

Refund is a separate financial operation.
