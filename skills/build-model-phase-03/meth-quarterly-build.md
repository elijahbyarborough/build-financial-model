<!-- CANONICAL: this file is canonical in build-model-phase-03. Copies elsewhere sync FROM here. -->

## Optional — Quarterly Build (feeds the annual Profit Build)

An **optional** quarterly operating build for the near-term forecast. Instead of driving the first forecast years with annual assumptions, you model the business **quarter by quarter** for the near horizon, then the annual Profit Build's forecast columns **sum the quarters up**. This gives granular, seasonally-aware control over the years that matter most to the thesis and to consensus, while the outer years keep their annual drivers.

**Default scope: the EBIT layer.** Quarterly segment revenue → segment P&L → Operating Income. Everything below EBIT (interest, tax, capital allocation, the full 3-statement) stays annual on the normal tabs. For most businesses that is enough — see the triage below for when to extend.

**Default horizon:** the **rest of the current fiscal year + the next full fiscal year** (extend one more FY when the thesis hangs on a longer near-term ramp). Quarters beyond the horizon are not built; those FYs revert to the annual Profit Build's own driver logic.

This module is **off by default.** Build it only when the analyst asks for a quarterly build, or when the near-term quarterly path is itself the thesis (a product ramp, a margin-inflection quarter, a capex cycle, a guidance-vs-model quarter). A standard annual build never needs it.

---

### Triage — does this business also need quarterly Balance Sheet / Cash Flow elements?

The default quarterly build stops at EBIT. Before building, decide whether the business has **balance-sheet or cash-flow dynamics whose *timing* materially changes the answer** — if so, extend the quarterly build to carry the relevant schedule (the common one, capex → D&A, is specced below). Run this checklist; if none apply, build EBIT-only.

| Trigger | Why quarterly timing matters | What to add |
|---------|------------------------------|-------------|
| **Accelerating / lumpy capex that flows into D&A on a lag** (hyperscaler & AI datacenter build-out, capex supercycle, fleet refresh) | Annual "capex % of revenue" misses the ramp, and D&A lags asset placement by quarters. Quarterly capex → asset-class roll-forward → quarterly depreciation captures both. *(This is the Microsoft/AI-capex case.)* | Quarterly PP&E & D&A schedule — see **Optional extension: quarterly capex → D&A** below |
| **The balance sheet IS the earnings engine** (banks, lenders, insurers, BDCs, other financials) | NII/float/spread income is driven by period-average balances, and reserve/credit timing swings quarterly earnings. An annual average smears it. | Quarterly earning-asset / liability / reserve roll-forward feeding the revenue and cost lines |
| **Deferred revenue / RPO / backlog conversion timing** (large-deal software, subscription ramps, construction %-complete, defense) | Near-term recognized revenue is a function of *when* bookings convert, not an annual growth rate. | Quarterly bookings → deferred-revenue/backlog roll → recognized revenue |
| **Strongly seasonal or one-off working-capital swing** (inventory build/unwind, receivables seasonality) that moves quarterly FCF | Only matters if you are taking the quarterly build below EBIT to FCF; annual WC drivers hide the intra-year swing. | Quarterly WC schedule (only if extending to cash) |
| **Debt paydown / refi / rate-reset timing** where quarterly interest is material | Interest on a rapidly changing debt balance or a mid-year refinancing is mis-stated by an annual average. | Quarterly debt roll + interest (only if extending below EBIT) |
| **Other "weird cash-flow timing"** — milestone payments, hedges rolling off, contingent consideration, tax true-ups | Anything where the quarter of the cash/expense event changes the near-term picture. | Model the specific schedule quarterly; roll it into the matching annual build section |

**Rule of thumb:** if the only thing you need quarterly is a granular revenue-and-margin path, build EBIT-only. Add a quarterly BS/CFS schedule **only for the specific driver that has real timing sensitivity** — never build a full quarterly 3-statement "because you can." Each added schedule follows the same pattern as the EBIT layer: quarterly roll-forward on the Quarterly Build tab → the matching annual build section sums it into the forecast columns.

---

### Tab setup

