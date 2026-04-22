# Phase 2 — Driver Buildout

**Prerequisite:** Phase 1 complete (historical IS/BS/CFS verified, all checks pass, BS→CF mapping done).

---

## What Happens in This Phase

Populate all build tabs with historical data and driver-based forward projections. The BS→CF mapping from Phase 1 is the blueprint — every BS item that was mapped to a build tab must appear here with an explicit driver.

1. **Revenue Build** (or segment tabs): driver-based revenue using the cascade from sector discovery
2. **Costs Build**: margin analysis, OpEx projections, incremental margins
3. **PP&E Build**: capex split (maintenance + growth), depreciation schedule, net PP&E roll-forward. **Set driver assumptions only in this phase — do NOT write forward projection formulas yet.** PP&E forward projections are built in Phase 3, after the Debt Build (including lease schedules) is complete. If the Lease BS Map shows FL ROU assets inside PP&E (FL_IN_PPE=Y), PP&E forward formulas depend on Debt Build finance lease data that may not exist until Phase 3.
4. **Debt Build**: tranche-by-tranche balances, interest expense
5. **Working Capital Build**: ALL operating BS items — current AND noncurrent — with explicit drivers
6. **Tax Schedule**: effective tax rate, NOL balances, DTA/DTL

---

## Mapping Requirement: Consult the BS→CF Map

Before building any tab, consult the BS→CF mapping from Phase 1. Every BS item assigned to a build tab must appear on that tab with:
1. Historical values linked to source
2. An explicit inline driver assumption (yellow bg + blue text)
3. A projected balance
4. A computed period-over-period change (delta) that the CFS will reference in Phase 3

**If a BS item is missing from its assigned build tab, add it.** No BS item may be left without a driver if it changes in projections.

---

## Working Capital Build: Comprehensive Scope

The Working Capital Build covers ALL operating BS items not on another dedicated build tab:

**Current operating items:** AR, Inventory, Prepaid, Other CA, AP, Accrued Liabilities, Deferred Revenue, Other CL

**Noncurrent operating items:** ARO, Operating Lease ROU Assets & Liabilities, Other Noncurrent Assets, Other Noncurrent Liabilities, Pension/Postretirement, Deferred Tax Assets (if not on Tax Schedule)

Each item gets the 4-step treatment:
1. **Identify**: from the BS→CF mapping
2. **Select Driver**: DSO/DIO/DPO, % of revenue, % of COGS, % of OpEx, fixed dollar, or growth rate — whichever is most stable historically
3. **Backtest**: check ratio stability (CV >25% → flag and consider alternatives)
4. **Structure**: inline assumption → projected balance → period-over-period delta

**Two summary output rows** for the CFS:
- **Change in Working Capital (CFO):** net of all current item deltas
- **Noncurrent Operating Changes (CFO):** net of all noncurrent item deltas

**No BS item may use an embedded growth rate** (e.g., `=K35*1.02`) unless the same growth is captured via a build tab with a visible driver assumption. The safe pattern: build tab has the driver → BS links to the build tab balance → CFS links to the build tab delta.

---

## Tax Schedule: DTL/DTA Output for CFS

The Tax Schedule must output the period-over-period change in Net DTL (or Net DTA) so the CFS can reference it as a non-cash CFO adjustment. The driver for DTL growth must be a visible yellow/blue assumption cell, not embedded in a formula.

---

### Debt Build — Canonical Section Ordering

The Debt Build tab follows a fixed section order. Every model uses this exact structure. Sections that don't apply (e.g., Finance Leases when HAS_FINANCE_LEASES = N) are omitted entirely — do not leave empty section headers.

Row numbers below are illustrative; actual rows depend on header placement (rows 1-5 are standard model headers). The key constraint is **section ordering**, not exact row numbers.

#### Section 1: Debt Balances

| Row | Label | Source |
|-----|-------|--------|
| 1 | Short-Term Debt / Current Portion | `=BS!Short-Term Debt` (if applicable) |
| 2 | Long-Term Debt | `=BS!Long-Term Debt` |
| 3 | Total Debt | `=SUM(Short-Term + Long-Term)` |
| 4 | Less: Cash & Equivalents | `=BS!Cash` |
| 5 | Net Debt | `=Total Debt - Cash` |

#### Section 2: Debt Roll-Forward

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Beginning Total Debt | `=Prior period Ending Total Debt` |
| 2 | Debt Issuance / (Repayment), Net | Blue assumption in projections. Historicals = `Ending - Beginning`. |
| 3 | Ending Total Debt | `=Beginning + Net Issuance` |

