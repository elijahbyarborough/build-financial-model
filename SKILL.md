---
name: build-model
description: |
  FC modeling methodology and model architecture. Single source of truth for how every model is structured, what tabs exist, how projections are built, and how valuation and returns work. Methodology ONLY — defer to firm-formatting for all formatting.

  Triggers on: building a financial model, creating projections, 3-statement model, revenue build, debt schedule, PP&E build, returns analysis, valuation, XIRR, MOIC, sensitivity, ingesting BAMSEC/Tegus/GS data, rebuilding an existing model, or any model architecture decision.

  Core rules: exit-multiple preferred over DCF, 7 projection years, driver-based revenue, XIRR returns, YEARFRAC calendarization, ROIC/ROTIC/FCF yields standard in every model.
---

# FC Modeling Conventions

## Phase Router — Read This First

Identify the current phase, then load ONLY its reference file. Do not load everything at once.

| Current Phase | Load This Reference |
|---------------|-------------------|
| **New build** | `references/phase-0-setup.md` |
| **Resuming** (Task Tracker exists) | Read Task Tracker → identify current phase → load that phase's reference file below |
| **Phase 1 — Historical Statements** | `references/phase-1-historical-statements.md` |
| **Phase 2 — Drivers** | `references/phase-2-drivers.md` |
| **Phase 2.5 — Driver Review** | `references/phase-2-drivers.md` (checkpoint section) |
| **Phase 3 — Forward Statements** | `references/phase-3-forward-statements.md` |
| **Phase 4 — Capital Allocation** | `references/phase-4-capital-allocation.md` |
| **Phase 5 — Model Tab** | `references/phase-5-model-tab.md` |
| **Phase 6 — Returns** | `references/phase-6-returns.md` |
| **Phase 7 — Formatting** | `references/phase-7-formatting.md` |
| **Phase 8 — QC** | `references/phase-8-qc.md` |

**Resume protocol:** If a Task Tracker tab exists, you are resuming. Read the tracker. Identify the current phase from the Model State Block. Load ONLY that phase's reference file. Do NOT reload Phase 0 content.

**Phase boundaries:** After completing each phase, STOP. Update the Task Tracker. Report status. Wait for "continue."

**Sequencing enforcement:** Each phase must complete before the next begins. Phase 0 → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8. No skipping. Dependencies are specified in each phase's reference file.

All skills are designed for Claude for Excel cell-level operations.

---

## Core Rules (Always Apply — Every Phase)

### No Freeze Panes
**Never freeze panes on any tab, ever.** No exceptions. Gridlines off. No merged cells in data area.

### Projection Length
**7 dynamic projection years** (5yr hold + NTM exit window). Calculate from today's date + FYE. When in doubt, add one more year.

### Historical Periods
As many years of actuals as available. No fixed minimum — use what the data gives you.

### Revenue Build Priority
1. Unit economics / driver-based (volume × price/rate) — strongly preferred
2. Segment-level with identified drivers — acceptable
3. Growth rate decomposition — last resort

Never slap growth rates on revenue without decomposing into drivers.

### Exit-Multiple Preferred
Exit-multiple over DCF. Only build DCF when explicitly requested. Multiple hierarchy: FCF > P/E > EBIT > EBITDA (closest to cash wins).

### Formula Construction
- Always use Excel formulas — never hardcode calculations in Python and paste values
- **Never embed assumptions as in-cell hardcodes within output/data formulas.** Every assumption must be a clearly identifiable cell with **yellow background (#FFFF00) + blue text (#0000FF)** and a source comment.
- Historical totals must also be formulas (`=SUM()` or equivalent)
- Comment every assumption with rationale and source
- Only ASCII in comments (no em dashes, smart quotes)

### Assumption Placement: Same Row, Not Separate
Assumption values go directly in the **projection columns of the existing metric row** — the same row that holds the historical values. Do NOT create a separate "assumption" row underneath. One row carries both: historical values (black text) on the left, assumption values (yellow bg + blue text + source comment) on the right.

### No Hardcoded Historicals
**Every historical value in the model must be a formula linking to a source tab** — never a hardcoded number. Historical cells reference BAMSEC, Tegus, broker model, or the Historical Data tab via direct cell references. The audit trail must be one click away.

### Build-First Rule (HARD STOP)
**Never populate projection assumptions directly on the IS, BS, or CF tabs.** Assumptions and driver logic live on build tabs. The IS/BS/CF tabs contain only:
1. **Pull formulas** — references to build tabs
2. **Derived computations** — formulas computed from other cells on the same statement

### Balance Sheet Integrity
Always include:
- **BS Check row**: `Total Assets - Total Liabilities & Equity = 0`
- **CF Check row**: `Beginning Cash + Total Cash Flow = Ending Cash`
- **Capital Allocation Check**: `Unreconciled Residual = 0`

If any check is non-zero, stop and debug. **Never use a plug, balancing item, or "other" line to force a check to zero.**

### Free Cash Flow
Compute all three: Unlevered FCF (`EBIT × (1 - tax rate) + D&A - Capex - ΔWC`), Levered FCF (`CFO - Capex`), FCFE (`Levered FCF - Debt Repayment + Debt Issuance`).

### ROIC, ROTIC & FCF Yield
Standard in every model. ROIC, ROTIC, Incremental ROIC, Incremental ROTIC, uFCF/EV yield, Levered FCF/Mkt Cap yield, FCFE/Mkt Cap yield. See `references/methodology.md` for full formulas.