- **New tab, named exactly `Quarterly Build`** — one canonical name regardless of scope, so the QC gates, tab-order checks, and Freeze Pane Standard (which all look for "Quarterly Build") always match. It is a **first-class build tab**, not a scratch tab: Tab color **steel blue `#5B8FA8`**, positioned immediately right of the **Profit Build** (matching the firm tab order in Phases 8–9). Because the annual model *links to it*, it must not be a gray source tab — gray tabs are never live-link targets (build-model core rules).
- **Standard Tab Header**, but with **fiscal-quarter** columns: row 1 title, row 2 units, row 3 `FQ1 2023A … FQ4 2027E` labels (Year Label Convention, text `@`), row 4 quarter-end dates (`MM/DD/YY`). **Freeze panes at `B5`** and gridlines off per the Freeze Pane Standard.
- **Column coverage:** **2–3 full fiscal years of actual quarters** (8–12 quarters) before the forward horizon — the YoY driver logic references the *same quarter one year prior*, and you need at least two prior-year readings of each quarter to see its seasonality rather than a single point. The hard floor is 5 actual quarters (one YoY comparison per forward quarter); treat that as an emergency minimum, not the default. Forward columns: rest of current FY + next full FY. Actual and forecast quarters labeled `A` / `E` per the Year Label Convention.

### Historicals: link, never hardcode

- **Actual quarters link to the Quarterly Historicals tab** (green cross-sheet refs). Quarterly Historicals is the single hardcode layer; the Quarterly Build is a driver tab like any other and must not re-hardcode reported quarters.
- **Sub-segment / finer-grained revenue** than the reportable segments (e.g., product-line splits under a segment) belongs on **Quarterly Historicals first** (capture-everything), then link here. Do **not** link the Quarterly Build to a raw source/scratch tab — that forks the capture layer and will fail the Phase 12 source-hygiene scan.
- Derived analytics on this tab (growth %, margins, implied ARPU, bps changes) are black formulas as usual.

### Section structure (EBIT layer) — mirror the annual Profit Build

One Tier-2 section per reportable segment (single-segment companies use one consolidated block), then a consolidated roll-up. Per segment:

1. **Segment revenue with drivers.** Forward quarter is built off the **same quarter one year prior**, grown by a year-over-year driver:

   ```
   Fwd quarter revenue = PY-same-quarter revenue × (1 + YoY growth)
   ```

   Decompose the YoY growth into unit/price drivers wherever disclosed — the same driver hierarchy as the annual build (unit economics > segment-with-drivers > growth decomposition):

   ```
   YoY growth = (1 + unit/volume growth) × (1 + price/ARPU growth) − 1
   ```

   e.g. seats × ARPU, subscribers × ARPU, volume × price. The unit and price growth rows are the **yellow-bg/blue-text assumptions** (source comments); revenue and total growth are formulas.

2. **Segment P&L** (each with its YoY growth %, margin %, YoY bps change, and — where useful — incremental margin as italic analytical rows):
   - Gross Profit / Gross Margin %
   - COGS — forward = `revenue × COGS%`, where `COGS% = PY-same-quarter COGS% ± a bps assumption`
   - Operating Expenses — forward = `PY-same-quarter Opex × (1 + growth)`, or `revenue × Opex%`
   - **Operating Income** (Major Total) / Operating Margin %

3. **Consolidated roll-up:** Total Revenue = `SUM(segment revenues)`, Total GP, Total COGS, Total Opex, **Total Operating Income**, with consolidated growth and margin rows.

4. **Tie-out checks (required).** For every **actual** quarter, a check row proving the build ties to the reported record:
   ```
   Check: Revenue = Quarterly Build total revenue − 'Quarterly Historicals'!<reported revenue> = 0
   Check: Operating Income = Quarterly Build total OI − 'Quarterly Historicals'!<reported OI> = 0
   ```
   Non-zero → a capture error, a mis-mapped driver, or a segment recast not yet ingested. Resolve before wiring the annual pull.

### The feed into the annual Profit Build (the whole point)

For each **forecast FY column that falls inside the quarterly horizon**, the annual Profit Build stops using its own annual assumptions and instead **sums the four quarters** from the Quarterly Build:

