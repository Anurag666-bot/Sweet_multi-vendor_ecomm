# 10 — Delivery

## Model
A parent order may contain multiple seller fulfillments. Delivery jobs normally correspond to seller fulfillment.

## Flow
CONFIRMED -> ACCEPTED -> PREPARING -> READY_FOR_PICKUP -> Delivery Job -> Rider -> PICKED_UP -> OUT_FOR_DELIVERY -> DELIVERED.

## Zones
Zones define serviceability, fees, estimated time and constraints.

## Fee
Server-calculated using active delivery rules.

## Rider
Riders only see assigned/permitted delivery data.

## Assignment
UNASSIGNED -> ASSIGNED -> ACCEPTED -> REASSIGNED -> COMPLETED/FAILED.

## Tracking
Tracking events are append-only.

## Proof
May use OTP, signature, image, receiver name and timestamp. Access is restricted.

## Failed delivery
Customer unavailable, address problem, store delay or rider issue must be represented as operational failure, not silently delivered.

## Future
Rider location, routing, ETA and batching can be added later.
