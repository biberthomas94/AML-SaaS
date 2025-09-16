# Demo Script (Customer → Transaction → Alert → Case → SAR)

1) **Login** as Analyst (show role-aware nav).  
2) **Customer onboarding**: create a new customer via form; show sanctions/KYC stub; display risk rating.  
3) **Import transactions**: upload sample CSV; confirm counts and errors.  
4) **Run rules**: trigger evaluation; open **Alert Queue**; filter by rule type.  
5) **Open a case** from an alert; add a note; upload a sample evidence file; show timeline in Case Detail.  
6) **Generate SAR draft** from the case; show markdown narrative referencing transactions and evidence.  
7) **Reviewer login**: approve SAR; show **locked** state and audit entry/hash.  
8) **Dashboard**: point to open alerts, cases by status, SARs pending.  
9) **Travel Rule**: show a txn ≥ $3,000 equivalent; capture originator/beneficiary fields in record.  
10) **CTR candidates**: show aggregated cash > $10k/day list and CSV export.
