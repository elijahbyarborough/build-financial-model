# Phase 8 — QC (Quality Control)

**Reference:** `references/phase-8-qc.md`
**Prerequisite:** Phase 7 complete (formatting applied).
**Also load:** `references/data-quality.md` for the 4 quality gates and formula rules.
**Next phase:** Phase 9 (Output) → load `references/phase-9-output.md`

---

## What Happens in This Phase

Run structural and formula QC across the entire model. The audit reads — it does not modify. All findings go to the Task Tracker.

---

## 4 Quality Gates

| Gate | What It Checks | Pass Criteria |
|------|---------------|---------------|
| 1. Structural integrity | BS Check = 0, CF Check = 0, NI linkage, RE roll-forward, BS→CF mapping holds | Zero errors, all periods |
| 2. Assumption review | All blue-text cells sourced, no stale assumptions (>90 days) | Every assumption sourced and current |
| 3. Model tab consistency | Summary numbers match underlying tabs | Zero mismatches |
| 4. Returns sanity | XIRR internally consistent, Returns tab pulls from Model tab correctly, entry price current | IRR plausible |

### Gate Failure Protocol
- **Gate 1**: Fix structural errors first. No exceptions.
- **Gate 2**: Flag, propose fix, get user confirmation.
- **Gate 3**: Refresh Model tab.
- **Gate 4**: Trace upstream to the offending assumption.

---

### Lease-Specific QC Checks

If `HAS_OPERATING_LEASES = Y` or `HAS_FINANCE_LEASES = Y`, run these additional checks:

1. **BS tie-out**: Verify that every lease balance on the BS (OL ROU, OL Liability, FL ROU if separate, FL Current, FL Noncurrent) matches the corresponding Debt Build ending balance for each projection year. Any mismatch indicates a broken link or missing WC Build row.

2. **CF Finance Lease payment = principal only**: Confirm that the CF Financing line for finance lease payments pulls from the Debt Build DEPRECIATION row (principal portion), NOT the total payment row (depreciation + interest). If the BS check is off by approximately the FL interest amount, this is almost certainly the cause.

3. **No interest double-count**: FL interest must appear in exactly two places: (a) IS interest expense, and (b) Debt Build FL Schedule interest accrual. It must NOT also appear in the CF Financing payment. Trace the CF Financing FL payment formula to verify it references only the depreciation/principal row.

4. **D&A completeness**: Verify that total D&A on the CF operating section includes FL depreciation. If `FL_IN_PPE=Y`, confirm PP&E Build total D&A includes it. If `FL_IN_PPE=N`, confirm CF pulls D&A from both PP&E Build and Debt Build.

5. **WC Build disaggregation**: Confirm that "Other Current Liabilities" and "Other Noncurrent Liabilities" on the WC Build have formulas that SUBTRACT the finance lease components in historical periods (to avoid double-counting when projections pull lease items directly from the Debt Build).

6. **Current portion not hardcoded**: Verify that the current portion of both OL and FL liabilities in projection years is formula-driven (not a static number carried forward from the last historical year).

---

## Key Rules

- **Does NOT fix issues** — tags each finding with the responsible skill and adds to Task Tracker.
- **Severity levels**: CRITICAL (wrong), WARNING (could mislead), INFO (worth noting).
- **No plugs.**
- **Validates against Core Rules** in build-model SKILL.md.
- **Validates formatting against `firm-formatting`.**
- **No freeze panes anywhere.**

**STOP. Update Task Tracker with all findings. Model is complete when all gates pass.**
