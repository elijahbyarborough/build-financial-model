<!-- CANONICAL COPY: build-model-phase-1/meth-lease-full.md — sync edits there first -->

## Lease Accounting (ASC 842)

Leases are projected on the **Debt Build** tab in dedicated schedule sections. Both operating and finance leases follow a roll-forward architecture that feeds the BS, IS, and CFS through the Working Capital Build.

### Detection & Classification (Phase 0 / Phase 1)

When scanning source data (BAMSEC, Tegus, Historical Data), look for lease indicators before building any tabs.

**Balance Sheet signals**: "Operating Lease ROU", "Right-of-use", "Finance Lease", "Capital Lease", "ROU Asset"
**Income Statement signals**: "Operating lease cost", "Finance lease depreciation", "Finance lease interest", "Lease expense"
**Cash Flow signals**: "Finance lease payments", "Operating lease payments", "ROU asset amortization"
**Footnote signals**: Look in the lease accounting policy footnote for explicit mapping language like "Finance leases are included in [BS line], [BS line], and [BS line]." Companies are required to disclose this under ASC 842.

Set the following flags on the Task Tracker Model State Block:
- `HAS_OPERATING_LEASES`: Y/N
- `HAS_FINANCE_LEASES`: Y/N
- `LEASE_MATERIALITY`: HIGH/LOW -- HIGH if lease assets > 5% of total assets

**Materiality shortcut**: If `LEASE_MATERIALITY = LOW` and the portfolio is declining (no material new lease additions), use simplified straight-line runoff with no new additions instead of the full roll-forward architecture.

### Lease BS Map (Phase 0 / Phase 1)

Companies present lease items on the balance sheet in different ways. Some give leases their own dedicated BS lines; others embed them inside broader line items like PP&E, "Accrued expenses," or "Other liabilities." The Lease BS Map captures exactly where each component lives for the specific company being modeled.

**Why this matters**: The Working Capital Build must strip lease items out of whatever reported BS lines they sit in, so those "Other" lines can be projected using their own drivers (% of revenue, flat, etc.) while the lease items are projected by the Debt Build schedule. If you get the mapping wrong, you'll either double-count lease balances or leave them stranded in a generic line that gets projected with the wrong driver.

Populate the following map on the Task Tracker during Phase 0/1 by reading the lease accounting policy footnote and cross-referencing against the face of the balance sheet and the PP&E footnote detail:

```
LEASE BS MAP:
  OL_ROU_ASSET_NC:     [reported BS line name]
  OL_ROU_ASSET_C:      [reported BS line name, or "N/A" if no current portion]
  OL_LIAB_C:           [reported BS line name]
  OL_LIAB_NC:          [reported BS line name]
  FL_ROU_ASSET_NC:     [reported BS line name]
  FL_ROU_ASSET_C:      [reported BS line name, or "N/A" if no current portion]
  FL_LIAB_C:           [reported BS line name]
  FL_LIAB_NC:          [reported BS line name]
```

**How to populate each field**:

1. **Start with the lease footnote disclosure.** Under ASC 842, companies must disclose where lease items are presented on the BS. Look for language like: "Operating leases are included in [line], [line], and [line]. Finance leases are included in [line], [line], and [line]." This is the primary source.

2. **Cross-reference the face of the BS.** Check whether the BS has dedicated lines for any lease items (e.g., "Operating lease right-of-use assets," "Operating lease liabilities, current"). If a dedicated line exists, that's the mapping — it's not embedded inside another line.

3. **Check the PP&E footnote.** If the lease footnote says FL ROU assets are in "Property and equipment, net," verify by looking at the PP&E gross detail table in the footnotes. You should see "Finance lease right-of-use assets" as an explicit category in the gross roll-up. This confirms FL ROU is inside PP&E.

4. **Check the maturity schedule.** The lease maturity schedule in the footnotes always discloses the current/noncurrent split for both OL and FL liabilities. Use this to determine the amounts, even when the BS doesn't show them as separate lines.

