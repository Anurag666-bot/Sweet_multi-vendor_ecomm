# Sweet Marketplace

Multi-vendor marketplace for:

- Sweets
- Mithai
- Cakes
- Desserts
- Gift boxes
- Bakery products
- Celebration products

## Architecture

### Backend

- Django
- Django REST Framework
- PostgreSQL
- Redis
- Celery
- JWT authentication
- REST API

### Web

- Next.js
- TypeScript
- React

### Mobile

Planned Phase 2:

- React Native / Expo

## Repository Structure

backend/
    config/
    apps/
        accounts/
        sellers/
        stores/
        catalog/
        inventory/
        cart/
        checkout/
        orders/
        payments/
        delivery/
        payouts/
        reviews/
        notifications/
        audit/
    common/
    tests/

web/
    src/
        app/
        components/
        features/
        lib/
        hooks/
        types/
        styles/

docs/
infrastructure/
tests/

## Architecture Pattern

Modular Monolith.

Each business module owns its:

- Models
- Serializers
- Views
- Services
- Selectors
- Permissions
- URLs
- Tests

## Development Rules

1. No direct push to main.
2. Work through feature branches.
3. Pull requests are required.
4. Database migrations must be reviewed.
5. Never commit secrets.
6. Backend controls business rules.
7. Frontend never decides payment success.
8. Seller data must be isolated server-side.
9. Financial calculations must be backend-controlled.
10. Frozen architecture requires approval before changes.

## Git Branches

main
develop

Feature branches:

feature/*
fix/*
chore/*
docs/*

## Development Flow

BACKLOG
    ↓
TODO
    ↓
IN PROGRESS
    ↓
CODE REVIEW
    ↓
TESTING
    ↓
DONE