#### Section 3: Interest Expense

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Debt Interest Expense | Historicals from Tegus/source. Projections = `Debt Rate × Average Total Debt`. |
| 2 | Finance Lease Interest Expense | `=FL Schedule Interest row` (only if HAS_FINANCE_LEASES = Y) |
| 3 | Total Interest Expense | `=SUM(Debt Int + FL Int)` — this is what the IS references. |
| 4 | Debt Interest Rate | Historical implied = `Debt Int / Avg Total Debt`. Projection = blue assumption (carry forward). |
| 5 | Finance Lease Interest Rate | `=FL Schedule Rate row` (only if HAS_FINANCE_LEASES = Y) |
| 6 | Check (Total Int - Debt Int - FL Int) | Must be 0. Validation row. |

#### Section 4: Leverage Metrics

| Row | Label | Notes |
|-----|-------|-------|
| 1 | EBITDA (from IS) | `=IS!EBITDA` |
| 2 | Total Debt | Echo from Section 1 |
| 3 | Total Finance Leases | `=FL Liability` (if applicable) |
| 4 | Total Obligations | `=Total Debt + OL Liability + FL Liability` |
| 5 | Net Debt | Echo from Section 1 |
| 6 | Net Obligations | `=Net Debt + OL Liability + FL Liability` |
| 7 | Total Debt / EBITDA | Ratio |
| 8 | Total Obligations / EBITDA | Ratio |
| 9 | Net Debt / EBITDA | Ratio |
| 10 | Net Obligations / EBITDA | Ratio |
| 11 | Interest Coverage (EBITDA / Interest) | `=EBITDA / ABS(Total Interest)` |

#### Section 5: Operating Lease Schedule (only if HAS_OPERATING_LEASES = Y)

**Sub-section: Drivers**

| Row | Label | Notes |
|-----|-------|-------|
| 1 | New Operating Leases (ROU Additions) | Historical = `Ending ROU - Beginning ROU + Lease Cost`. Projection = blue assumption (trailing 3yr avg). |
| 2 | Implied Useful Life (years) | `=Prior Total Liability / Annual Lease Cost`. Projection = carry forward. |
| 3 | Operating Lease Cost | `=Prior Total Liability / Useful Life`. NOT revenue-driven. |
| 4 | % of Revenue (memo) | Memo only. `=Lease Cost / Revenue`. Italic. |

**Sub-section: Balance Sheet Items**

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Operating Lease ROU Asset, Net | Roll-forward: `=Beginning + New - Lease Cost` |
| 2 | Operating Lease Liability — Current | Blue assumption or formula-driven split |
| 3 | Operating Lease Liability — Non-Current | `=Total - Current` |
| 4 | Total Operating Lease Liability | `=Beginning + New - Lease Cost` |
| 5 | Wtd Avg Discount Rate | Blue assumption (carry forward historical) |

#### Section 6: Finance Lease Schedule (only if HAS_FINANCE_LEASES = Y)

**Sub-section: Drivers**

| Row | Label | Notes |
|-----|-------|-------|
| 1 | New Finance Leases | Historical = `Ending Gross ROU - Beginning Gross ROU`. Projection = blue assumption. |
| 2 | Implied Depreciation Life (years) | Historical = `Beginning Net ROU / Depreciation`. Projection = carry forward. |
| 3 | Depreciation Expense | `=(Beginning Net ROU + New) / Life` |
| 4 | Interest Rate | Historical = `FL Interest / Beginning Liability`. Projection = blue assumption. |
| 5 | Interest Expense | `=Rate × Beginning Liability` (use beginning, NOT average — avoids circular ref) |

**Sub-section: ROU Asset Roll-Forward**

| Row | Label | Formula |
|-----|-------|---------|
| 1 | Beginning ROU Asset, Net | `=Prior Ending` |
| 2 | + New Finance Leases | `=Drivers New FL` |
| 3 | - Depreciation of ROU Assets | `=-Drivers Depreciation` (negative) |
| 4 | Ending ROU Asset, Net | `=Beginning + New - Depreciation` |

**Sub-section: Liability Roll-Forward**

| Row | Label | Formula |
|-----|-------|---------|
| 1 | Beginning Liability | `=Prior Ending` |
| 2 | + New Finance Leases | `=Drivers New FL` |
| 3 | + Interest Accrued | `=Drivers Interest` |
| 4 | - Cash Payments (= Dep + Int) | `=-(Depreciation + Interest)` (negative) |
| 5 | Ending Liability | `=Beginning + New + Interest - Payments` |

**Sub-section: Balance Sheet Items**

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Finance Lease ROU Asset, Net | `=ROU Roll-Forward Ending` |
| 2 | Finance Lease Liability — Current | Blue assumption or formula split |
| 3 | Finance Lease Liability — Non-Current | `=Total - Current` |
| 4 | Total Finance Lease Liability | `=Liability Roll-Forward Ending` |

**Sub-section: Finance Lease Expense (IS Memo)**

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Depreciation of ROU Assets | `=Drivers Depreciation` |
| 2 | Interest on Lease Liabilities | `=Drivers Interest` |
| 3 | Total Finance Lease Cost | `=Depreciation + Interest` |

