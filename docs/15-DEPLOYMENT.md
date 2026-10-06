# 15 — Deployment

## Environments
Local, staging/test and production. Never use production secrets/data locally.

## Components
Reverse proxy/load balancer, Next.js, Django API workers, Celery workers, PostgreSQL, Redis and object storage/CDN as required.

## Docker
Use Docker for reproducible environments where practical.

## Configuration
DEBUG, SECRET_KEY, database, allowed hosts, CORS/CSRF origins, Redis, email, storage, payment providers, Celery and logging configuration are environment-controlled.

## Production
DEBUG=False, HTTPS, secure cookies, security headers, restricted CORS, protected database network and least-privilege credentials.

## Migration deployment
Backup -> review migration -> staging -> compatible deployment -> migrate -> verify -> monitor.

Never run destructive production migrations without explicit approval/recovery plan.

## CI/CD
PR -> lint -> typecheck -> tests -> build -> security checks -> review -> merge -> staging -> smoke tests -> production approval -> deploy.

## Monitoring
API errors, latency, DB connections, CPU/memory, queue depth, failed tasks, payment/webhook failures and inventory anomalies.

## Recovery
Automated encrypted backups and periodic restore tests.

## Rollback
Define code rollback, migration compatibility, feature flags where useful and data recovery procedures.
