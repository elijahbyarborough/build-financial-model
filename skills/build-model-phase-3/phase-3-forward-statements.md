# Phase 3 — Forward Statement Build (Model View Projections)

**Reference:** `references/phase-3-forward-statements.md`
**Prerequisite:** Phase 2 complete (all build tabs populated with historicals + projections, Driver Review passed).
**Next phase:** Phase 4 (Capital Allocation) → load `references/phase-4-capital-allocation.md`

---

## What Happens in This Phase

Project the Model View forward on the IS, BS, and CFS tabs by linking to the build tabs populated in Phase 2. The Reported View stays historical-only — its projection columns remain blank.

1. **IS Model View projections**: Pull revenue from Revenue Build, costs from Costs Build, D&A from PP&E Build, interest from Debt Build, tax from Tax Schedule. Compute EBITDA, EBIT, EBT, NI, EPS, margins.
2. **BS Model View projections**: Pull PP&E from PP&E Build, debt from Debt Build, WC items from Working Capital Build. Compute totals and BS Check.
3. **CFS Model View projections**: Pull NI from IS, D&A from PP&E Build, WC deltas from WC Build, noncurrent operating deltas from WC Build, DTL changes from Tax Schedule, capex from PP&E Build, debt changes from Debt Build. Compute CFO, CFI, CFF, ending cash, CF Check.

---

### Build Order (Updated for Leases)

When lease flags are set on the Task Tracker (`HAS_OPERATING_LEASES=Y` or `HAS_FINANCE_LEASES=Y`), the build order is:

1. Revenue Build
2. Costs Build (including operating lease cost as an OpEx line item)
3. **Debt Build -- including Operating Lease Schedule and Finance Lease Schedule projections**
4. PP&E Build (requires Debt Build FL data if `FL_IN_PPE=Y`)
5. Working Capital Build (requires Debt Build lease balances, uses Lease BS Map for disaggregation)
6. Tax Schedule
7. Income Statement assembly
8. Balance Sheet assembly
9. Cash Flow Statement assembly

**Dependency**: Debt Build (step 3) must complete before PP&E Build (step 4) starts, because PP&E Build needs:
- New FL additions for the "Other" capex line (if `FL_IN_PPE=Y`)
- FL depreciation amount to exclude from the D&A % driver calculation (if `FL_IN_PPE=Y`)

---

## The CFS is the BS Bridge

In projections, every CFS line item is a delta from a BS item or build tab. The assembly rule:

- **CFO non-cash items** = deltas from build tabs (ΔWC from WC Build, ΔDTL from Tax Schedule, ΔARO from WC Build noncurrent section, D&A from PP&E Build)
- **CFI** = negated capex from PP&E Build + acquisitions (set to 0 — placeholder for Phase 4)
- **CFF** = debt changes from Debt Build + dividends (0 placeholder) + buybacks (0 placeholder) — Phase 4 will re-link these
- **Cash = plug** (beginning + all flows = ending)

**If any BS item changes in projections and has no CF flow, the model is broken.** Consult the BS→CF mapping from Phase 1. Every mapped item must have its delta flowing through the CFS.

---

### Operating Lease Projections (Debt Build)

Project the operating lease schedule using drivers from Phase 2:
- **New Operating Leases**: Flat dollar assumption (yellow/blue), hardcoded per year
- **Lease Cost**: `= Prior Year Total Liability / Implied Useful Life`
- **ROU Asset**: `= Prior ROU + New Leases - Lease Cost`
- **Total Liability**: `= Prior Liability + New Leases - Lease Cost`
- **Current Liability**: Formula-driven: `=MIN(Total_Liability, Next_Year_Lease_Cost)` or use historical current/total ratio
- **Noncurrent Liability**: `= Total - Current`

### Finance Lease Projections (Debt Build)

Project the finance lease schedule:
- **New FL Additions**: Flat dollar assumption (yellow/blue). Set to zero if winding down.
- **Gross ROU**: `= Prior Gross + New Additions`
- **Depreciation**: `= Prior Net ROU / Useful Life` (straight-line)
- **Net ROU**: `= Prior Net - Depreciation` (or via accumulated depreciation roll)
- **Interest**: `= Beginning Liability * Implicit Rate`
- **Cash Payment (total)**: `= Depreciation + Interest`
- **Liability**: `= Prior Liability + New Additions + Interest - Cash Payment`
- **Current / Noncurrent**: Formula-driven, same approach as operating leases

### PP&E Build -- Finance Lease Adjustments

**Only applies when `FL_IN_PPE = Y`.**

- **"Other" Capex line**: `='Debt Build'!New_FL_Additions` -- pulls new finance lease additions as a non-cash capex item
- **D&A % of Revenue driver (CRITICAL)**: The historical D&A-as-%-of-revenue must be calculated EXCLUDING finance lease depreciation:
  D&A % = (Total D&A - FL Depreciation) / Revenue
  If FL depreciation is not excluded, the driver overstates D&A in projection years.
