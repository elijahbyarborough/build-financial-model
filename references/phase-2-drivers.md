# Phase 2 — Driver Buildout

**Reference:** `references/phase-2-drivers.md`
**Prerequisite:** Phase 1 complete (historical IS/BS/CF verified, all checks pass, BS→CF mapping done).
**Next phase:** Phase 2.5 (Driver Review), then Phase 3 → load `references/phase-3-forward-statements.md`
**Also load:** `references/methodology.md` for detailed projection rules.

---

## What Happens in This Phase

Populate all build tabs with historical data and driver-based forward projections. The BS→CF mapping from Phase 1 is the blueprint — every BS item that was mapped to a build tab must appear here with an explicit driver.

1. **Revenue Build** (or segment tabs): driver-based revenue using the cascade from sector discovery
2. **Costs Build**: margin analysis, OpEx projections, incremental margins
3. **PP&E Build**: capex split (maintenance + growth), depreciation schedule, net PP&E roll-forward
4. **Debt Build**: tranche-by-tranche balances, interest expense
5. **Working Capital Build**: ALL operating BS items — current AND noncurrent — with explicit drivers
6. **Tax Schedule**: effective tax rate, NOL balances, DTA/DTL

---

## Mapping Requirement: Consult the BS→CF Map

Before building any tab, consult the BS→CF mapping from Phase 1. Every BS item assigned to a build tab must appear on that tab with:
1. Historical values linked to source
2. An explicit inline driver assumption (yellow bg + blue text)
3. A projected balance
4. A computed period-over-period change (delta) that the CF will reference in Phase 3

**If a BS item is missing from its assigned build tab, add it.** No BS item may be left without a driver if it changes in projections.

---

## Working Capital Build: Comprehensive Scope

The Working Capital Build covers ALL operating BS items not on another dedicated build tab:

**Current operating items:** AR, Inventory, Prepaid, Other CA, AP, Accrued Liabilities, Deferred Revenue, Other CL

**Noncurrent operating items:** ARO, Operating Lease ROU Assets & Liabilities, Other Noncurrent Assets, Other Noncurrent Liabilities, Pension/Postretirement, Deferred Tax Assets (if not on Tax Schedule)

Each item gets the 4-step treatment:
1. **Identify**: from the BS→CF mapping
2. **Select Driver**: DSO/DIO/DPO, % of revenue, % of COGS, % of OpEx, fixed dollar, or growth rate — whichever is most stable historically
3. **Backtest**: check ratio stability (CV >25% → flag and consider alternatives)
4. **Structure**: inline assumption → projected balance → period-over-period delta

**Two summary output rows** for the CF:
- **Change in Working Capital (CFO):** net of all current item deltas
- **Noncurrent Operating Changes (CFO):** net of all noncurrent item deltas

**No BS item may use an embedded growth rate** (e.g., `=K35*1.02`) unless the same growth is captured via a build tab with a visible driver assumption. The safe pattern: build tab has the driver → BS links to the build tab balance → CF links to the build tab delta.

---

## Tax Schedule: DTL/DTA Output for CF

The Tax Schedule must output the period-over-period change in Net DTL (or Net DTA) so the CF can reference it as a non-cash CFO adjustment. The driver for DTL growth must be a visible yellow/blue assumption cell, not embedded in a formula.

---

## Key Rules for This Phase

- **Historicals link to source tabs** — never hardcoded.
- **Assumptions are inline** — same row as the metric, projection columns. Yellow bg + blue text + source comment.
- **Revenue is driver-based** — volume × price/rate strongly preferred.
- **PP&E acquisitions in projection years: set to 0 as a placeholder.** Phase 4 (Capital Allocation) will overwrite with a live link. Never back-solve acquisitions as a plug.
- **Non-contractual debt changes (optional prepayment, new issuance beyond scheduled amort) in projection years: set to 0 as a placeholder.** Phase 4 will incorporate final debt decisions.
- **Each build tab outputs both balances (for BS) and deltas (for CF)** — the statements pull from build tabs in Phase 3.
- **No freeze panes.**

---

## Driver Review Checkpoint (Phase 2.5)

Before proceeding to Phase 3, conduct a meta-review of every key driver:

For each driver assumption:
1. Is this the right driver for this line item?
2. Does the projected ratio map to historical patterns?
3. Would a buyside analyst drive it this way?
4. Confidence level: HIGH / MEDIUM / LOW

Flag MEDIUM and LOW confidence drivers. Ask the analyst for confirmation before proceeding.

**STOP. Update Task Tracker. Report driver review results. Wait for "continue."**
