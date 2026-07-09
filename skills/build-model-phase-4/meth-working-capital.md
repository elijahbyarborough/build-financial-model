<!-- CANONICAL COPY: build-model-phase-3/meth-working-capital.md — sync edits there first -->

## BS & CFS Build — Working Capital Section

**Do NOT assume a fixed set of WC items or default to DSO/DIO/DPO.** The balance sheet is the source of truth. Use the 4-step discovery protocol below. Working capital lives in the Working Capital section (Tier-2 header) of the BS & CFS Build tab.

### 4-Step Discovery Protocol

1. **Identify**: Scan the BS for all operating items — current assets and current liabilities (excluding cash and current debt) AND noncurrent operating items (ARO, pension/postretirement, other operating NCA/NCL). These are the WC items to model. Items owned by other sections are excluded: lease balances (lease schedules, via ex-lease disaggregation below), goodwill/intangibles/M&A Assets (Goodwill & Intangibles section), DTAs/DTLs (Tax section).
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

### Denominator Consistency Rule

Before writing any forward WC formula, inspect the historical calculation formula for that line item to identify which denominator it uses (Revenue, COGS, Segment OI, etc.). The forward projection formula must use the identical denominator. Common mappings:

- **AR / DSO**: typically Revenue
- **AP / DPO**: may use COGS, Segment OI, or Revenue — check the historical formula
- **Other CA / CL % items**: check whether the historical driver references Revenue or another line

Do not assume all WC items are driven off Revenue. A denominator mismatch (e.g., projecting AP off Revenue when historicals compute DPO off Segment OI) will silently distort NWC as % of Revenue by 2–5x.

### Ex-Lease Disaggregation

When the Lease BS Map shows a lease component embedded inside a broader reported BS line (e.g., finance lease liabilities inside "Other Noncurrent Liabilities"), the Working Capital section models the **ex-lease** version of that line:

- Historical rows (built in Phase 1): `[Reported line] (ex-[component]) = reported balance − lease schedule component balance` (same tab)
- Projections: drive the ex-lease balance with a WC driver; the lease component is projected by its lease schedule
- The BS line in Phase 4 recombines them: `= ex-lease projection + lease schedule component`
- NEVER project an embedded lease balance with a revenue-based WC driver — lease balances follow their schedules

Every Lease BS Map component gets its own Working Capital section row either way (per `meth-lease-full.md`): a **direct reference** to the lease schedule when the component has its own dedicated BS line (no ex-lease math needed), or the **ex-lease disaggregation pair** above when it is embedded in a broader line. Do not skip the WC row for dedicated-line components — the BS wires through this section in both cases.

### Methodology
1. Calculate historical ratios for each WC item using the selected driver (historical balances link to Annual Historicals — green cross-sheet refs)
2. Project driver metrics: hold flat at recent average, or trend based on management commentary
3. Change in Working Capital on the CF statement = ending WC - beginning WC (increase in WC asset is a cash outflow, increase in WC liability is a cash inflow)

### CFS Summary Outputs (Required)

The section must end with two summary rows that the CFS references in Phase 4:

1. **Change in Working Capital (CFO)** — net of all current item deltas
2. **Noncurrent Operating Changes (CFO)** — net of all noncurrent item deltas. Includes the operating lease net change (ΔOL ROU − ΔOL liability, zero in projections by construction); EXCLUDES finance lease liability changes — FL principal flows through CFF, and new FL additions are non-cash.
