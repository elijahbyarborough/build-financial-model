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
