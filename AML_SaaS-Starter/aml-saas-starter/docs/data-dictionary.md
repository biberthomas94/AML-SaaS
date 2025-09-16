# Data Dictionary (MVP focus)

## Customers (KYC)
- Full name, DOB, address, phone, email
- Government ID type + redacted/hashed number
- Sanctions/PEP screening result
- Risk rating (Low/Medium/High) + rationale

## Transactions
- Timestamp, kiosk_id, kiosk_location
- Asset (BTC/etc), fiat_amount, cvc_amount, rate, fee
- Payment method (cash), wallet_address, txid
- Source/destination type (hosted vs self-hosted)

## Alerts
- Rule id/version, severity, rationale text
- Related transaction ids and customer id

## Cases
- Case number, status, assignee, notes
- Evidence keys (S3), timeline entries (audit)

## SARs (Form-aligned fields)
- Subjects, activity type, instruments, locations
- Date range, amounts, indicators/typologies
- Narrative (markdown) referencing evidence/txids
- Status (draft/ready/approved/locked)

## Travel Rule (≥ $3,000 equivalent)
- Originator: name, account/wallet id, financial institution (if any)
- Beneficiary: name, account/wallet id, financial institution (if any)
- Amount, asset, timestamp
- Retention metadata (5 years)

## CTR Candidates (cash > $10k/day)
- Person reference (customer id or KYC key)
- Date, aggregated_cash_in, aggregated_cash_out
- Supporting details JSON (txn ids, kiosks, notes)
