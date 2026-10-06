# 16 — Team Development

## Team
### Developer 1 — Lead / Architect / Full Stack
Owns architecture, DB, backend, authentication, RBAC, payments, checkout/order, inventory, financial logic, API standards, security, deployment, review and final technical approval.

### Developer 2 — Frontend-focused fresher
Owns Next.js UI, components, customer pages, forms, responsive layouts, seller UI under guidance, API integration and frontend tests.

Initially the fresher does not own DB migrations, auth architecture, payment/financial logic, inventory concurrency, RBAC architecture or production secrets/deployment.

## Repository
sweet-marketplace/
backend/
web/
docs/
infrastructure/
tests/
.env.example
docker-compose.yml
README.md

## Git
main = stable
develop = integration
feature/* = individual work

No direct push to main.

## Workflow
BACKLOG -> TODO -> IN PROGRESS -> CODE REVIEW -> TESTING -> DONE

Feature -> test -> commit -> push -> PR -> review -> fix -> merge -> delete branch.

Fresher does not merge their own PR.

## Task format
ID, owner, objective, dependencies, files/modules, implementation notes, not-to-change, acceptance criteria and tests.

Example: CAT-001 Product List API.

## Commit examples
feat: add product listing API
fix: prevent duplicate inventory reservation
test: add seller isolation tests
docs: update checkout flow
refactor: extract payment service

## PR checklist
Requirement, scope, tests, migrations, permissions, seller/store scope, security, API docs, UI screenshots and secrets review.

## Small tasks
Foundation -> accounts -> RBAC -> seller/store -> catalog -> store catalog -> inventory -> customer UI -> cart -> checkout -> orders -> payment -> delivery -> seller dashboard -> payouts -> notifications -> admin -> hardening.

## Vertical slice
First useful slice:
Backend product API + frontend product listing + PostgreSQL data + tests.

Then Store -> Product listing -> Cart -> Checkout -> Order -> Payment -> Fulfillment.

## Definition of Done
Code works, tests pass, review approved, docs updated, branch merged, no critical unresolved issue.

## Architecture-change policy
DB ownership, RBAC, authentication, payment, order lifecycle, inventory, financial ledger and breaking API changes require lead review before implementation.

## Local development
Each developer uses their own PostgreSQL/Redis and .env. Shared `.env.example`. Never commit secrets.

## Communication
PRs state what changed, why, files, tests, limitations, migration impact and security impact.

## Review priority
1. business correctness
2. security
3. data integrity
4. authorization
5. concurrency
6. tests
7. maintainability
8. style
