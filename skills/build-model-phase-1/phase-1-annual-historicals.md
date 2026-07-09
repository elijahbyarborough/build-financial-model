# Phase 1 — Annual Historicals & Statements

**Prerequisite:** Phase 0 complete (all tabs created with correct naming, formatting, columns, dates, colors).

---

## What Happens in This Phase

Capture the company's reported annual data onto the **Annual Historicals** tab, then build the IS, BS, and CFS tabs with **historicals only** — no projections yet. This establishes a verified historical foundation that the entire model will build on.

The key outputs are:
1. The **Annual Historicals** tab fully captured per the canonical section hierarchy (`meth-historicals.md`)
2. A **Model View** on each statement (IS/BS/CFS) — the only view; historical cells link to Annual Historicals
3. **Reconciliation checks** proving the Model View ties to reported totals
4. A **BS→CF mapping** proving every balance sheet change has a home on the cash flow statement
5. Historical **lease schedules** on the BS & CFS Build (if leases present)
6. All integrity checks passing for every historical period

---

## Step 1: Capture the Annual Historicals Tab

Capture everything the company reports, annually, for all available history, onto the Annual Historicals tab. Follow `meth-historicals.md` exactly: the 13-section hierarchy, the capture-everything principle, and the two governing rules:

- **Single Hardcode Layer**: every captured cell is a hardcoded value with blue text (#0000FF) + source comment (`Source: [Document Type], [Date], [Specific Reference]`). This is the ONLY tab (with Quarterly Historicals) where hardcoded historicals are allowed.
- **Capture Everything**: full statement faces, every segment line, every disclosed KPI, share counts, debt detail, lease detail, non-GAAP reconciliations. Do not consolidate or skip lines at capture. If the company reports it, capture it.

Use the most exhaustive source (Tegus > Broker > BAMSEC > User model, per the Phase 0 Source Exhaustiveness Rule). Cross-check ambiguous values against a second source where available. Apply any edge-case protocols that history requires (segment recasts, ASC adoptions, FYE stubs — see `meth-historicals.md`) and populate the Change Log.

**Subtotals within captured faces**: capture reported subtotals as hardcodes like any other reported line, then add a check row where useful (`=SUM(components) - reported subtotal = 0`, black formula) to catch entry errors on long faces.

---

## Step 2: Build the Model View (Historicals Only)

Each statement tab (IS, BS, CFS) carries a single view: the **Model View** — a condensed, analytical presentation that groups reported line items into buckets that will become natural driver targets in Phase 3 (Drivers).

**Model View design principles:**
- **IS Model View**: Consolidate detailed OpEx lines into analytical categories (COGS, SG&A, R&D, D&A, SBC, Other). Add analytical subtotals not in GAAP: Gross Profit, EBITDA, EBIT. Add margin rows (italic %).
- **BS Model View**: Group into analytical categories: Cash, Operating Current Assets, PP&E, Goodwill & Intangibles, Other Noncurrent Assets, M&A Assets / Operating Current Liabilities, Debt (current + noncurrent), Other Noncurrent Liabilities, Equity. Each category maps to a build-tab section. **M&A Assets is a structural placeholder** — set to 0 across all historical periods. It exists so Phase 5 (Capital Allocation) can link cumulative acquisition spend here without touching Goodwill or the organic model.
- **CFS Model View**: Organize into CFO (NI + non-cash adjustments + WC changes), CFI (capex + acquisitions + other), CFF (debt + dividends + buybacks + other). Each line should correspond to a BS change.

**Model View rules:**
- Historical periods only at this stage — projection columns are blank (they get populated in Phase 4).
- **Every historical cell is a formula linking to the Annual Historicals tab** (`='Annual Historicals'!D20`) or a derived computation from other cells on the same statement. No hardcodes anywhere on IS/BS/CFS.
- Each Model View line item should map to either a build-tab section or a derived computation. If you can't identify where a line item will be driven from, that's a gap to resolve before Phase 3.
- Where the Model View consolidates several reported lines into one bucket, the bucket formula sums the relevant Annual Historicals cells (`=SUM('Annual Historicals'!D20, 'Annual Historicals'!D24)`) — consolidation happens in the formula, keeping the audit trail explicit.

---

## Step 3: Reconciliation Checks (Historical Periods Only)

On each statement tab, include reconciliation rows tying the Model View to the reported record on Annual Historicals:

- **IS**: `Model View Net Income - 'Annual Historicals' reported Net Income = 0`
- **BS**: `Model View Total Assets - 'Annual Historicals' reported Total Assets = 0`
- **CFS**: `Model View Ending Cash - 'Annual Historicals' reported Ending Cash = 0`

Must equal zero for every historical period. This proves the Model View's consolidation didn't lose or double-count anything relative to what the company reported.

---

## Step 4: BS→CF Mapping

**CRITICAL:** Before proceeding to Phase 3, map every BS line item to its CF home. The Cash Flow Statement is the mathematical bridge between consecutive Balance Sheets — every BS change must appear somewhere on the CFS.

Create a mapping (on the Task Tracker or as a working section):

| BS Item | Source | CF Category | Build Tab Section |
|---------|--------|-------------|-------------------|
| Cash | Annual Historicals | PLUG (= Beg + all CF) | — |
| AR | Annual Historicals | CFO: ΔWC | BS & CFS Build — Working Capital |
| Inventory | Annual Historicals | CFO: ΔWC | BS & CFS Build — Working Capital |
| PP&E net | Annual Historicals | CFI: Capex; CFO: D&A | BS & CFS Build — PP&E & Capex |
| Goodwill | Annual Historicals | CFI: Acquisitions | — (flat if no M&A) |
| Total Debt | Annual Historicals | CFF: Net Debt | BS & CFS Build — Debt & Cash |
| DTL | Annual Historicals | CFO: Deferred Tax | Profit Build — Tax |
| ARO | Annual Historicals | CFO: Noncurrent Ops | BS & CFS Build — Working Capital |
| RE | derived | NI (IS→CFO) + Div (CFF) | — |
| *(every other BS item)* | ... | ... | ... |

**Every row must have a CF home.** If a BS item changes and you can't identify where on the CF it flows, that's a structural gap that must be resolved before Phase 3.

**Key principle:** Cash is the PLUG — it equals Beginning Cash + all CF flows. The BS balances by construction because the CF IS the set of all BS changes.

---

## Step 4b: Lease Schedule Historicals (BS & CFS Build)

If `HAS_OPERATING_LEASES = Y`, add an **Operating Lease Schedule** section to the BS & CFS Build with historical rows:
- **Drivers**: New Operating Leases (implied from ROU roll-forward: `Ending ROU - Beginning ROU + Lease Cost`), Implied Useful Life (`Prior Liability / Lease Cost`), Operating Lease Cost, Lease Cost as % of Revenue (memo)
- **Balance Sheet Items**: Operating Lease ROU Asset Net, Operating Lease Liability -- Current, Operating Lease Liability -- Noncurrent, Total Operating Lease Liability

If `HAS_FINANCE_LEASES = Y`, add a **Finance Lease Schedule** section below:
- **ROU Asset**: Beginning Gross, New Additions, Ending Gross, Accumulated Depreciation, Ending Net
- **Liability**: Beginning, New Additions, Interest Accrual, Cash Payment, Ending, Current, Noncurrent
- **Expense Memo**: Depreciation of ROU Assets, Interest on Lease Liabilities, Total Finance Lease Cost
- **Drivers**: Implied Useful Life, Implied Interest Rate (`Interest / Average Liability`)

All historical values in these schedules are formulas linking to the **Annual Historicals** tab (Lease Detail section, Section 10) — never hardcoded here, and never linked to raw source tabs.

### BS Mapping for Leases

For each lease component, check the Lease BS Map to determine whether it has its own dedicated
BS line or is embedded inside a broader line. For embedded components, the Working Capital
section of the BS & CFS Build must disaggregate: create an "ex-lease" version of the host line
using the formula pattern from `meth-lease-full.md` § Working Capital Disaggregation. The map
tells you exactly which reported lines to strip lease amounts from.

### Working Capital Historical Disaggregation

The Working Capital section of the BS & CFS Build must include dedicated rows for each lease balance sheet item mapped in the Lease BS Map. For historical periods, each lease row pulls from the lease schedule sections on the same tab (which source from Annual Historicals). For each lease component that is embedded inside a broader reported BS line (per the Lease BS Map), create an "ex-lease" version of that host line:

```
[Reported BS line] (ex-[lease component]) = BS![Reported line as reported] - [lease schedule ending balance, same tab]
```

If a lease component has its own dedicated BS line (e.g., "Operating lease liabilities, current"), no disaggregation is needed — the Working Capital row simply references it directly.

If multiple lease components are embedded in the same reported line, disaggregate all of them from that line.

This disaggregation is essential -- without it, lease items will be projected using revenue-based WC drivers instead of the lease schedules, causing BS imbalances.

---

## Step 5: Integrity Checks (HARD GATE)

All of the following must pass for every historical period before proceeding to Phase 2:

1. **BS Check = $0** every historical year (Total Assets - Total L&E)
2. **CF Check = $0** every historical year (Beginning Cash + all CF - Ending Cash)
3. **NI ties IS→CFS**: Net Income on IS = Net Income starting the CFS
4. **RE roll-forward**: Beginning RE + NI - Dividends = Ending RE
5. **CF bridges BS**: For each historical year, Beginning BS + all CF flows ≈ Ending BS
6. **Reconciliation checks = $0**: Model View NI / Total Assets / Ending Cash = Annual Historicals reported values for every historical period

If reported CF flows don't perfectly reconcile to BS changes (common due to acquisitions, FX, reclassifications), document the residual as "Historical Reconciliation Difference" — but the structure must ensure that in projections the CFS will bridge the BS exactly.

**HARD GATE: If any check fails, STOP and fix it. Do not proceed to Phase 2 with a broken historical foundation.**

---

## Tab Completion Verification

Before reporting any tab complete (Annual Historicals, IS, BS, CFS, BS & CFS Build if lease schedules built), read and paste:

```
TAB VERIFICATION -- [Tab Name]:
  freezePanes: [null -- if not null, STOP and fix]
  gridlines: [off -- if on, STOP and fix]
  font: [Arial 10pt -- if not, STOP and fix]
  column widths uniform: [yes/no]
  tab color: [hex] -- expected: [hex]
  Annual Historicals only -- sections captured: [list of section numbers present]
  Annual Historicals only -- hardcode discipline: [all captured cells blue + source comment: yes/no]
  Statements only -- historical cells link to Annual Historicals: [yes/no]
  Reconciliation checks (all periods): [0 or values]
  BS Check (all periods): [0 or value]
  CF Check (all periods): [0 or value]
```

If you do not paste this output, the user cannot verify compliance. No output = not verified.

---

## Definition of Done (Phase 1)

A phase is complete if and only if ALL of the following are true. Report completion by reading these values back to the user -- not by summarizing in prose.

1. **Task Tracker**: Every subtask row for Phase 1 shows Status = "COMPLETE". Cite the actual cell addresses you checked.
2. **Annual Historicals captured**: every applicable section of the canonical hierarchy populated for all available years; sections not reported by the company omitted (list which); Change Log populated where edge-case protocols applied.
3. **Hardcode discipline**: every captured cell on Annual Historicals is blue text + source comment; zero hardcoded historicals exist on IS/BS/CFS.
4. **BS Check row = 0** for every historical period. Read and report the actual values.
5. **CF Check row = 0** for every historical period. Read and report the actual values.
6. **Reconciliation checks = 0** (Model View NI / Total Assets / Ending Cash = Annual Historicals reported) for every historical period.
7. **NI ties IS→CFS**: Read both cells and confirm they match.
8. **RE roll-forward**: Beginning RE + NI - Dividends = Ending RE for every period.
9. **BS→CF mapping** documented on the Task Tracker with every BS item assigned a CF home.
10. **Tab Completion Verification** output pasted for Annual Historicals, IS, BS, CFS, and BS & CFS Build (if lease schedules built).
11. **Task Tracker Model State Block**: "Last Skill Run" updated, "Next Skill" = "build-model-phase-2".

If you write "Phase 1 complete" in chat before reading and reporting these values, you have made an error. Re-verify and correct.

**STOP. Report status. Wait for "continue."**
