# API Surface (MVP)

All endpoints require JWT with role claims. Responses are JSON unless exporting files.

## Auth
- `GET /auth/me` → returns user claims/roles

## Customers
- `POST /customers` → create + screen (stub provider by default)
- `GET /customers/:id`
- `PATCH /customers/:id`

## Transactions
- `POST /transactions/import` → upload CSV/JSON; returns counts & errors
- `GET /transactions?customer_id=&date_from=&date_to=&kiosk_id=&min_amount=&max_amount=`

## Rules & Alerts
- `GET /rules` → list rules (current versions)
- `POST /rules/:id/version` → create a new rule version
- `POST /alerts/run` → evaluate rules on a period / dataset
- `GET /alerts?status=&rule=&severity=&customer_id=&date_from=&date_to=`
- `PATCH /alerts/:id` → update status / notes

## Cases
- `POST /cases` → open a case from one or more alerts
- `GET /cases?status=&assignee=&q=`
- `GET /cases/:id`
- `PATCH /cases/:id` → updates (status, assignment, notes)
- `POST /cases/:id/evidence` → upload evidence (S3)

## SARs
- `POST /cases/:id/sar` → create a SAR draft from a case
- `GET /sars/:id`
- `PATCH /sars/:id` → update fields_json / narrative_md
- `POST /sars/:id/approve` → reviewer/admin locks SAR & writes audit hash
- `GET /sars/:id/export?type=pdf|docx`

## Audit
- `GET /audit?entity_type=&entity_id=&actor=&date_from=&date_to=`
- `GET /audit/export` → CSV/JSON for exam support

## Dashboard/Metrics
- `GET /metrics/overview` → cards for open alerts, cases by status, SARs pending, etc.
