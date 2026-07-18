# Phase 3 — Driver Buildout

**Prerequisite:** Phase 2 complete (Quarterly Historicals captured, KPI Tracker built) and Phase 1 integrity checks passing (historical IS/BS/CFS verified, all checks = 0, BS→CF mapping done).

---

## What Happens in This Phase

Populate the model's two driver tabs — **Profit Build** and **BS & CFS Build** — with historical data and driver-based assumptions, and write the build-tab forward projections. The BS→CF mapping from Phase 1 is the blueprint — every BS item that was mapped to a build tab must appear here with an explicit driver.

Forward **statement** assembly (IS/BS/CFS projection columns) happens in Phase 4. This phase finishes the build tabs completely so Phase 4 is pure wiring.

### Profit Build — everything from revenue through EBIT, plus tax

Tier-2 sections, in order:

1. **Segment sections** — one Tier-2 section per reportable segment: volume/price drivers → segment revenue → segment costs → segment profit. Single-segment companies use two consolidated sections instead: "Revenue" and "Costs". See `meth-revenue.md` and `meth-cost.md`.
2. **Consolidated P&L Bridge** — Total Revenue (`=SUM()` of segment revenue rows), consolidated OpEx detail, EBITDA, less: D&A (green ref to the PP&E & Capex section of BS & CFS Build), EBIT.
3. **Tax** — effective tax rate, DTA/DTL schedule, NOLs, memo pre-tax income. See `meth-tax.md`. Built LAST (see build order).

### BS & CFS Build — every balance sheet driver and its CFS delta

**Canonical tab layout (physical top → bottom).** The tab reads backbone-first — the sections that drive the balance sheet and cash flow come first; the self-contained lease schedules are anchored at the bottom as supporting sub-schedules that the sections above reference down into:

1. **PP&E & Capex** — capex, D&A, net PP&E roll-forward. See `meth-ppe-build.md`.
2. **Goodwill & Intangibles** — goodwill carried flat, intangible amortization schedule (Beg − Amort + Additions = End), M&A Assets placeholder (0 until Phase 5). See `meth-goodwill.md`.
3. **Working Capital** — ALL operating BS items, current AND noncurrent, with explicit drivers. See `meth-working-capital.md`.
4. **Debt & Cash** — debt balances, roll-forward, interest expense, leverage metrics, cash target balance. See `meth-debt-build.md`.
5. **Operating Lease Schedule** (only if HAS_OPERATING_LEASES = Y). See `meth-debt-build.md`.
6. **Finance Lease Schedule** (only if HAS_FINANCE_LEASES = Y). See `meth-debt-build.md`.

**This is layout order, not build order.** The physical top-to-bottom sequence above is how the finished tab reads; the sequence you *compute* the sections in is the dependency-driven "Mandatory Build Order" below (Debt & Cash and leases get calculated early even though they sit lower on the tab). Don't conflate the two.

**Extensibility — additional sections discovered during the build.** The six above are the standard set, but many companies surface company- or industry-specific BS/CFS sections that need their own build section (e.g., equity-method / other long-term investments, pension & OPEB assets/liabilities, deferred revenue or contract-liability schedules, insurance float/reserves, a loan/lease receivable book, regulatory assets). Slot each new section into the layout by analytical category, keeping the backbone-first flow: **assets** (PP&E → Goodwill & Intangibles → other long-term assets) → **operating working capital** → **financing** (Debt & Cash) → **self-contained schedules** (leases, and any similar standalone sub-schedules) anchored at the bottom. Place a new section adjacent to its nearest analytical kin (a pension liability sits with the other long-term liabilities near Debt & Cash; an investment asset sits up with PP&E/Goodwill), and give any new *self-contained schedule* the same bottom-anchor treatment as the lease schedules so references keep pointing up into it. Every added section still needs the four mapping elements below (historical link, inline driver, projected balance, CFS delta).

Historical lease schedules were built in Phase 1 (anchored at the bottom of the tab). This phase adds their forward drivers (New OL/FL, implied life, rate — yellow bg + blue text) and forward roll-forward projections.

---

## Optional — Quarterly Build (near-term forecast)

**Off by default.** For most models the annual driver build above is sufficient. When the near-term quarterly path is itself the thesis (a product ramp, a margin-inflection quarter, a capex cycle, a guidance-vs-model debate), you can optionally model the business **quarter by quarter** for the near horizon — the **rest of the current fiscal year + the next full fiscal year** — and have the annual Profit Build's forecast columns **sum the quarters up**.

