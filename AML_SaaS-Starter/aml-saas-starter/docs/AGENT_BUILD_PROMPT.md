# 🔧 MASTER BUILD PROMPT (Paste into your AI agent + repo)

**Project:** AML SaaS MVP for MSBs (Bitcoin ATM operators first)  
**Goal:** Ship a **demo-ready** web app that supports: onboarding/KYC, sanctions screening (stub → swappable provider), transaction ingestion, rules-based alerting, case management, SAR draft/export, immutable audit trail, and basic dashboards.  
**Non-negotiable:** Human-in-the-loop approvals for SARs and sensitive actions. Strong auditability.

---

## 0) Working Agreement for the Agent
- **Source of truth = GitHub Issues** (“task cards”). For each Issue:
  1) Create a new branch: `feature/<issue-#>-<slug>`  
  2) Build to **Acceptance Criteria** in the Issue.  
  3) Write/extend tests.  
  4) Open a PR titled: `<short title> (Closes #<issue>)`.  
  5) In the PR body include: summary, screenshots/GIFs, test results, schema diffs/migrations, risks, and docs links.  
  6) Do **not** merge—wait for human approval.
- **Keep secrets out of code.** Use environment variables and cloud secrets manager.  
- **Explainability:** Any detection/decision must include human-readable rationale.  
- **Docs first mindset:** If you create/alter components, update `/docs/` (ADR, API spec, schemas).  

---

## 1) Target Architecture (decisions)
- **Front end:** React + TypeScript, Vite build, Tailwind for styling.  
- **Back end:** Node.js (NestJS + TypeScript).  
- **DB:** PostgreSQL (via Prisma).  
- **Auth:** Auth0 or AWS Cognito (OIDC/JWT).  
- **Object storage:** S3 (evidence uploads).  
- **Infra as code:** Terraform for VPC, RDS, ALB, ECS/Fargate (or similar).  
- **Workflow engine (optional for demo):** Temporal for case/SAR flows. If not used, implement within API service.  
- **Search/audit (optional):** Start with Postgres tables; design so OpenSearch could be added later.  
- **CI/CD:** GitHub Actions (lint, tests, build, basic security scan).  

---

## 2) Global Quality Bars
- **Language/strictness:** TypeScript strict mode everywhere.  
- **Static checks:** ESLint + Prettier + type-checking must pass.  
- **Security scans:** Add lightweight OWASP ZAP/Semgrep step in CI.  
- **Tests:** Unit tests for services/utils; API integration tests for critical endpoints; Cypress tests for critical UI flows.  
- **Coverage target:** Aim ≥ 70% on new/changed code (don’t block PRs if <70%, but report).  
- **Accessibility:** Keyboard navigation + basic ARIA where applicable.

---

## 3) Data Model (minimum viable)
Create Prisma schema and migrations for at least:
- `users` (id, email, role: analyst|reviewer|admin, created_at)  
- `customers` (id, name, dob, address, phone, email, gov_id_type, gov_id_last4_or_hash, risk_rating, risk_rationale, created_at, updated_at)  
- `transactions` (id, customer_id, kiosk_id, kiosk_location, ts, fiat_amount, cvc_amount, asset_symbol, rate, fee, payment_method, wallet_address, txid, source_type, dest_type, created_at)  
- `alerts` (id, customer_id, rule_id, transaction_ids[], severity, rationale, status: open|in_review|closed, created_at)  
- `cases` (id, case_no, status, assigned_to, related_alert_ids[], notes, created_at, updated_at)  
- `evidence` (id, case_id, object_key, filename, content_type, uploaded_by, uploaded_at)  
- `sars` (id, case_id, status: draft|ready_for_review|approved|locked, fields_json, narrative_md, version, locked_at, locked_by)  
- `audit_log` (id, actor_user_id, entity_type, entity_id, action, before_json, after_json, hash, prev_hash, ts)  
- `rules` (id, name, current_version_id) and `rule_versions` (id, rule_id, version, json_config, author, justification, created_at)  
- `travel_rule_records` (id, transaction_id, originator_json, beneficiary_json, amount, asset, ts, retention_expires_at)  
- `ctr_candidates` (id, person_ref, date, aggregated_cash_in, aggregated_cash_out, details_json)

> Include reasonable indexes (e.g., `transactions.customer_id`, timestamps, and any frequent filter fields).

---

## 4) API Surface (minimal)
- **Auth:** `/auth/me` (returns claims/roles)  
- **Customers:** `POST /customers` (create+screen), `GET /customers/:id`, `PATCH /customers/:id`  
- **KYC/Sanctions:** abstracted via provider adapters; stub mode default  
- **Transactions:** `POST /transactions/import` (CSV/JSON), `GET /transactions?filters`  
- **Rules/Alerts:** `GET /rules`, `POST /rules/:id/version` (new version), `POST /alerts/run` (re-evaluate), `GET /alerts?filters`, `PATCH /alerts/:id`  
- **Cases:** `POST /cases` (from alerts), `GET /cases?filters`, `GET /cases/:id`, `PATCH /cases/:id`, `POST /cases/:id/evidence`  
- **SARs:** `POST /cases/:id/sar`, `GET /sars/:id`, `PATCH /sars/:id`, `POST /sars/:id/approve`, `GET /sars/:id/export?(pdf|docx)`  
- **Audit:** `GET /audit?filters`, `GET /audit/export`  
- **Metrics/Dashboard:** `GET /metrics/overview`