**Common patterns** (not exhaustive — always verify from the actual filing):

Pattern A — OL standalone, FL embedded (e.g., Meta):
```
OL_ROU_ASSET_NC:     "Operating lease right-of-use assets"        (own BS line)
OL_ROU_ASSET_C:      N/A
OL_LIAB_C:           "Operating lease liabilities, current"        (own BS line)
OL_LIAB_NC:          "Operating lease liabilities, non-current"    (own BS line)
FL_ROU_ASSET_NC:     "Property and equipment, net"                 (inside PP&E)
FL_ROU_ASSET_C:      N/A
FL_LIAB_C:           "Accrued expenses and other current liabilities" (embedded)
FL_LIAB_NC:          "Other liabilities"                           (embedded)
```

Pattern B — Everything standalone (e.g., some REITs, airlines):
```
OL_ROU_ASSET_NC:     "Operating lease right-of-use assets"        (own BS line)
OL_ROU_ASSET_C:      N/A
OL_LIAB_C:           "Operating lease liabilities, current"        (own BS line)
OL_LIAB_NC:          "Operating lease liabilities, non-current"    (own BS line)
FL_ROU_ASSET_NC:     "Finance lease right-of-use assets, net"     (own BS line)
FL_ROU_ASSET_C:      N/A
FL_LIAB_C:           "Finance lease liabilities, current"          (own BS line)
FL_LIAB_NC:          "Finance lease liabilities, non-current"      (own BS line)
```

Pattern C — FL liabilities in debt lines:
```
FL_LIAB_C:           "Current portion of long-term debt"           (embedded with debt)
FL_LIAB_NC:          "Long-term debt"                              (embedded with debt)
```

Pattern D — FL ROU in "Other assets":
```
FL_ROU_ASSET_NC:     "Other assets"                                (embedded)
```

**Current vs. noncurrent ROU assets**: Most companies report only the net (noncurrent) ROU asset on the BS. However, some companies separate a current portion into "Prepaid expenses and other current assets" or a similar line. If the BS or footnotes show a current portion of ROU assets, map it. If not (the majority of cases), set `_C` to "N/A."

**Derived flag for PP&E Build logic**: After populating the map, set:
- `FL_IN_PPE`: Y if `FL_ROU_ASSET_NC` maps to "Property and equipment, net" (or equivalent PP&E line); N otherwise. This flag drives the PP&E Build and D&A driver calculation, same as before.

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
- FL Depreciation: included in D&A (inside PP&E Build D&A if `FL_IN_PPE=Y`, or as a separate component if `FL_IN_PPE=N`)
- FL Interest: included in total interest expense

### Finance Lease ROU Asset Placement (FL_IN_PPE)

This is a derived flag from the Lease BS Map. If `FL_ROU_ASSET_NC` maps to a PP&E line, then `FL_IN_PPE = Y`. This drives the PP&E Build structure and D&A driver calculation.

#### FL_IN_PPE = Y (finance lease ROU inside PP&E)

- **PP&E Build**: New FL additions flow through an "Other" capex line: `='Debt Build'!New_FL_Additions`. FL depreciation is embedded in total D&A.
- **D&A driver calculation (CRITICAL)**: The D&A-as-%-of-revenue driver must be calculated EXCLUDING finance lease depreciation. Otherwise the FL portion inflates the %, leading to over-projection of D&A. Historical driver formula: `=(Total D&A - FL Depreciation) / Revenue`. Projection D&A allocation formula must subtract FL depreciation before applying the segment/corporate split.
- **BS**: Single "Net PP&E" line that includes FL ROU. No separate BS line for FL ROU asset.
- **CFS**: Total D&A add-back on CFS already includes FL depreciation (it is inside PP&E Build D&A).

#### FL_IN_PPE = N (finance lease ROU NOT inside PP&E)

