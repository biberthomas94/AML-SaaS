# ADR 0001 — Initial Architecture

**Date:** 2025-09-16  
**Status:** Accepted

## Context
We need a demo-ready AML platform quickly with clear RBAC, auditability, and explainable rules.

## Decision
- React + TypeScript for web; NestJS + TypeScript for API
- PostgreSQL via Prisma as primary datastore
- OIDC (Auth0/Cognito) for auth
- JSON-versioned rules engine
- Audit log with hash chaining
- Terraform for infra (AWS VPC, RDS, ALB, ECS/Fargate, Secrets Manager)
- S3 for evidence storage

## Consequences
- Strong developer velocity and ecosystem support
- Clear separation of concerns
- Easy to add providers (KYC/Sanctions) behind adapters
- Straightforward path to production hardening after demo
