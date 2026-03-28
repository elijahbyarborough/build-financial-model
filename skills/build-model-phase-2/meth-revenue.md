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
