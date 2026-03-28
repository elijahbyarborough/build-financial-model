# Phase 1 — Historical Statement Build

**Reference:** `references/phase-1-historical-statements.md`
**Prerequisite:** Phase 0 complete (all tabs created with correct naming, formatting, columns, dates, colors).
**Next phase:** Phase 2 (Drivers) → load `references/phase-2-drivers.md`

---

## What Happens in This Phase

Build the IS, BS, and CF tabs with **historicals only** — no projections yet. This establishes a verified historical foundation that the entire model will build on.

The key outputs are:
1. A **Reported View** on each statement (IS/BS/CF) that mirrors GAAP presentation
2. A **Model View** on each statement that condenses the Reported View into analytical buckets suitable for driving projections
3. A **BS→CF mapping** proving every balance sheet change has a home on the cash flow statement
4. All integrity checks passing for every historical period

---

## Step 1: Build the Reported View (Historicals Only)

The Reported View mirrors the company's actual GAAP presentation from the 10-K/10-Q — same line items, same ordering, same subtotals. Use the most exhaustive source tab (Tegus > Broker > BAMSEC > User model).

**Reported View rules:**
- Historical periods only — **projection columns are blank.** The Reported View never gets projections.
- Every historical cell is a formula linking to a source tab (`='Tegus'!D20`). Never hardcoded.
- Subtotals are formulas (`=SUM()`), not hardcoded even if they match the filing.
- Include all line items the company reports — do not consolidate or skip items at this stage.

**Layout:** Standard Tab Header (rows 1-6) → Model View (top, built in Step 2) → Reconciliation Check → Reported View (bottom).

---

## Step 2: Build the Model View (Historicals Only)

The Model View is a condensed, analytical version of the Reported View. It groups GAAP line items into buckets that will become natural driver targets in Phase 2.

**Model View design principles:**
- **IS Model View**: Consolidate detailed OpEx lines into analytical categories (COGS, SG&A, R&D, D&A, SBC, Other). Add analytical subtotals not in GAAP: Gross Profit, EBITDA, EBIT. Add margin rows (italic %).
- **BS Model View**: Group into analytical categories: Cash, Operating Current Assets, PP&E, Goodwill & Intangibles, Other Noncurrent Assets, M&A Assets / Operating Current Liabilities, Debt (current + noncurrent), Other Noncurrent Liabilities, Equity. Each category maps to a build tab. **M&A Assets is a structural placeholder** — set to 0 across all historical periods. It exists so Phase 4 can link cumulative acquisition spend here without touching Goodwill or the organic model (see `references/methodology.md`).
- **CF Model View**: Organize into CFO (NI + non-cash adjustments + WC changes), CFI (capex + acquisitions + other), CFF (debt + dividends + buybacks + other). Each line should correspond to a BS change.

**Model View rules:**
- Historical periods only at this stage — projection columns are blank (they get populated in Phase 3).
- Every cell references either the Reported View on the same tab or a source tab. No hardcodes.
- Each Model View line item should map to either a build tab or a derived computation. If you can't identify where a line item will be driven from, that's a gap to resolve before Phase 2.

---

## Step 3: Reconciliation Check (Historical Periods Only)

Between Model View and Reported View on each tab, include a reconciliation row:

- **IS**: `Model View Net Income - Reported View Net Income = 0`
- **BS**: `Model View Total Assets - Reported View Total Assets = 0`
- **CF**: `Model View Ending Cash - Reported View Ending Cash = 0`

Must equal zero for every historical period. This proves the Model View's consolidation didn't lose or double-count anything.

---

## Step 4: BS→CF Mapping

**CRITICAL:** Before proceeding to Phase 2, map every BS line item to its CF home. The Cash Flow Statement is the mathematical bridge between consecutive Balance Sheets — every BS change must appear somewhere on the CF.

Create a mapping (on the Task Tracker or as a working section):

