# Projection Methodology

Detailed rules for building each component of the financial model. Load this reference when constructing or reviewing projections.

---

## Revenue Build

**This is the most important tab. Invest maximum effort.**

### Approach Hierarchy
1. **Unit economics / driver-based** (strongly preferred): volume × price/rate
   - Example: Equipment Rentals = Fleet Size × Utilization Rate × Average Monthly Rate × 12
   - Example: Subscriptions = Beginning Subscribers + Net Adds × ARPU
2. **Segment-level with identified drivers** (acceptable): revenue by business line with growth decomposed into volume and pricing
3. **Growth rate decomposition** (last resort): only when no driver data exists

### Rules
- Each revenue segment gets its own block with clearly labeled drivers
- Driver assumptions are yellow background (#FFFF00) + blue text (#0000FF) with source comments
- Build from the bottom up — total revenue should be a `=SUM()` of segments, never a hardcoded number
- Include year-over-year growth rates as italic percentage rows below each segment total
- If a company reports non-GAAP revenue adjustments, show both GAAP and adjusted

---

## Cost Build

### Operating Expenses
- Project major OpEx line items individually when disclosed (COGS, SG&A, R&D, etc.)
- Use margin-based projections (as % of revenue) for stable cost lines
- Use absolute dollar growth for fixed costs or costs with weak revenue correlation
- Always show implied margins as italic percentage rows

---

## PP&E Build

### Sign Convention (CRITICAL)
On the PP&E Build tab, use the **asset perspective** for all line items:
- **Capital Expenditures**: POSITIVE (adds to asset base)
- **Depreciation / Depletion / Amortization**: NEGATIVE (reduces asset base)
- **Acquisitions**: POSITIVE (adds to asset base) — pulled from Capital Allocation Build
- **Asset Disposals**: POSITIVE or NEGATIVE depending on direction

The CF tab negates capex when pulling it: `CF!Capex = -'PP&E Build'!Capex` to show it as a cash outflow. The PP&E Build itself always uses the asset perspective.

### Ending PP&E = Organic Roll-Forward (NEVER a Target Ratio)
Ending Net PP&E is ALWAYS the sum of its components:

```
Ending Net PP&E = Beginning Net PP&E + Capex - D&A + Acquisitions + Disposals/Other
```

NEVER set Ending PP&E = Revenue × target ratio and back-solve for acquisitions as a plug. That approach:
- Makes acquisitions a mechanical output instead of a capital allocation decision
- Can produce unrealistic acquisition volumes that exceed available cash
- Hides the true capital intensity of the business

### Capex
Split into two categories:
- **Maintenance capex**: sustaining existing asset base. Project as % of revenue or % of beginning gross PP&E.
- **Growth capex**: expansion, new capacity. Project based on management guidance or as % of revenue.

Both are POSITIVE values on this tab (asset additions).

### Depreciation
- Project as % of revenue or % of beginning Net PP&E — whichever produces more stable historical ratios
- Stored as NEGATIVE on this tab (asset reduction)
- **Separate depreciation schedule** at the asset-category or individual-asset level when granularity is available
- Use straight-line depreciation by default unless the company explicitly uses a different method
- For each asset category: Beginning Gross PP&E + Capex Additions - Disposals = Ending Gross PP&E
- Accumulated Depreciation: Beginning + Depreciation Expense - Depreciation on Disposed Assets = Ending
- Net PP&E = Gross PP&E - Accumulated Depreciation

### Acquisitions
- **Acquisitions do NOT originate on the PP&E Build.** They are a capital allocation decision.
- PP&E Build row for Acquisitions = pull from Capital Allocation Build (with sign flip if needed)
- Formula: `='Capital Allocation Build'!Acquisition Budget` (negated, since Cap Alloc stores it as negative cash outflow)
- The Capital Allocation Build owns the acquisition budget assumption

### Disposals / Retirements
- If the company has a history of asset disposals, model them separately
- Gain/loss on disposal flows to Other Income on the IS

### Reference Metrics
Include below the roll-forward:
- Capex as % of Revenue (= Capex / Revenue, both positive)
- D&A as % of Revenue
- D&A as % of Beginning Net PP&E
- Net PP&E Turnover (Revenue / Average Net PP&E)

---

## Goodwill & Intangibles

**Always model amortization schedules.**

### Goodwill
- Goodwill does not amortize under GAAP — carry forward at the most recent balance
- Only adjust for impairments (if modeling a scenario). In projections, acquisition spend flows through the M&A Assets line (see below), not Goodwill — Goodwill stays flat unless the analyst explicitly models a goodwill allocation.

### Intangible Assets
- Build an amortization schedule: Beginning Balance - Amortization Expense + Additions = Ending Balance
- Amortization expense: straight-line over estimated useful life by asset category
- If the company discloses an amortization schedule in filings, use it directly
- Amortization flows to the IS (either as a separate line or within D&A, depending on company reporting)

### M&A Assets (Required BS Row)
Every Balance Sheet must include an "M&A Assets" row in Noncurrent Assets, positioned after Other Noncurrent Assets and before Total Assets.

- **Historical periods**: 0 (or link to reported goodwill from acquisitions if available)
- **Projection periods**: Pull directly from the Capital Allocation Build's **Cumulative M&A Invested Capital** row:
  - `='Capital Allocation Build'!Cumulative_M&A_Invested_Capital` for each projection year
  - This is the running total of all acquisition spend (Phase 4, Section 3, Row 3)
- This line offsets the cash outflow from acquisitions on the CF/BS so the balance sheet stays balanced without touching Goodwill or the organic model
- **Total Assets SUM range must include this row**
- The Phase 4 Re-Link step explicitly maps: `BS!M&A Assets = Cap Alloc!Cumulative M&A Invested Capital`

---

## Lease Accounting (ASC 842)

Leases are projected on the **Debt Build** tab in dedicated schedule sections. Both operating and finance leases follow a roll-forward architecture that feeds the BS, IS, and CF through the Working Capital Build.

### Detection & Classification (Phase 0 / Phase 1)

When scanning source data (BAMSEC, Tegus, Historical Data), look for lease indicators before building any tabs.

**Balance Sheet signals**: "Operating Lease ROU", "Right-of-use", "Finance Lease", "Capital Lease", "ROU Asset"
**Income Statement signals**: "Operating lease cost", "Finance lease depreciation", "Finance lease interest", "Lease expense"
**Cash Flow signals**: "Finance lease payments", "Operating lease payments", "ROU asset amortization"

Set the following flags on the Task Tracker Model State Block:
- `HAS_OPERATING_LEASES`: Y/N
- `HAS_FINANCE_LEASES`: Y/N
- `FL_IN_PPE`: Y/N -- does the company include finance lease ROU assets inside reported Net PP&E?
- `LEASE_MATERIALITY`: HIGH/LOW -- HIGH if lease assets > 5% of total assets

**Materiality shortcut**: If `LEASE_MATERIALITY = LOW` and the portfolio is declining (no material new lease additions), use simplified straight-line runoff with no new additions instead of the full roll-forward architecture.

### Operating Lease Schedule

Built on the **Debt Build** tab in a dedicated "Operating Lease Schedule" section.

**Drivers (assumption rows, yellow bg + blue text)**:
- New Operating Leases (noncash ROU addition): flat dollar amount based on trailing 3-year average
- Implied Useful Life (years): based on historical implied life (prior liability / annual cost)

**ROU Asset roll-forward**:

Ending ROU = Beginning ROU + New Leases - Lease Cost

**Liability roll-forward**:

Ending Liability = Beginning Liability + New Leases - Lease Cost

Under ASC 842 for operating leases, the full lease cost (P&L expense) reduces the liability. There is no interest/principal split for operating leases.

**Current / Noncurrent split**: Formula-driven, never hardcoded in projections. Use one of:
1. `=MIN(Total_Liability, Next_Year_Lease_Cost)` for current portion
2. Historical current/total ratio applied to projected total

Noncurrent = Total - Current.

**IS linkage**: Operating lease cost flows to operating expenses (SG&A or a dedicated "Lease Expense" line). This is an operating expense, NOT interest.

**CF linkage**: The net change in operating lease balances (Change in ROU Asset - Change in Liability) flows through working capital on the CF statement. When new lease additions roughly equal amortization, this nets to approximately zero. No separate CF Financing entry for operating leases.

### Finance Lease Schedule

Built on the **Debt Build** tab in a dedicated "Finance Lease Schedule" section, below the Operating Lease Schedule.

**Drivers (assumption rows, yellow bg + blue text)**:
- New Finance Lease Additions: flat dollar amount (or zero if portfolio is winding down)
- Useful Life (years): for depreciation calculation
- Implicit Interest Rate: based on historical implied rate (interest expense / average liability)

**ROU Asset roll-forward**:

Ending Gross ROU = Beginning Gross ROU + New Additions
Accumulated Depreciation rolls forward with annual depreciation
Ending Net ROU = Ending Gross - Ending Accumulated Depreciation

**Depreciation**: `FL Depreciation = Beginning Net ROU / Useful Life` (straight-line). This is NOT the same as the cash payment.

**Interest**: `FL Interest = Beginning Liability * Implicit Rate`. Flows to IS interest expense.

**Total FL Cost (memo)**: `= Depreciation + Interest`. This equals the total cash lease payment.

**Liability roll-forward**:

Ending Liability = Beginning Liability + New Additions + Interest Accrual - Cash Payment

Where Cash Payment = Depreciation + Interest (total lease cost). The liability reduction each period equals the principal portion (= Depreciation), because Payment - Interest = Depreciation.

**Current / Noncurrent split**: Same rules as operating leases -- formula-driven, never hardcoded. Use `=MIN(Total_Liability, Next_Year_Principal_Payment)` or historical ratio.

**IS linkage**:
- FL Depreciation: included in D&A (either inside PP&E Build D&A if `FL_IN_PPE=Y`, or as a separate component)
- FL Interest: included in total interest expense

### Finance Lease ROU Asset Placement (FL_IN_PPE Decision)

**This decision determines the entire model structure for finance leases.** Check the company's reported balance sheet to determine whether finance lease ROU assets are bundled inside Net PP&E or reported as a separate line item.

**How to determine**: If the PP&E footnote or BS detail includes finance lease ROU assets within the PP&E gross/net roll-forward, then `FL_IN_PPE = Y`. If the BS shows a separate line for "Finance Lease ROU Assets", then `FL_IN_PPE = N`.

#### FL_IN_PPE = Y (finance lease ROU inside PP&E)

- **PP&E Build**: New FL additions flow through an "Other" capex line: `='Debt Build'!New_FL_Additions`. FL depreciation is embedded in total D&A.
- **D&A driver calculation (CRITICAL)**: The D&A-as-%-of-revenue driver must be calculated EXCLUDING finance lease depreciation. Otherwise the FL portion inflates the %, leading to over-projection of D&A. Historical driver formula: `=(Total D&A - FL Depreciation) / Revenue`. Projection D&A allocation formula must subtract FL depreciation before applying the segment/corporate split.
- **BS**: Single "Net PP&E" line that includes FL ROU. No separate BS line for FL ROU asset.
- **CF**: Total D&A add-back on CF already includes FL depreciation (it is inside PP&E Build D&A).

#### FL_IN_PPE = N (finance lease ROU separate from PP&E)

- **PP&E Build**: Clean -- no FL items at all. D&A is purely operating D&A. No complex allocation needed.
- **Debt Build**: Tracks the full FL ROU asset roll-forward (in addition to the liability roll).
- **BS**: New noncurrent asset line "Finance Lease ROU Assets, Net" pulling from `='Debt Build'!FL_ROU_Net`. Place between Net PP&E and Goodwill (or wherever the company reports it).
- **CF**: Total D&A add-back = PP&E Build D&A + Debt Build FL Depreciation (two separate pulls).

### Cash Flow Rules for Leases (HARD STOP)

**This is the most common lease modeling error. Read carefully.**

**CF Operating**:
- D&A add-back INCLUDES finance lease depreciation (it is a noncash charge)
- Working capital changes include the net change in operating lease balances (ROU - Liability)
- FL interest is already captured in Net Income, which flows to CFO -- no separate add-back needed

**CF Financing**:
- Finance lease payment in CF Financing = **PRINCIPAL ONLY** = the depreciation amount
- Pull from: `=-'Debt Build'!FL_Depreciation` (the depreciation row, NOT the total payment row)
- **NEVER** pull from the total payment row (Depreciation + Interest). The interest component is already inside Net Income, which flows through CFO. Pulling the total payment double-counts the interest as a cash outflow.

**Verification**: After building, confirm that `Total CF from Financing` includes only the FL principal (depreciation) amount, not dep + interest. If the BS check is non-zero by roughly the amount of FL interest expense, this is almost certainly the cause.

### Working Capital Build Disaggregation

The Working Capital Build is the bridge between the Debt Build (where leases are projected) and the BS/CF. Lease items MUST be disaggregated from generic "Other" balance sheet lines.

**Required rows in WC Build Noncurrent Operating Items section**:
- Operating Lease ROU Assets: pull from Debt Build OL ROU
- Operating Lease Liabilities: pull from Debt Build OL Liability (noncurrent or total, matching BS presentation)
- Finance Lease Liability -- Noncurrent: pull from Debt Build FL Noncurrent
- Finance Lease Liability -- Current: pull from Debt Build FL Current

**Historical formula pattern for disaggregation**:
- "Other Current Liabilities (ex-Finance Lease)" = `BS!Other_CL_as_reported - 'Debt Build'!FL_Current`
- "Other Noncurrent Liabilities (ex-Finance Lease, NCI)" = `BS!Other_NCL_as_reported - 'Debt Build'!FL_Noncurrent - BS!NCI`

This ensures the "Other" lines contain only the residual items that should be projected using revenue-based or flat-line drivers, while lease items are projected by the Debt Build schedule.

**Projection formula pattern**: In projection years, each lease row pulls directly from the Debt Build. The "Other" lines use their own drivers (% of revenue, flat, etc.) and do NOT include any lease amounts.

### Build Order (Updated for Leases)

When leases are present, the build order for Phase 3 (Forward Statements) is:

1. Revenue Build
2. Costs Build (operating expenses, including OL lease cost)
3. **Debt Build (including Operating Lease Schedule and Finance Lease Schedule)**
4. PP&E Build (needs FL additions and depreciation from Debt Build if `FL_IN_PPE=Y`)
5. Working Capital Build (needs lease balances from Debt Build)
6. Tax Schedule
7. Income Statement assembly
8. Balance Sheet assembly
9. Cash Flow assembly

The Debt Build must complete before the PP&E Build because PP&E needs:
- New FL additions (for "Other" capex if `FL_IN_PPE=Y`)
- FL depreciation (to exclude from the D&A% driver calculation)

---

## Working Capital

**Do NOT assume a fixed set of WC items or default to DSO/DIO/DPO.** The balance sheet is the source of truth. Use the 4-step discovery protocol below.

### 4-Step Discovery Protocol

1. **Identify**: Scan the BS for all current asset and current liability line items (excluding cash and current debt). These are the WC items to model.
2. **Select Driver**: For each item, pick the best-fit forecast driver from the table below — based on which produces the most stable historical ratio.
3. **Backtest**: Check ratio stability over available history. If a driver shows high variance (CV >25%), flag it and consider an alternative or qualitative adjustment.
4. **Structure**: One assumption row + one output row per WC item. Assumption row gets yellow background (#FFFF00) + blue text (#0000FF) + source comment. Output row computes the BS balance from the driver.

### Driver Types

| Driver Type | Formula | Best For |
|-------------|---------|----------|
| Days (DSO/DIO/DPO) | `Revenue (or COGS) / 365 × Days` | AR, Inventory, AP with stable turnover |
| % of Revenue | `Revenue × %` | Deferred revenue, prepaid expenses, accrued liabilities |
| % of COGS | `COGS × %` | Inventory, AP when COGS is the better denominator |
| % of OpEx | `Total OpEx × %` | Prepaid expenses, accrued compensation |
| Fixed Dollar | Hold flat or grow with inflation | Immaterial items with no clear revenue/cost linkage |
| Growth Rate | `Prior Balance × (1 + g)` | Items with consistent historical growth patterns |

### Common WC Items and Typical Drivers
These are common patterns, not prescriptive rules. Always verify against the actual BS:

| Item | Typical Driver | Notes |
|------|---------------|-------|
| Accounts Receivable | DSO | Most stable for companies with consistent billing cycles |
| Inventory | DIO | Tied to COGS, not revenue |
| Accounts Payable | DPO | Tied to COGS |
| Deferred Revenue | % of Revenue | Common for SaaS, subscription, and prepaid models |
| Prepaid Expenses | % of OpEx | Usually small, low volatility |
| Accrued Liabilities | % of Revenue | Catch-all for accrued comp, taxes, other |

### Methodology
1. Calculate historical ratios for each WC item using the selected driver
2. Project driver metrics: hold flat at recent average, or trend based on management commentary
3. Change in Working Capital on the CF statement = ending WC - beginning WC (increase in WC asset is a cash outflow, increase in WC liability is a cash inflow)

---

## Tax

### Default Approach: Effective Tax Rate (Most Companies)

Most companies can be modeled with a simple effective tax rate. This is the default unless the company has material NOLs or complex tax structures.

- **Income Statement**: Apply an effective tax rate assumption to EBT
  - Effective tax rate: yellow background (#FFFF00) + blue text (#0000FF), source comment with historical average and statutory reference
  - `Income Tax Expense = EBT × Effective Tax Rate`
  - Default: use the trailing 3-year average effective rate unless management guides otherwise
  - If EBT is negative, set tax expense to 0 (do not model a tax benefit unless there is a clear DTA to support it)
- **Balance Sheet**: Model DTAs and DTLs on the Tax Schedule tab
  - Deferred Tax Assets: driven by tax loss carryforwards, SBC deductions, accruals timing
  - Deferred Tax Liabilities: driven by accelerated depreciation, intangible amortization timing
  - Net DTA/DTL change flows through the CF statement as a non-cash CFO adjustment
  - For most companies, project net DTL as a % of PP&E or as flat-to-growing based on historical trend

### NOL Companies (When Applicable)

When a company has material Net Operating Loss carryforwards (check the tax footnote), the Tax Schedule needs additional rows:

- **Beginning NOL Balance**: sourced from the most recent 10-K tax footnote
- **NOL Utilization**: `=MIN(NOL Balance, EBT × Section 382 Limit %)` — NOLs offset taxable income up to the Section 382 annual limit (if applicable) or 80% of taxable income post-TCJA
- **Ending NOL Balance**: `=Beginning - Utilization`
- **Cash Tax Rate**: `=MAX(0, (EBT - NOL Utilization) × Statutory Rate) / EBT` — the actual cash tax paid after NOL shielding
- **GAAP Tax Rate**: Use the effective rate for IS presentation (GAAP requires full provision regardless of NOL usage). The difference between GAAP and cash tax creates the DTA drawdown.

The key output: **DTA drawdown** = NOL Utilization × Statutory Rate. This is a non-cash charge that flows through CFO as a negative adjustment (DTA decrease = cash source).

### Tax Schedule Outputs

The Tax Schedule tab must output these rows for other tabs to reference:
1. **Income Tax Expense** → IS pulls this
2. **Net change in DTA/DTL** → CF pulls this as a CFO non-cash adjustment
3. **Cash Taxes Paid** (memo) → useful for FCF sanity checks

---

## Interest Expense & Debt

### Debt Build (Tranche-by-Tranche)
For each debt instrument:
- Beginning Balance + Draws - Repayments = Ending Balance
- Interest Expense = Average Balance × Interest Rate (or Beginning Balance × Rate if contractually specified)
- Track maturity dates, mandatory amortization, optional prepayment
- Revolver: modeled as plug for minimum cash balance or set to zero with excess cash sweep

### Interest Rate Assumptions
- Fixed-rate debt: use the contractual rate
- Floating-rate debt: base rate assumption (SOFR) + spread. Base rate assumption cell: yellow background (#FFFF00) + blue text (#0000FF) + source comment.
- Interest income on cash balances: separate line, use conservative money market rate

### Cash & Equivalents
Cash & Equivalents lives on the Debt Build alongside the debt tranches. Historical periods pull from the BS tab (green text). Projection periods are a **hardcoded target balance assumption** (yellow bg + blue text) -- the analyst sets the target cash balance directly, just like debt balances.

The change in cash flows into the Capital Allocation Build as a source/use of cash:
- **Capital Allocation Build**: `Net Change in Cash = -(Target Cash - Prior Period Cash)`
- **Sign convention**: cash increase = negative (use of cash, reduces Cash Available for Allocation); cash decrease = positive (source of cash)
- Net Change in Cash sits in the **Cash Available for Allocation** section, NOT in Capital Deployment
- The BS Cash line pulls FROM the Debt Build (`='Debt Build'!cell`), not from the CF tab

This ensures the buyback plug on the Capital Allocation Build automatically adjusts when the analyst changes the target cash balance.

### Debt = Input (Capital Allocation Decision)
Debt assumptions (contractual amortization, planned issuance, optional prepayment) live on the Debt Build tab. The Capital Allocation Build waterfall (Phase 4) incorporates net debt changes from the Debt Build as an input to the cash waterfall. The Debt Build owns the debt assumptions; the Cap Alloc waterfall passes them through.

---

## Share Count

**Share count logic lives on the Capital Allocation Build tab**, since buybacks are the primary driver and buyback dollars come from the capital allocation waterfall.

### Diluted Shares via Treasury Stock Method
1. **Basic shares**: Beginning shares - buyback shares + SBC dilution = ending basic shares
2. **Diluted shares**: Basic shares + dilutive impact of options/RSUs via TSM
   - TSM: `Dilutive Shares = In-the-Money Options × (1 - Exercise Price / Current Stock Price)`
   - Current stock price: use market price for historical
3. **Buyback shares**: `Buyback Dollars / Buyback Price` (Buyback Price is a hardcoded assumption on the Capital Allocation Build, separate from the investor's Entry Price on the Returns tab)
4. **SBC Dilution**: `SBC Dollars / Buyback Price` — net share dilution from stock-based compensation at the assumed share price
5. **Diluted share count feeds back to IS** for EPS computation

---

## Projection Length

**7 dynamic projection years** (5yr hold + NTM exit window).

### Calculation
1. Start date = today
2. Exit date = today + 5 years
3. NTM window at exit = 12 months from exit date
4. Required fiscal years = all FYs contributing to NTM window + current FY

**Example** (Dec FYE, model built March 2026): CY2026E through CY2032E = 7 projection years.

**Rule**: When in doubt, add one more projection year. It's cheaper to project an extra year than to rebuild the model later.

### Historical Periods
As many years of actuals as the data supports. No fixed minimum. More history = better driver calibration.

---

## ROIC & ROTIC Analysis

**Every model must include ROIC and ROTIC.** These are the core capital allocation quality metrics. Build them as a dedicated section on the Model tab.

### ROIC (Return on Invested Capital)

```
NOPAT = EBIT × (1 - Effective Tax Rate)
Invested Capital = Total Equity + Total Debt - Cash & Equivalents
                 = Net PP&E + Net Working Capital + Goodwill + Net Intangibles + Other LT Operating Assets - Other LT Operating Liabilities
ROIC = NOPAT / Average Invested Capital
```

**Use average invested capital** (beginning + ending / 2) to match the flow metric (NOPAT) with a point-in-time metric (IC). Never use ending IC alone.

### ROTIC (Return on Tangible Invested Capital)

```
Tangible Invested Capital = Invested Capital - Goodwill - Net Intangibles
ROTIC = NOPAT / Average Tangible Invested Capital
```

ROTIC isolates the return on the company's physical and working capital — strips out acquisition-driven goodwill and intangibles to reveal the underlying asset productivity.

### Presentation
- Show ROIC and ROTIC for all historical and projection years
- Format as percentage rows (italic, percentage format)