#### Key Rules

- **Label prefixes**: Roll-forward addition rows use `"+ "` prefix (text-formatted cell), subtraction rows use `"- "` prefix. These are string literals, not formulas.
- **Sign convention**: Interest expense and depreciation are stored as positive values in the Drivers section. They are negated with `=-` when they appear as subtractions in roll-forwards or on the IS/CFS.
- **Circular reference avoidance**: FL Interest = `Rate × Beginning Liability`, never average liability. This breaks the circular dependency (interest → payments → ending liability → average liability → interest).
- **Historicals link to source** (Tegus Model tab, BS, IS). Projections use blue assumptions with source comments.
- **Blank separator rows** between each major section (Debt Balances, Roll-Forward, Interest, Leverage, OL Schedule, FL Schedule).
- **Section headers** (e.g., "Debt Build", "Operating Lease Schedule", "Finance Lease Schedule") are bold, left-aligned, in the label column.
- **Sub-headers** (e.g., "Drivers", "Balance Sheet Items", "ROU Asset Roll-Forward") are bold italic, indented or left-aligned below their parent section.

---

## Key Rules for This Phase

- **Historicals link to source tabs** — never hardcoded.
- **Assumptions are inline** — same row as the metric, projection columns. Yellow bg + blue text + source comment.
- **Revenue is driver-based** — volume × price/rate strongly preferred.
- **PP&E acquisitions in projection years: set to 0 as a placeholder.** Phase 4 (Capital Allocation) will overwrite with a live link. Never back-solve acquisitions as a plug.
- **Non-contractual debt changes (optional prepayment, new issuance beyond scheduled amort) in projection years: set to 0 as a placeholder.** Phase 4 will incorporate final debt decisions.
- **Cash & Equivalents lives on the Debt Build** as a target balance assumption (yellow bg + blue text in projections, green text linking to BS for historicals). The change in cash feeds the Capital Allocation Build -- see `meth-debt-build.md` for full spec.
- **Each build tab outputs both balances (for BS) and deltas (for CFS)** — the statements pull from build tabs in Phase 3.
- **No freeze panes.**

---

---

## Tab Completion Verification

Before reporting any build tab complete, read and paste:

```
TAB VERIFICATION -- [Tab Name]:
  freezePanes: [null -- if not null, STOP and fix]
  gridlines: [off -- if on, STOP and fix]
  font: [Arial 10pt -- if not, STOP and fix]
  column widths uniform: [yes/no]
  tab color: [hex] -- expected: #5B8FA8
  historicals linked to source (not hardcoded): [yes/no]
  assumptions: yellow bg + blue text + source comments: [yes/no]
```

If you do not paste this output, the user cannot verify compliance. No output = not verified.

---

## Driver Review Checkpoint (Phase 2.5) -- Mandatory Output

Do NOT "conduct a review in your head." Write the driver review into the Task Tracker using this exact table schema:

| Tab | Driver | FY_last_E Value | Confidence | Rationale | Flag/Action |

**One row per driver assumption.** Every yellow-bg/blue-text assumption cell on every build tab must appear as a row in this table.

### Completeness Check

After populating the table:
1. Count yellow/blue assumption cells across all build tabs
2. Count rows in the driver review table
3. If rows < assumption cells, Phase 2 is not complete -- find the missing drivers and add them

### Confidence Gate

After populating, count LOW and MEDIUM confidence drivers:
- **If count >= 1**: STOP. List each LOW/MEDIUM driver with its tab, cell address, current value, and your rationale. Ask the analyst for explicit sign-off on each before proceeding.
- **If count = 0**: Report "All drivers HIGH confidence" and proceed.

Do NOT write "Driver review complete" without populating this table. The table IS the review.

---

## Definition of Done (Phase 2)

A phase is complete if and only if ALL of the following are true. Report completion by reading these values back to the user -- not by summarizing in prose.

1. **Task Tracker**: Every subtask row for Phase 2 shows Status = "COMPLETE". Cite the actual cell addresses you checked.
2. **Driver Review table populated** on the Task Tracker -- one row per driver assumption. Cite the cell range (e.g., "A166:F189 populated with [count] driver rows").
3. **Completeness check passed**: driver review row count = yellow/blue assumption cell count.
4. **LOW/MEDIUM drivers resolved**: either count = 0 or analyst sign-off obtained for each.
5. **All build tabs have historicals + driver assumptions**: Revenue Build, Costs Build, PP&E Build, Debt Build, Working Capital Build, Tax Schedule.
6. **Tab Completion Verification** output pasted for every build tab modified in this phase.
7. **Task Tracker Model State Block**: "Last Skill Run" updated, "Next Skill" = "build-model-phase-3".

If you write "Phase 2 complete" in chat before reading and reporting these values, you have made an error. Re-verify and correct.

**STOP. Report status. Wait for "continue."**
