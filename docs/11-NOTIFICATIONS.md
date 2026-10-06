# 11 — Notifications

## Channels
In-app, email, SMS and push.

## Events
Account created, order created, payment success/failure, seller accepted/preparing, out for delivery, delivered, refund, payout.

## Architecture
Domain action -> Outbox event -> worker -> template -> provider -> notification delivery record.

## Records
Recipient, event, channel, template, status, provider reference, sent_at, failure information.

## Retry
Retry transient failures; stop after policy-defined attempts for permanent failures.

## Idempotency
One domain event should not unintentionally generate duplicate notifications.

## Preferences
Future support may include marketing opt-out and channel preferences. Required transactional notifications remain enabled where policy/law requires.

## Privacy
Do not send passwords, tokens, payment secrets or unnecessary sensitive data in notifications.
