<!-- CANONICAL: this file is canonical in build-model-phase-03. Copies elsewhere sync FROM here. -->

## BS & CFS Build — PP&E & Capex Section

The PP&E & Capex section (Tier-2 header on the BS & CFS Build tab) must have exactly 3 sub-blocks, each with a bold-italic sub-header (sub-block header style registered in firm-formatting). Build this section AFTER the Finance Lease Schedule when FL_IN_PPE = Y (the D&A driver and "Other" capex line depend on it).

### Sub-Block 1: Capital Expenditures

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Capital Expenditures | $ amount, bold |
| 2 | Capex as % of Revenue | Italic, derived |
| 3 | Revenue | Green font, reference line from the Profit Build Consolidated P&L Bridge |

For companies with segment capex reporting: break out by segment with a controlling total = Revenue × Capex%. For single-segment or companies that don't disclose segment capex: single Capex line with blue/yellow for projection assumptions.

Both capex categories are POSITIVE values in this section (asset additions). The CFS tab negates capex when pulling it: `CFS!Capex = -'BS & CFS Build'!Capex` to show it as a cash outflow.

**If FL_IN_PPE = Y**: add an "Other (Finance Lease Additions)" capex row = `New Finance Leases` from the Finance Lease Schedule (same tab, black formula). These are non-cash additions — the CFS must NOT include them in cash capex.

### Sub-Block 2: Depreciation & Amortization

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Depreciation & Amortization | $ amount, bold |
| 2 | D&A as % of Avg PP&E | Italic, driver assumption — blue/yellow for projections |
| 3 | D&A as % of Revenue | Italic, derived |

**Projection formula**: `D&A = rate × (2 × BegPPE + Capex) / (2 + rate)` where rate = D&A % of Avg PP&E. This solves the circular reference between D&A and Ending PP&E algebraically.

**Sign convention (explicit)**: the projection formula above yields a POSITIVE value. D&A is stored as NEGATIVE in this section (asset reduction) — so the D&A $ row is the formula negated: `=-(rate × (2 × BegPPE + Capex) / (2 + rate))`. The roll-forward's "Less: D&A" row references the stored (already negative) value directly, and the IS/CFS apply their own sign handling when they pull it.

**Approximation caveat**: the algebraic solution assumes the roll-forward base is Beginning PP&E + Capex only. Once acquisitions (Phase 5 live link) or FL additions enter the roll-forward, projected D&A is a slight approximation (the formula's base excludes those additions). This is acceptable — note it, don't chase precision.

**If FL_IN_PPE = Y**: the historical D&A % of Avg PP&E driver must EXCLUDE finance lease depreciation — compute it as `(Total D&A − FL Depreciation) / Avg PP&E` — otherwise projected D&A is overstated. Add a "Finance Lease Depreciation" line below the main D&A (= FL Schedule depreciation row, same tab), and project Total D&A = operating D&A (from the % driver) + FL Depreciation.

### Sub-Block 3: Net PP&E Roll-Forward

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Beginning Net PP&E | `=Prior year Ending` |
| 2 | Plus: Capex | `=Sub-block 1 total` |
| 3 | Less: D&A | `=Sub-block 2 total` (shown as negative) |
| 4 | Other (Acquisitions, Disposals, Impairments) | Historical: back-solved from BS; projections: link to Capital Allocation Build acquisitions |
| 5 | **Ending Net PP&E** | Bold, light gray #F2F2F2 fill, thin top border. Historical: `=BS!col`; projections: sum of roll-forward components |

### Cross-References

- IS tab D&A line → PP&E & Capex sub-block 2 D&A $ amount
- BS tab PP&E line → PP&E & Capex sub-block 3 Ending Net PP&E
- CFS tab Capex line → PP&E & Capex sub-block 1 Capex (sign-flipped for CFS convention, excluding non-cash FL additions)

### Key Rules

- Historical capex and D&A link to Annual Historicals (green cross-sheet refs); Beginning/Ending PP&E historicals reference the BS.
- Ending Net PP&E is ALWAYS the sum of its components (organic roll-forward). NEVER set Ending PP&E = Revenue × target ratio and back-solve for acquisitions as a plug.
- Acquisitions do NOT originate in this section. They are a capital allocation decision. The Acquisitions row = pull from Capital Allocation Build (with sign flip if needed).
- PP&E acquisitions in projection years: set to 0 as a placeholder in Phase 3. Phase 5 (Capital Allocation) will overwrite with a live link.
