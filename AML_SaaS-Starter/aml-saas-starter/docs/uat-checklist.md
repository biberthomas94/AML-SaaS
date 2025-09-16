# UAT Checklist

- [ ] Login works for Analyst, Reviewer, Admin; role-based nav differs
- [ ] Create customer; sanctions stub result shown; risk rating stored
- [ ] Import transactions (CSV & JSON); errors reported clearly
- [ ] Run rules; threshold/structuring/velocity alerts generated
- [ ] Open case from alert; assign, add notes; evidence upload works
- [ ] Timeline shows audit entries (create, update, upload)
- [ ] SAR draft generated with correct fields and narrative markdown
- [ ] Reviewer approves; SAR becomes locked; audit hash recorded
- [ ] Dashboard shows accurate counts for alerts, cases, SARs
- [ ] Travel Rule record created for ≥ $3,000 equivalent transfer
- [ ] CTR candidate list aggregates cash > $10k/day correctly
- [ ] No PII present in logs; error messages are generic
- [ ] All endpoints deny access without valid JWT/role
