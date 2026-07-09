# Phase 8 — QC (Quality Control)

**Prerequisite:** Phase 7 complete (formatting applied).

---

## What Happens in This Phase

Run structural and formula QC across the entire model. The audit reads — it does not modify. All findings go to the Task Tracker.

---

## 4 Quality Gates

| Gate | What It Checks | Pass Criteria |
|------|---------------|---------------|
| 1. Structural integrity | BS Check = 0, CF Check = 0, NI linkage, RE roll-forward, BS→CF mapping holds, WC denominator consistency | Zero errors, all periods |
| 2. Assumption review | All blue-text cells sourced, no stale assumptions (>90 days) | Every assumption sourced and current |
| 3. Model tab consistency | Summary numbers match underlying tabs | Zero mismatches |
| 4. Returns sanity | XIRR internally consistent, Returns tab pulls from Model tab correctly, entry price current | IRR plausible |

### Gate Failure Protocol
- **Gate 1**: Fix structural errors first. No exceptions.
- **Gate 2**: Flag, propose fix, get user confirmation.
- **Gate 3**: Refresh Model tab.
- **Gate 4**: Trace upstream to the offending assumption.

---

### WC Denominator Consistency Check

For every projected Working Capital line item (AR, AP, Other CA, Accrued Comp, Accrued Expenses, Rev Share, Deferred Revenue, etc.):

1. **Historical denominator identification**: Read the last historical year's formula to identify the denominator used (e.g., `IS!K7` = Revenue, `ABS(IS!K9)` = Segment OI).
2. **Projection denominator confirmation**: Read the first projection year's formula and confirm it references the same IS/Consolidation line.
3. **Mismatch flagging**: Flag any mismatch as **FAIL** with the specific line item, historical denominator, and projection denominator.
4. **NWC reasonableness**: Compute NWC as % of Revenue in the first projection year and compare to the trailing 3-year average. If it deviates by more than ±200 bps, flag for manual review — likely a denominator mismatch or an unreasonable driver assumption.

---

### Lease-Specific QC Checks

If `HAS_OPERATING_LEASES = Y` or `HAS_FINANCE_LEASES = Y`, run these additional checks:

1. **BS tie-out**: For each lease component in the Lease BS Map, verify that the projected BS
   balance matches the corresponding Debt Build ending balance. If the component is embedded in
   a broader BS line, verify that the WC Build "ex-lease" formula correctly strips it. Check
   both current and noncurrent splits. Any mismatch indicates a broken link, a missing WC Build
   row, or an incorrect Lease BS Map entry.

2. **CF Finance Lease payment = principal only**: Confirm that the CF Financing line for finance lease payments pulls from the Debt Build DEPRECIATION row (principal portion), NOT the total payment row (depreciation + interest). If the BS check is off by approximately the FL interest amount, this is almost certainly the cause.

3. **No interest double-count**: FL interest must appear in exactly two places: (a) IS interest expense, and (b) Debt Build FL Schedule interest accrual. It must NOT also appear in the CF Financing payment. Trace the CF Financing FL payment formula to verify it references only the depreciation/principal row.

4. **D&A completeness**: Verify that total D&A on the CF operating section includes FL depreciation. If `FL_IN_PPE=Y`, confirm PP&E Build total D&A includes it. If `FL_IN_PPE=N`, confirm CFS pulls D&A from both PP&E Build and Debt Build.

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

### Tab Organization Check

Verify tab order matches firm standard: Task Tracker first, then Output / Consensus / Returns / Model, then IS / BS / CF, then builds, then data/reference tabs last. Verify all tab colors match the Tab Colors table in firm-formatting. Flag any tab that is out of order or has wrong coloring.

---

## Definition of Done (Phase 8)

A phase is complete if and only if ALL of the following are true. Report completion by reading these values back to the user -- not by summarizing in prose.

1. **Task Tracker**: Every subtask row for Phase 8 shows Status = "COMPLETE". Cite the actual cell addresses you checked.
2. **All 4 QC gates pass**. Read and report each gate's result:
   - Gate 1 (Structural): BS Check=[val], CF Check=[val], NI linkage=[pass/fail], RE roll-forward=[pass/fail], WC denominator consistency=[pass/fail]
   - Gate 2 (Assumptions): [count] blue-text cells reviewed, [count] missing sources, [count] stale
   - Gate 3 (Model Tab): [count] mismatches found
   - Gate 4 (Returns): XIRR=[val], entry price current=[yes/no]
3. **Zero CRITICAL findings** remaining. Any CRITICAL findings must be resolved before proceeding.
4. **All findings logged** to Task Tracker with severity (CRITICAL/WARNING/INFO), responsible skill, and description.
5. **Tab organization verified**: tab order and colors match firm standard.
6. **Task Tracker Model State Block**: "Last Skill Run" updated, "Next Skill" = "build-model-phase-9".

If you write "Phase 8 complete" in chat before reading and reporting these values, you have made an error. Re-verify and correct.

**STOP. Report status. Wait for "continue."**
