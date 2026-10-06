# 12 — Audit Logging

## Purpose
Record security-sensitive and business-sensitive actions.

## Audit
Login/logout/security events, role changes, permission changes, seller approval/suspension, store/catalog changes, inventory adjustments, order transitions, payments, refunds, payouts, configuration changes and sensitive administrative access.

## Fields
id, actor_user_id, action, resource_type, resource_id, seller_id/store_id scope, request_id, allowed IP/user-agent metadata where policy permits, redacted before/after data, created_at.

## Never store
Passwords, access/refresh tokens, payment secrets, full card data or unnecessary personal data.

## Immutability
Append-only to normal users. Corrections use controlled administrative procedures.

## Access
Require explicit `audit.view` permission.

## Retention
Configurable by legal/business/storage requirements. Cleanup must be explicit and auditable.

## Correlation
Use request/correlation IDs so API logs and audit events can be connected.

## Denied actions
Important security-sensitive denied attempts should be recorded.