| BS Item | Source | CF Category | Build Tab |
|---------|--------|-------------|-----------|
| Cash | source ref | PLUG (= Beg + all CF) | — |
| AR | source ref | CFO: ΔWC | Working Capital Build |
| Inventory | source ref | CFO: ΔWC | Working Capital Build |
| PP&E net | source ref | CFI: Capex; CFO: D&A | PP&E Build |
| Goodwill | source ref | CFI: Acquisitions | — (flat if no M&A) |
| Total Debt | source ref | CFF: Net Debt | Debt Build |
| DTL | source ref | CFO: Deferred Tax | Tax Schedule or WC Build |
| ARO | source ref | CFO: Noncurrent Ops | Working Capital Build |
| RE | derived | NI (IS→CFO) + Div (CFF) | — |
| *(every other BS item)* | ... | ... | ... |

**Every row must have a CF home.** If a BS item changes and you can't identify where on the CF it flows, that's a structural gap that must be resolved before Phase 2.

**Key principle:** Cash is the PLUG — it equals Beginning Cash + all CF flows. The BS balances by construction because the CF IS the set of all BS changes.

---

## Step 4b: Lease Schedule Historicals (Debt Build)

If `HAS_OPERATING_LEASES = Y`, add an **Operating Lease Schedule** section to the Debt Build with historical rows:
- **Drivers**: New Operating Leases (implied from ROU roll-forward: `Ending ROU - Beginning ROU + Lease Cost`), Implied Useful Life (`Prior Liability / Lease Cost`), Operating Lease Cost, Lease Cost as % of Revenue (memo)
- **Balance Sheet Items**: Operating Lease ROU Asset Net, Operating Lease Liability -- Current, Operating Lease Liability -- Noncurrent, Total Operating Lease Liability

If `HAS_FINANCE_LEASES = Y`, add a **Finance Lease Schedule** section below:
- **ROU Asset**: Beginning Gross, New Additions, Ending Gross, Accumulated Depreciation, Ending Net
- **Liability**: Beginning, New Additions, Interest Accrual, Cash Payment, Ending, Current, Noncurrent
- **Expense Memo**: Depreciation of ROU Assets, Interest on Lease Liabilities, Total Finance Lease Cost
- **Drivers**: Implied Useful Life, Implied Interest Rate (`Interest / Average Liability`)

All historical values must be formulas linking to the source data tab -- never hardcoded.

### BS Mapping for Leases

For each lease component, check the Lease BS Map to determine whether it has its own dedicated
BS line or is embedded inside a broader line. For embedded components, the WC Build must
disaggregate: create an "ex-lease" version of the host line using the formula pattern from
methodology.md § Working Capital Build Disaggregation. The map tells you exactly which reported
lines to strip lease amounts from.

### WC Build Historical Disaggregation

The Working Capital Build must include dedicated rows for each lease balance sheet item mapped in the Lease BS Map. For historical periods, each lease row pulls from the Debt Build (which sources from the filing). For each lease component that is embedded inside a broader reported BS line (per the Lease BS Map), create an "ex-lease" version of that host line:

```
[Reported BS line] (ex-[lease component]) = BS![Reported line as reported] - 'Debt Build'![lease component]
```

If a lease component has its own dedicated BS line (e.g., "Operating lease liabilities, current"), no disaggregation is needed — the WC Build row simply references it directly.

If multiple lease components are embedded in the same reported line, disaggregate all of them from that line.

This disaggregation is essential -- without it, lease items will be projected using revenue-based WC drivers instead of the Debt Build schedule, causing BS imbalances.

---

## Step 5: Integrity Checks (HARD GATE)

All of the following must pass for every historical period before proceeding to Phase 2:

1. **BS Check = $0** every historical year (Total Assets - Total L&E)
2. **CF Check = $0** every historical year (Beginning Cash + all CF - Ending Cash)
3. **NI ties IS→CF**: Net Income on IS = Net Income starting the CF
4. **RE roll-forward**: Beginning RE + NI - Dividends = Ending RE
5. **CF bridges BS**: For each historical year, Beginning BS + all CF flows ≈ Ending BS
6. **Reconciliation checks = $0**: Model View totals = Reported View totals for every historical period

If BAMSEC/Tegus CF flows don't perfectly reconcile to BS changes (common due to acquisitions, FX, reclassifications), document the residual as "Historical Reconciliation Difference" — but the structure must ensure that in projections the CF will bridge the BS exactly.

**HARD GATE: If any check fails, STOP and fix it. Do not proceed to Phase 2 with a broken historical foundation.**

**STOP. Update Task Tracker with BS→CF mapping and check results. Report status. Wait for "continue."**