All endpoints must enforce RBAC and write to `audit_log` for sensitive actions.

---

## 5) UI Pages (minimal)
- Login / role-aware nav  
- **Dashboard** (open alerts, cases by status, SARs pending, basic charts)  
- **Alert Queue** (filters: date, rule, risk; open → details)  
- **Case Detail** (timeline from `audit_log`, notes, evidence upload, link to SAR)  
- **Customer Onboarding** (form + screening result + risk rating display)  
- **SAR Draft/Review** (markdown preview, approval workflow, export buttons)

---

## 6) Rules Engine Contract
- **Rules stored as JSON** (versioned).  
- Must support at least:  
  - **Threshold** (e.g., `fiat_amount > 10000`)  
  - **Structuring** (multiple sub-threshold txns in time window; per customer/day)  
  - **Velocity** (count or amount ≥ N within X hours)  
- **Explainability:** Engine returns `{ rule_id, version, matched_tx_ids, rationale: string }`.  
- **Sample config JSON (ship with repo):**
```json
{
  "name": "Threshold_10k",
  "version": 1,
  "type": "threshold",
  "params": { "field": "fiat_amount", "operator": ">", "value": 10000 },
  "severity": "high",
  "window_hours": 24
}
```

---

## 7) SAR Drafting Contract
- Map essential fields (store in `sars.fields_json`): subjects, activity dates, amounts, instruments, locations, narrative.  
- **Narrative generator:** Produce markdown that references **evidence IDs, transaction IDs, dates, amounts, and rationale**.  
- **Approval gate:** Only `reviewer|admin` can approve; after approval set status `locked` with timestamp + actor; write `audit_log` with a content hash.

---

## 8) Travel Rule & CTR (demo-level)
- **Travel Rule trigger:** if `fiat_or_cvc_amount >= 3000` (USD equivalent), require originator/beneficiary minimum info; store in `travel_rule_records` with retention metadata (5 years).  
- **CTR candidates:** daily job aggregates **cash** legs by person/day; store list in `ctr_candidates`; provide CSV export and link to cases when suspicious.

---

## 9) Security, Privacy, and Logging
- **Secrets:** use cloud secrets manager (no secrets in code/PR/CI logs).  
- **PII fields:** encrypt at rest where reasonable (e.g., partial ID, address); never log raw PII.  
- **Audit immutability:** implement hash chain over `audit_log` rows (`prev_hash` + SHA-256 of row content).  
- **Access:** RBAC everywhere; deny by default.  
- **Files:** evidence → S3; store only keys + metadata in DB.

---

## 10) Documentation Deliverables
- `/README.md` (how to run locally via Docker Compose)  
- `/docs/architecture.md` (diagrams + components)  
- `/docs/api.md` (endpoint list + request/response examples)  
- `/docs/schema.md` (ERD + table descriptions)  
- `/docs/rules.md` (rule config schema + examples)  
- `/docs/demo-script.md` (click-through demo)  
- `/docs/adr/` Architecture Decision Records (major choices)  
- `/docs/threat-model.md` (assumptions, mitigations)

---

## 11) Branch/PR Ritual
- Branch from `main` → `feature/<issue-#>-<slug>`  
- Keep PRs under ~400 lines diff when possible (split if larger)  
- PR must include:
  - What/why  
  - Screens/GIFs (for UI)  
  - Test plan & results  
  - Schema migration notes  
  - Any new env vars/secrets  
  - Follow-ups/todos

---

## 12) Initial Execution Plan (the first 8 steps)
1. **Scaffold monorepo + CI** (repo structure, ESLint/Prettier, Dockerfiles, GitHub Actions).  
2. **Terraform placeholders** (plan for VPC/RDS/Secrets; local dev uses Docker Compose).  
3. **Prisma schema v0 + migrations** (tables listed above) + ERD export.  
4. **Auth integration** (Auth0/Cognito), roles + seed users.  
5. **Transactions import** API + React upload form; sample files + validations.  
6. **Rules engine** v1 and three default rules; alert persistence + alert queue UI.  
7. **Case management** (lifecycle, notes, evidence → S3, timeline from audit).  
8. **SAR model + draft + approval + export** + audit immutability.

> After these, add dashboard, Travel Rule stub, CTR candidate aggregation, polish, demo seed data, and the UAT checklist.

---

## 13) Non-functional Targets (demo)
- P95 page load < 2s on alert queue with 10k alerts  
- Ingestion of 10k transactions in < 60s on dev hardware  
- No PII in logs; zero secrets in repo; CI security scan passes

---

## 14) Environment Variables (examples)
Create `.env.example` (no real secrets):
```
NODE_ENV=development
PORT=8080
DATABASE_URL=postgresql://user:pass@localhost:5432/amlsaas
AUTH_ISSUER_URL=<oidc-issuer>
AUTH_AUDIENCE=<api-audience>
S3_BUCKET_EVIDENCE=amlsaas-evidence-dev
```

---

## 15) “Definition of Done” (for every Issue)
- Acceptance Criteria met  
- Tests updated/added (unit + any relevant integration/UI)  
- Lint/type checks pass; CI is green  
- Docs updated (`/docs/*` or README)  
- Screenshots/GIFs for UI changes  
- Audit and security implications considered (state any risks)

---

# END OF MASTER PROMPT
