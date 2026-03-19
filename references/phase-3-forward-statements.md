# Phase 3 — Forward Statement Build (Model View Projections)

**Reference:** `references/phase-3-forward-statements.md`
**Prerequisite:** Phase 2 complete (all build tabs populated with historicals + projections, Driver Review passed).
**Next phase:** Phase 4 (Capital Allocation) → load `references/phase-4-capital-allocation.md`

---

## What Happens in This Phase

Project the Model View forward on the IS, BS, and CF tabs by linking to the build tabs populated in Phase 2. The Reported View stays historical-only — its projection columns remain blank.

1. **IS Model View projections**: Pull revenue from Revenue Build, costs from Costs Build, D&A from PP&E Build, interest from Debt Build, tax from Tax Schedule. Compute EBITDA, EBIT, EBT, NI, EPS, margins.
2. **BS Model View projections**: Pull PP&E from PP&E Build, debt from Debt Build, WC items from Working Capital Build. Compute totals and BS Check.
3. **CF Model View projections**: Pull NI from IS, D&A from PP&E Build, WC deltas from WC Build, noncurrent operating deltas from WC Build, DTL changes from Tax Schedule, capex from PP&E Build, debt changes from Debt Build. Compute CFO, CFI, CFF, ending cash, CF Check.

---

## The CF is the BS Bridge

In projections, every CF line item is a delta from a BS item or build tab. The assembly rule:

- **CFO non-cash items** = deltas from build tabs (ΔWC from WC Build, ΔDTL from Tax Schedule, ΔARO from WC Build noncurrent section, D&A from PP&E Build)
- **CFI** = negated capex from PP&E Build + acquisitions (set to 0 — placeholder for Phase 4)
- **CFF** = debt changes from Debt Build + dividends (0 placeholder) + buybacks (0 placeholder) — Phase 4 will re-link these
- **Cash = plug** (beginning + all flows = ending)

**If any BS item changes in projections and has no CF flow, the model is broken.** Consult the BS→CF mapping from Phase 1. Every mapped item must have its delta flowing through the CF.

---

## Reported View: Historical Only

The Reported View on IS, BS, and CF contains historical periods only. Projection columns are blank. The Reported View's job is auditability — proving the model ties to GAAP for the periods where GAAP data exists.

The reconciliation check (Model View Total = Reported View Total) applies to **historical periods only**, since the Reported View has no projections to compare against.

---

## Capital Allocation Placeholders (Phase 4 Will Overwrite)

This phase uses zero/flat placeholders for all items that depend on the Capital Allocation Build, which does not exist yet:

- **Dividends (CFF)**: set to 0 in all projection years
- **Share Repurchases (CFF)**: set to 0 in all projection years
- **Acquisitions (CFI)**: set to 0 in all projection years
- **Diluted Share Count**: hold flat at most recent historical diluted share count

Phase 4 (Capital Allocation) will overwrite all of these with live formulas linking to the Capital Allocation Build tab. EPS, CF, and BS update automatically once the links are in place.

---

## Integrity Checks

All checks must pass for **every period** (historical AND projected):

1. **BS Check = $0**: Total Assets - Total L&E = 0
2. **CF Check = $0**: Beginning Cash + all CF - Ending Cash = 0
3. **NI ties IS→CF**: Net Income on IS = Net Income on CF
4. **RE roll-forward**: Beginning RE + NI - Dividends = Ending RE

If any check fails in projections, the most likely cause is a BS item changing without a corresponding CF flow. Use the BS→CF mapping to diagnose — find the BS line that changed and trace whether its delta appears on the CF.

**No freeze panes. All formatting defers to `firm-formatting`.**

**STOP. Update Task Tracker. Report all check results. Wait for "continue."**
