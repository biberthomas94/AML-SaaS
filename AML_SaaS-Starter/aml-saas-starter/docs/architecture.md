# Architecture Overview

**Date:** 2025-09-16

This document describes the high-level architecture of the AML SaaS MVP targeted at MSBs operating Bitcoin/CVC kiosks.

## Components
- **Web App (React/TypeScript):** Analyst & Reviewer UI for onboarding, alerts, cases, SAR.
- **API (NestJS/TypeScript):** Business logic, rules evaluation, SAR drafting, RBAC.
- **PostgreSQL (Prisma):** System of record for customers, transactions, alerts, cases, SARs, audit.
- **Object Storage (S3):** Evidence files (documents, images, exports).
- **Auth (Auth0 or Cognito):** OIDC/JWT-based authentication & roles.
- **KYC/Sanctions Providers:** Pluggable adapters (sandbox/stub by default).
- **Infra (Terraform on AWS):** VPC, RDS, ALB, ECS/Fargate (or similar), Secrets Manager.
- **(Optional) Workflow Engine:** Temporal for case lifecycles; optional for MVP.

## Data Flow (Mermaid)
```mermaid
flowchart LR
  A[User (Analyst/Reviewer/Admin)] -->|OIDC Login| B[Auth Provider]
  A --> C[Web App (React)]
  C -->|REST/JSON| D[API (NestJS)]
  D --> E[(PostgreSQL)]
  D --> F[(S3 Evidence)]
  D --> G[Sanctions/KYC Adapters]
  D --> H[Audit Log (append-only)]
  subgraph Cloud
    D
    E
    F
    H
  end
```
