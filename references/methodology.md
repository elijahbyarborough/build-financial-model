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
- Only adjust for impairments (if modeling a scenario) or new acquisitions (via M&A Build)

### Intangible Assets
- Build an amortization schedule: Beginning Balance - Amortization Expense + Additions = Ending Balance
- Amortization expense: straight-line over estimated useful life by asset category
- If the company discloses an amortization schedule in filings, use it directly
- Amortization flows to the IS (either as a separate line or within D&A, depending on company reporting)

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

### Blended Approach
- **Income Statement**: Apply an effective tax rate assumption to EBT
  - Effective tax rate: yellow background (#FFFF00) + blue text (#0000FF), source comment with historical average and statutory reference
  - `Income Tax Expense = EBT × Effective Tax Rate`
- **Balance Sheet**: Model DTAs and DTLs explicitly
  - Deferred Tax Assets: driven by tax loss carryforwards, SBC deductions, accruals timing
  - Deferred Tax Liabilities: driven by accelerated depreciation, intangible amortization timing
  - Net DTA/DTL change flows through the CF statement as a non-cash adjustment

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

### Debt = Input (Capital Allocation Decision)
Debt assumptions (contractual amortization, planned issuance, optional prepayment) live on the Debt Build tab. The Capital Allocation Build waterfall (Phase 4) incorporates net debt changes from the Debt Build as an input to the cash waterfall. The Debt Build owns the debt assumptions; the Cap Alloc waterfall passes them through.

---

## Share Count

**Share count logic lives on the Capital Allocation Build tab**, since buybacks are the primary driver and buyback dollars come from the capital allocation waterfall.

### Diluted Shares via Treasury Stock Method
1. **Basic shares**: Beginning shares - buyback shares = ending basic shares
2. **Diluted shares**: Basic shares + dilutive impact of options/RSUs via TSM
   - TSM: `Dilutive Shares = In-the-Money Options × (1 - Exercise Price / Current Stock Price)`
   - Current stock price: use the implied share price from the model (Exit Multiple × metric / shares) or market price for historical
3. **Buyback shares**: `Buyback Dollars / Buyback Price` (Buyback Price = Entry Price from the Returns tab)
4. **Diluted share count feeds back to IS** for EPS computation

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

**Every model must include ROIC and ROTIC, including incremental measures.** These are the core capital allocation quality metrics. Build them as a dedicated section on the Model tab (within or immediately after KPIs).

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

### Incremental ROIC

```
Incremental ROIC = ΔNOPAT / ΔInvested Capital
                 = (NOPAT_t - NOPAT_t-1) / (Invested Capital_t - Invested Capital_t-1)
```

Measures the marginal return on each new dollar invested. This is the most important capital allocation signal:
- **Trend**: rising incremental ROIC = improving reinvestment quality; declining = deteriorating

### Incremental ROTIC

```
Incremental ROTIC = ΔNOPAT / ΔTangible Invested Capital
```

Same logic as incremental ROIC but excludes changes in goodwill and intangibles. Useful for isolating organic capital efficiency from M&A-driven changes in the IC base.

### Presentation
- Show ROIC, ROTIC, Incremental ROIC, and Incremental ROTIC for all historical and projection years
- Format as percentage rows (italic, percentage format)

---

## FCF Yield Analysis

**Every model must include FCF yield metrics.** These connect the model's cash generation to the market price.

### Definitions

```
uFCF Yield = Unlevered FCF / Enterprise Value
Levered FCF Yield = Levered FCF / Market Cap (Equity Value)
FCFE Yield = FCFE / Market Cap (Equity Value)
```

### Implementation
- Compute all three yields for each historical and projection year
- **Enterprise Value and Market Cap**: for historical years, use actual market data (from Data Pull). For projection years, use the implied values from the model's exit multiple.
- Alternatively, for a "current yield" view: hold today's EV and market cap constant across all years to show how yields evolve if the stock price doesn't move. This answers "what yield am I buying at today's price?"

### Presentation
- Show as a dedicated block within the KPI section on the Model tab
- Format as percentage rows (italic, percentage format)
- Include both the "implied value" yields and the "current price" yields if space permits
- FCF yield trending up = improving cash generation relative to value (bullish signal for a long-only investor)
