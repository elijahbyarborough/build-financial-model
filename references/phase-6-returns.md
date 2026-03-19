# Phase 6 — Returns

**Reference:** `references/phase-6-returns.md`
**Prerequisite:** Phase 5 complete (Model tab populated).
**Next phase:** Phase 7 (Formatting) → load `references/phase-7-formatting.md`

---

## What Happens in This Phase

Build the Returns tab — exit-multiple valuation, XIRR/MOIC returns analysis, sensitivity tables.

1. **Entry & exit assumptions**: entry price, exit P/E
2. **Unified 5-year timeline**: NTM EPS blending via YEARFRAC
3. **Dividend cash flow schedule**: YEARFRAC-weighted DPS
4. **Net cash flow row**: entry → dividends → exit
5. **XIRR**: from net cash flow and date rows
6. **IRR attribution**: EPS CAGR + dividend yield + multiple change
7. **Sensitivity table**: exit multiple × entry price → IRR

---

## Key Rules

- **Exit-multiple preferred.** Hierarchy: FCF > P/E > EBIT > EBITDA.
- **Only 2 hardcoded assumptions**: entry price and exit multiple. Everything else is formula-driven.
- **NTM EPS uses YEARFRAC blending** at entry and exit.
- **Dividend counting is YEARFRAC-weighted**: partial at entry, full in middle, partial at exit.
- **Pulls from Model tab** for EPS, DPS, and other metrics.
- **After completion**, add Intrinsic Value per Share back to Model tab KPI section.
- **No freeze panes. All formatting defers to `firm-formatting`.**

**STOP. Update Task Tracker. Report XIRR and results. Wait for "continue."**
