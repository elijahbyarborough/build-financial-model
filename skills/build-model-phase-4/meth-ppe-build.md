<!-- CANONICAL COPY: build-model-phase-2/meth-ppe-build.md — sync edits there first -->

## PP&E Build — 3-Section Structure

PP&E Build must have exactly 3 sections, each with a Tier-2 header.

### Section 1: Capital Expenditures

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Capital Expenditures | $ amount, bold |
| 2 | Capex as % of Revenue | Italic, derived |
| 3 | Revenue from Consolidation | Green font, reference line |

For companies with segment capex reporting: break out by segment with a controlling total = Revenue × Capex%. For single-segment or companies that don't disclose segment capex: single Capex line with blue/yellow for projection assumptions.

Both capex categories are POSITIVE values on this tab (asset additions). The CFS tab negates capex when pulling it: `CFS!Capex = -'PP&E Build'!Capex` to show it as a cash outflow.

### Section 2: Depreciation & Amortization

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Depreciation & Amortization | $ amount, bold |
| 2 | D&A as % of Avg PP&E | Italic, driver assumption — blue/yellow for projections |
| 3 | D&A as % of Revenue | Italic, derived |

**Projection formula**: `D&A = rate × (2 × BegPPE + Capex) / (2 + rate)` where rate = D&A % of Avg PP&E. This solves the circular reference between D&A and Ending PP&E algebraically.

For companies with finance leases: add a "Finance Lease Depreciation" line below the main D&A if FL depreciation is material and separately disclosed.

D&A is stored as NEGATIVE on this tab (asset reduction).

### Section 3: Net PP&E Roll-Forward

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Beginning Net PP&E | `=Prior year Ending` |
| 2 | Plus: Capex | `=Section 1 total` |
| 3 | Less: D&A | `=Section 2 total` (shown as negative) |
| 4 | Other (Acquisitions, Disposals, Impairments) | Historical: back-solved from BS; projections: link to Capital Allocation Build acquisitions |
| 5 | **Ending Net PP&E** | Bold, light gray #F2F2F2 fill, thin top border. Historical: `=BS!col`; projections: sum of roll-forward components |

### Cross-References

- IS tab D&A line → PP&E Build Section 2 D&A $ amount
- BS tab PP&E line → PP&E Build Section 3 Ending Net PP&E
- CFS tab Capex line → PP&E Build Section 1 Capex (sign-flipped for CFS convention)

### Key Rules

- Ending Net PP&E is ALWAYS the sum of its components (organic roll-forward). NEVER set Ending PP&E = Revenue × target ratio and back-solve for acquisitions as a plug.
- Acquisitions do NOT originate on the PP&E Build. They are a capital allocation decision. PP&E Build row for Acquisitions = pull from Capital Allocation Build (with sign flip if needed).
- PP&E acquisitions in projection years: set to 0 as a placeholder in Phase 2. Phase 4 (Capital Allocation) will overwrite with a live link.