- **D&A allocation in projections**: Total projected D&A from the % driver represents operating D&A only. Finance lease depreciation is added separately from the Debt Build:
  Total D&A on PP&E Build = Operating D&A (from % driver) + FL Depreciation (from Debt Build)
  Or equivalently, if using segment allocation: each segment's D&A is computed from the ex-FL D&A pool, and FL depreciation is shown as a separate line.

**When `FL_IN_PPE = N`**: No adjustments needed. PP&E Build is clean. FL depreciation lives entirely on the Debt Build and is pulled separately to the CFS tab.

---

## Reported View: Historical Only

The Reported View on IS, BS, and CFS contains historical periods only. Projection columns are blank. The Reported View's job is auditability — proving the model ties to GAAP for the periods where GAAP data exists.

The reconciliation check (Model View Total = Reported View Total) applies to **historical periods only**, since the Reported View has no projections to compare against.

---

## Capital Allocation Placeholders (Phase 4 Will Overwrite)

This phase uses zero/flat placeholders for all items that depend on the Capital Allocation Build, which does not exist yet:

- **Dividends (CFF)**: set to 0 in all projection years
- **Share Repurchases (CFF)**: set to 0 in all projection years
- **Acquisitions (CFI)**: set to 0 in all projection years
- **Diluted Share Count**: hold flat at most recent historical diluted share count

Phase 4 (Capital Allocation) will overwrite all of these with live formulas linking to the Capital Allocation Build tab. EPS, CFS, and BS update automatically once the links are in place.

---

### IS -- Lease Expense Flows

- **Operating lease cost**: Flows to operating expenses (inside SG&A, COGS, or as a separate "Lease Expense" line, matching the company's reported presentation)
- **Finance lease depreciation**: Included in total D&A expense. If `FL_IN_PPE=Y`, this is already inside PP&E Build D&A. If `FL_IN_PPE=N`, pull separately: `Total IS D&A = PP&E Build D&A + Debt Build FL Depreciation`
- **Finance lease interest**: Included in total interest expense. Pull from Debt Build: `='Debt Build'!FL_Interest`. Add to any other interest (term loan, revolver, etc.) for total interest expense.

### BS -- Lease Line Items

**Assets**:
- **Operating Lease ROU Assets**: From WC Build (sourced from Debt Build OL ROU). Placed per
  Lease BS Map OL_ROU_ASSET_NC.
- **Finance Lease ROU Assets**: If FL_IN_PPE=Y, already inside Net PP&E. If FL_IN_PPE=N,
  placed per Lease BS Map FL_ROU_ASSET_NC (own line or through WC Build into host line).
- **Net PP&E**: From PP&E Build. Includes FL ROU only if `FL_IN_PPE=Y`.

**Liabilities**:
- **Finance Lease Current Liability**: Per Lease BS Map FL_LIAB_C — either its own BS line or
  embedded in the mapped host line via WC Build disaggregation.
- **Finance Lease Noncurrent Liability**: Per Lease BS Map FL_LIAB_NC — either its own BS line
  or embedded in the mapped host line via WC Build disaggregation.
- **Operating Lease Liabilities**: Per Lease BS Map OL_LIAB_C and OL_LIAB_NC — typically their own dedicated BS lines, sourced from WC Build (which pulls from Debt Build).

### CFS -- Lease Cash Flow Rules (HARD STOP)

**CF from Operating Activities**:
- **D&A add-back**: Includes finance lease depreciation. If `FL_IN_PPE=Y`, this is automatic (FL dep is inside PP&E Build D&A). If `FL_IN_PPE=N`, pull separately and add: `D&A addback = -'PP&E Build'!Total_D&A + -'Debt Build'!FL_Depreciation`.
- **Working capital changes**: Include the net change in operating lease balances (Change in OL ROU - Change in OL Liability). Also include changes in FL current/noncurrent liabilities as they flow through the WC Build's noncurrent operating items.

**CF from Financing Activities**:
- **Finance lease payment**: `= -'Debt Build'!FL_Depreciation` (the DEPRECIATION row, which equals the principal portion)
- **NEVER pull from the total payment row** (Depreciation + Interest). The interest component is already inside Net Income, which flows through CFO. Pulling total payment double-counts interest as a cash outflow.
- **This is the #1 lease modeling error.** If the BS check is non-zero by approximately the amount of FL interest expense, check this line first.

**Verification after CF assembly**:
1. Confirm FL financing payment = principal only (matches depreciation, not total payment)
2. Confirm D&A add-back includes FL depreciation
3. Confirm BS Check = 0 across all projection years

---

## Integrity Checks

All checks must pass for **every period** (historical AND projected):

1. **BS Check = $0**: Total Assets - Total L&E = 0
2. **CF Check = $0**: Beginning Cash + all CF - Ending Cash = 0
3. **NI ties IS→CFS**: Net Income on IS = Net Income on CFS
4. **RE roll-forward**: Beginning RE + NI - Dividends = Ending RE

If any check fails in projections, the most likely cause is a BS item changing without a corresponding CF flow. Use the BS→CF mapping to diagnose — find the BS line that changed and trace whether its delta appears on the CFS.

**No freeze panes. All formatting defers to `firm-formatting`.**

**STOP. Update Task Tracker. Report all check results. Wait for "continue."**