When `FL_ROU_ASSET_NC` maps to something other than PP&E (e.g., "Other assets," "Finance lease right-of-use assets, net"), the FL ROU asset is tracked entirely on the Debt Build:

- **PP&E Build**: Clean -- no FL items at all. D&A is purely operating D&A. No complex allocation needed.
- **Debt Build**: Tracks the full FL ROU asset roll-forward (in addition to the liability roll).
- **BS**: The FL ROU asset gets its own line (or flows through the WC Build into whatever "Other" line the map specifies), pulling from `='Debt Build'!FL_ROU_Net`.
- **CFS**: Total D&A add-back = PP&E Build D&A + Debt Build FL Depreciation (two separate pulls).

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

The Working Capital Build is the bridge between the Debt Build (where leases are projected) and the BS/CFS. Lease items MUST be disaggregated from whatever reported BS lines they are embedded in. The Lease BS Map determines which lines need disaggregation.

**Required WC Build rows for leases** (one row per mapped component):
- Operating Lease ROU Assets (noncurrent): pull from Debt Build OL ROU
- Operating Lease ROU Assets (current): pull from Debt Build, only if `OL_ROU_ASSET_C` is mapped (not N/A)
- Operating Lease Liability — Current: pull from Debt Build OL Liability Current
- Operating Lease Liability — Noncurrent: pull from Debt Build OL Liability Noncurrent
- Finance Lease Liability — Current: pull from Debt Build FL Current
- Finance Lease Liability — Noncurrent: pull from Debt Build FL Noncurrent
- Finance Lease ROU Assets (noncurrent): pull from Debt Build FL ROU Net, only if `FL_IN_PPE = N`

**Disaggregation formula pattern**: For each lease component that is embedded inside a broader reported BS line (i.e., NOT its own dedicated BS line), create an "ex-lease" version of that reported line:

```
[Reported BS line] (ex-[lease component]) = BS![Reported line as reported] - 'Debt Build'![lease component]
```

The Lease BS Map tells you exactly which reported lines need this treatment. For example:

If `FL_LIAB_C` maps to "Accrued expenses and other current liabilities":
- WC Build gets: "Other CL (ex-FL Current)" = `BS!Accrued_Exp_Reported - 'Debt Build'!FL_Current`

If `FL_LIAB_NC` maps to "Other liabilities":
- WC Build gets: "Other NCL (ex-FL Noncurrent)" = `BS!Other_Liab_Reported - 'Debt Build'!FL_Noncurrent`

If `FL_LIAB_C` maps to "Current portion of long-term debt":
- WC Build gets: "Current LTD (ex-FL Current)" = `BS!Current_LTD_Reported - 'Debt Build'!FL_Current`
- The Debt Build may also need to disaggregate FL from corporate debt on that same line.

If a lease component has its own dedicated BS line (e.g., "Operating lease liabilities, current"), no disaggregation is needed for that component — the WC Build row simply references it directly.

**Projection formula pattern**: In projection years, each lease row pulls directly from the Debt Build. The "ex-lease" lines use their own drivers (% of revenue, flat, etc.) and do NOT include any lease amounts.

**Edge case — multiple lease components in the same reported line**: If both FL current liability AND another lease item are embedded in the same reported BS line, disaggregate both. For example, if both FL current liability and OL current liability are inside "Accrued expenses":
- "Accrued Expenses (ex-OL Current, ex-FL Current)" = `BS!Accrued_Reported - 'Debt Build'!OL_Current - 'Debt Build'!FL_Current`

### Build Order (Updated for Leases)

When leases are present, the build order for Phase 3 (Forward Statements) is:

1. Revenue Build
2. Costs Build (operating expenses, including OL lease cost)
3. **Debt Build (including Operating Lease Schedule and Finance Lease Schedule)**
4. PP&E Build (needs FL additions and depreciation from Debt Build if `FL_IN_PPE=Y`)
5. Working Capital Build (needs lease balances from Debt Build, uses Lease BS Map for disaggregation)
6. Tax Schedule

---
