<!-- CANONICAL: this file is canonical in build-model-phase-03. Copies elsewhere sync FROM here. -->

## Profit Build — Revenue Sections

**Revenue is the most important part of the model. Invest maximum effort.**

Revenue lives on the **Profit Build** tab: one Tier-2 section per reportable segment (volume/price drivers → segment revenue), or a single consolidated "Revenue" section for single-segment companies.

### Approach Hierarchy
1. **Unit economics / driver-based** (strongly preferred): volume × price/rate
   - Example: Equipment Rentals = Fleet Size × Utilization Rate × Average Monthly Rate × 12
   - Example: Subscriptions = Beginning Subscribers + Net Adds × ARPU
2. **Segment-level with identified drivers** (acceptable): revenue by business line with growth decomposed into volume and pricing
3. **Growth rate decomposition** (last resort): only when no driver data exists

### Rules
- Each revenue segment gets its own Tier-2 section with clearly labeled drivers
- Driver assumptions are yellow background (#FFFF00) + blue text (#0000FF) with source comments
- Historical driver and revenue values link to Annual Historicals (green cross-sheet refs) — segment revenue history lives in its Segment Data section, volume/pricing history in its Operational/KPI Data section
- Build from the bottom up — Total Revenue on the Consolidated P&L Bridge is a `=SUM()` of the segment revenue rows, never a hardcoded number
- Include year-over-year growth rates as italic percentage rows below each segment total
- If a company reports non-GAAP revenue adjustments, show both GAAP and adjusted