| Annual row type | Annual Profit Build forecast-column formula |
|-----------------|---------------------------------------------|
| **Flow items** — revenue, GP, COGS, Opex, D&A, Operating Income | `=SUM('Quarterly Build'!<Q1:Q4 of that FY>)` |
| **Margins & ratios** — GM %, Op margin % | Recompute from the summed flows: `= annual GP / annual revenue` (cleanest — no drift) |
| **Driver rates you want to preserve** — a blended YoY growth, a weighted ASP | Revenue-weighted: `=SUMPRODUCT(rate range, revenue range) / SUM(revenue range)` |

These annual forecast cells are now **green pulls** from the Quarterly Build; the driver assumptions for the quarterly-window years live on the Quarterly Build (at quarterly granularity), not on the annual Profit Build.

**Handoff to the annual-only years (continuity — do not skip).** The first FY *beyond* the quarterly horizon reverts to the annual Profit Build's own growth drivers, and its growth must be anchored to the **quarterly-built terminal year** as its base (`= annual driver × quarterly-built FY total`). Read across the seam once: revenue growth, margins, and Op income should transition smoothly from the last quarterly-built year to the first annual-only year — no step-change, no double-count.

### Optional extension: quarterly capex → D&A (the common one)

When the triage flags capex-into-D&A timing, add a **quarterly PP&E, capex & depreciation schedule** to the Quarterly Build (this is what the Microsoft reference does):

- **Capital additions** — owned cash capex + finance-lease ROU additions (blue/yellow quarterly assumptions), split by asset class where disclosed.
- **Asset-class roll-forwards** — Gross PP&E by class = `prior + additions`; accumulated depreciation rolls with quarterly depreciation.
- **Useful-life assumptions** per class → **quarterly depreciation** = `average(class gross) / (life-in-years × 4)`.
- **Segment allocation** — depreciation and capex allocated to operating segments (editable % of segment revenue, with one residual segment) so the D&A feeds each segment P&L above.
- **Reconciliation checks** — segment D&A sum = total D&A; class totals tie to reported gross/net PP&E on Quarterly Historicals.

**Feed into the annual BS & CFS Build** (PP&E & Capex section, forecast-window columns): capex = `SUM(4 quarters)`, D&A = `SUM(4 quarters)`, ending Net PP&E = the **terminal quarter's** ending balance. The annual roll-forward (`meth-ppe-build.md`) is unchanged in structure — its forecast-window inputs just come from the summed quarters instead of annual driver rows. Finance-lease mechanics still follow `meth-lease-full.md` (principal-only CFF payment, D&A add-back rules).

Any *other* extension from the triage (financials' earning-asset roll, deferred-revenue conversion, quarterly WC/debt) follows the identical shape: build the roll-forward quarterly here, sum it into the matching annual build section's forecast-window columns.

### Build order & placement within Phase 3

The quarterly build is done **inside Phase 3**, and it must be complete **before** the annual Profit Build forecast columns are finalized (they pull from it). Sequence:

1. Establish the segment revenue/cost structure (as normal for Phase 3).
2. Build the **Quarterly Build** tab for the near-term window (revenue drivers → segment P&L → OI; add the capex→D&A schedule if triaged in).
3. Run the quarterly **tie-out checks** (= 0 for all actual quarters).
4. Wire the annual **Profit Build** forecast-window columns to `SUM`/weighted-avg the quarters; anchor the first annual-only year to the quarterly terminal year.
5. If extended: wire the annual **BS & CFS Build** forecast-window columns to the quarterly capex/D&A schedule.
6. Everything below EBIT (interest, tax, capital allocation, statements) proceeds annually per the normal Phase 3–5 flow.

### Definition-of-Done additions (when the quarterly build is used)

- Quarterly Build tab exists, steel-blue, frozen at `B5`, actuals linked to Quarterly Historicals (spot-check 3 cells green, none hardcoded).
- All actual-quarter **tie-out checks = 0** (read back).
- Annual Profit Build forecast-window columns = `SUM`/weighted-avg of the quarters (spot-check revenue and OI for one forecast FY).
- **Seam continuity:** first annual-only FY anchored to the quarterly terminal year; no growth/margin step-change across the boundary.
- If extended: annual BS & CFS Build capex/D&A forecast-window columns tie to the summed quarters; BS Check and CF Check still = 0 all periods.