The default is an **EBIT-layer** build (quarterly segment revenue → segment P&L → Operating Income) on a dedicated steel-blue **`Quarterly Build`** tab that links its actuals from Quarterly Historicals and feeds the annual Profit Build forecast-window columns via `SUM`/revenue-weighted formulas. Before building, run the triage in `meth-quarterly-build.md` to decide whether the business *also* needs a quarterly balance-sheet/cash-flow schedule (the common one is accelerating **capex → D&A** timing, which additionally feeds the BS & CFS Build PP&E section) — for most businesses, EBIT-only suffices.

**If used, build the Quarterly Build tab in this phase, before finalizing the annual Profit Build forecast columns (they pull from it).** Full methodology, triage checklist, tab structure, tie-out checks, the annual-pull formulas, and the seam-continuity rule are in **`meth-quarterly-build.md`**.

---

## Mandatory Build Order (Cross-Tab Dependencies)

Work in this exact sequence. Each step depends on outputs of earlier steps:

1. **Profit Build — segment revenue sections** (drivers + projections)
2. **Profit Build — cost sections + Consolidated P&L Bridge through EBITDA** (capex and WC drivers reference revenue)
3. **BS & CFS Build — Debt & Cash** (balances, roll-forward, interest, leverage, cash target)
4. **BS & CFS Build — Operating Lease Schedule + Finance Lease Schedule** (self-contained: each schedule's projections depend only on its own drivers)
5. **BS & CFS Build — PP&E & Capex** (capex % needs revenue from step 2; if FL_IN_PPE = Y, the D&A driver and "Other" capex line need Finance Lease Schedule data from step 4)
6. **BS & CFS Build — Goodwill & Intangibles** (self-contained: amortization schedule from Annual Historicals + M&A Assets placeholder)
7. **BS & CFS Build — Working Capital** (ex-lease disaggregation needs lease schedule balances from step 4)
8. **Profit Build — Tax** (memo pre-tax income = EBIT − Total Interest Expense + Interest Income, if any; interest exists after step 3, EBIT after steps 5–6)

**No circularity:** Tax depends on interest; interest depends on debt balances only — never on tax. The dependency chain is a straight line.

---

## Mapping Requirement: Consult the BS→CF Map

Before building any section, consult the BS→CF mapping from Phase 1. Every BS item assigned to a build tab must appear on that tab with:
1. Historical values linked to Annual Historicals (green cross-sheet refs)
2. An explicit inline driver assumption (yellow bg + blue text)
3. A projected balance
4. A computed period-over-period change (delta) that the CFS will reference in Phase 4

**If a BS item is missing from its assigned section, add it.** No BS item may be left without a driver if it changes in projections.

---

## Working Capital Section: Comprehensive Scope

The Working Capital section of BS & CFS Build covers ALL operating BS items not owned by another section:

**Current operating items:** AR, Inventory, Prepaid, Other CA, AP, Accrued Liabilities, Deferred Revenue, Other CL

**Noncurrent operating items:** ARO, Operating Lease ROU Assets & Liabilities (via ex-lease disaggregation — the lease schedules own the lease balances), Other Noncurrent Assets, Other Noncurrent Liabilities, Pension/Postretirement, Deferred Tax Assets (if not in the Tax section)

**Explicitly excluded from WC scope** (owned by other sections): Goodwill, Intangibles, and the M&A Assets row (Goodwill & Intangibles section); lease schedule balances themselves; DTLs modeled in the Tax section.

Each item gets the 4-step treatment:
1. **Identify**: from the BS→CF mapping
2. **Select Driver**: DSO/DIO/DPO, % of revenue, % of COGS, % of OpEx, fixed dollar, or growth rate — whichever is most stable historically
3. **Backtest**: check ratio stability (CV >25% → flag and consider alternatives)
4. **Structure**: inline assumption → projected balance → period-over-period delta

**Two summary output rows** for the CFS:
- **Change in Working Capital (CFO):** net of all current item deltas
- **Noncurrent Operating Changes (CFO):** net of all noncurrent item deltas

**No BS item may use an embedded growth rate** (e.g., `=K35*1.02`) unless the same growth is captured via a visible driver assumption. The safe pattern: build tab has the driver → BS links to the build tab balance → CFS links to the build tab delta.

**Denominator Consistency Rule applies to every forward WC formula** — see `meth-working-capital.md`. The projection denominator must match the historical formula's denominator exactly.

---

## Tax Section: Outputs for IS and CFS

The Tax section of Profit Build must output:
1. **Income Tax Expense** → IS pulls this in Phase 4. Computed as `Memo Pre-Tax Income × Effective Tax Rate` where Memo Pre-Tax Income = EBIT (P&L Bridge) − Total Interest Expense + Interest Income, if any (Debt & Cash section, green refs).
2. **Net change in DTA/DTL** → CFS pulls this as a non-cash CFO adjustment. The driver for DTL growth must be a visible yellow/blue assumption cell, not embedded in a formula.
3. **Cash Taxes Paid** (memo) → useful for FCF sanity checks.

In Phase 4, the IS EBT must tie to the Memo Pre-Tax Income row — this is a validation check, not a coincidence.

---

## Key Rules for This Phase

- **Historicals link to Annual Historicals** (green cross-sheet refs) — never hardcoded, never linked to raw source tabs. Annual Historicals is the single capture layer.
- **Assumptions are inline** — same row as the metric, projection columns. Yellow bg + blue text + source comment.
- **Revenue is driver-based** — volume × price/rate strongly preferred.
- **PP&E acquisitions in projection years: set to 0 as a placeholder.** Phase 5 (Capital Allocation) will overwrite with a live link. Never back-solve acquisitions as a plug.
- **Non-contractual debt changes (optional prepayment, new issuance beyond scheduled amort) in projection years: set to 0 as a placeholder.** Phase 5 will incorporate final debt decisions.
- **Cash & Equivalents lives in the Debt & Cash section** as a target balance assumption (yellow bg + blue text in projections, green ref to BS for historicals). The change in cash feeds the Capital Allocation Build — see `meth-debt-build.md` for the full spec.
- **Both build tabs output balances (for BS) and deltas (for CFS)** — the statements pull from them in Phase 4.
- **Freeze panes at `B5`** on Profit Build, BS & CFS Build, and the optional Quarterly Build (Freeze Pane Standard).

---

## Tab Completion Verification

Before reporting either build tab complete, read and paste:

```
TAB VERIFICATION -- [Tab Name]:
  freezePanes: [expected value per the firm-formatting Freeze Pane Standard -- STOP and fix if wrong]
  gridlines: [off -- if on, STOP and fix]
  font: [Arial 10pt -- if not, STOP and fix]
  column widths uniform: [yes/no]
  tab color: [hex] -- expected: #5B8FA8
  sections present (in canonical order): [list]
  historicals linked to Annual Historicals (not hardcoded): [yes/no]
  assumptions: yellow bg + blue text + source comments: [yes/no]
```

If you do not paste this output, the user cannot verify compliance. No output = not verified.

---

## Driver Review Checkpoint (Phase 3.5) -- Mandatory Output

Do NOT "conduct a review in your head." Write the driver review into the Task Tracker using this exact table schema:

| Tab | Driver | FY_last_E Value | Confidence | Rationale | Flag/Action |

**One row per driver assumption.** Every yellow-bg/blue-text assumption cell on Profit Build, BS & CFS Build, and the Quarterly Build (when used) must appear as a row in this table — including lease schedule drivers and quarterly drivers. For quarterly drivers, one row per driver per fiscal year is sufficient (cite the quarter cells in the rationale) — do not omit them just because they are quarterly.

### Completeness Check

After populating the table:
1. Count yellow/blue assumption cells across both build tabs
2. Count rows in the driver review table
3. If rows < assumption cells, Phase 3 is not complete -- find the missing drivers and add them

### Confidence Gate

After populating, count LOW and MEDIUM confidence drivers:
- **If count >= 1**: STOP. List each LOW/MEDIUM driver with its tab, cell address, current value, and your rationale. Ask the analyst for explicit sign-off on each before proceeding.
- **If count = 0**: Report "All drivers HIGH confidence" and proceed.

Do NOT write "Driver review complete" without populating this table. The table IS the review.

---

## Definition of Done (Phase 3)

A phase is complete if and only if ALL of the following are true. Report completion by reading these values back to the user -- not by summarizing in prose.

1. **Task Tracker**: Every subtask row for Phase 3 shows Status = "COMPLETE". Cite the actual cell addresses you checked.
2. **Driver Review table populated** on the Task Tracker -- one row per driver assumption. Cite the cell range (e.g., "A166:F189 populated with [count] driver rows").
3. **Completeness check passed**: driver review row count = yellow/blue assumption cell count.
4. **LOW/MEDIUM drivers resolved**: either count = 0 or analyst sign-off obtained for each.
5. **Both build tabs fully populated**: every section required by the BS→CF map exists on Profit Build or BS & CFS Build with historicals, driver, projected balance, and delta.
6. **Tab Completion Verification** output pasted for both build tabs.
7. **Task Tracker Model State Block**: "Last Skill Run" updated, "Next Skill" = "build-model-phase-04".

If you write "Phase 3 complete" in chat before reading and reporting these values, you have made an error. Re-verify and correct.

**STOP. Report status. Wait for "continue."**
