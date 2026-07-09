<!-- CANONICAL: this file is canonical in build-model-phase-3. Copies elsewhere sync FROM here. -->

## BS & CFS Build — Debt & Cash Section and Lease Schedules

The **Debt & Cash** section (Tier-2 header) follows a fixed sub-block order, and the two lease schedules are separate Tier-2 sections that follow it. Every model uses this exact structure. Sections/sub-blocks that don't apply (e.g., the Finance Lease Schedule when HAS_FINANCE_LEASES = N) are omitted entirely — do not leave empty headers.

Row numbers below are illustrative; actual rows depend on header placement (rows 1-5 are standard model headers). The key constraint is **ordering**, not exact row numbers.

### Sub-Block 1: Debt Balances

| Row | Label | Source |
|-----|-------|--------|
| 1 | Short-Term Debt / Current Portion | `=BS!Short-Term Debt` (if applicable) |
| 2 | Long-Term Debt | `=BS!Long-Term Debt` |
| 3 | Total Debt | `=SUM(Short-Term + Long-Term)` |
| 4 | Less: Cash & Equivalents | `=Cash & Equivalents row (Sub-Block 5)` |
| 5 | Net Debt | `=Total Debt - Cash` |

### Sub-Block 2: Debt Roll-Forward

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Beginning Total Debt | `=Prior period Ending Total Debt` |
| 2 | Debt Issuance / (Repayment), Net | Blue assumption in projections. Historicals = `Ending - Beginning`. |
| 3 | Ending Total Debt | `=Beginning + Net Issuance` |

### Sub-Block 3: Interest Expense

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Debt Interest Expense | Historicals link to Annual Historicals. Projections = `Debt Rate × Average Total Debt`. |
| 2 | Finance Lease Interest Expense | `=FL Schedule Interest row` (only if HAS_FINANCE_LEASES = Y) |
| 3 | Total Interest Expense | `=SUM(Debt Int + FL Int)` — this is what the IS and the Profit Build Tax section reference. |
| 4 | Debt Interest Rate | Historical implied = `Debt Int / Avg Total Debt`. Projection = blue assumption (carry forward). |
| 5 | Finance Lease Interest Rate | `=FL Schedule Rate row` (only if HAS_FINANCE_LEASES = Y) |
| 6 | Check (Total Int - Debt Int - FL Int) | Must be 0. Validation row. |

### Sub-Block 4: Leverage Metrics

| Row | Label | Notes |
|-----|-------|-------|
| 1 | EBITDA | Green ref to the Profit Build Consolidated P&L Bridge |
| 2 | Total Debt | Echo from Sub-Block 1 |
| 3 | Total Finance Leases | `=FL Liability` (if applicable) |
| 4 | Total Obligations | `=Total Debt + OL Liability + FL Liability` |
| 5 | Net Debt | Echo from Sub-Block 1 |
| 6 | Net Obligations | `=Net Debt + OL Liability + FL Liability` |
| 7 | Total Debt / EBITDA | Ratio |
| 8 | Total Obligations / EBITDA | Ratio |
| 9 | Net Debt / EBITDA | Ratio |
| 10 | Net Obligations / EBITDA | Ratio |
| 11 | Interest Coverage (EBITDA / Interest) | `=EBITDA / ABS(Total Interest)` |

### Sub-Block 5: Cash & Equivalents (Target Balance)

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Cash & Equivalents | Historicals: `=BS!Cash` (green). Projections: hardcoded target balance assumption — yellow bg + blue text + source comment (e.g., "management minimum cash guidance" or "held flat at current level"). |
| 2 | Change in Cash (memo) | `=Current − Prior`. Italic. |

**This row is the model's cash authority in projections.** The BS cash line pulls FROM this row in Phase 4 (`BS!Cash = Debt & Cash target`), and the Capital Allocation Build references the change in cash as a use/source in its waterfall (`Net Change in Cash = -(Target − Prior)`; an increase in the cash balance is a use of capital). CFS ending cash stays flow-based and is forced to this target by the Phase 5 buyback plug.

### Operating Lease Schedule (own Tier-2 section; only if HAS_OPERATING_LEASES = Y)

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

### Finance Lease Schedule (own Tier-2 section; only if HAS_FINANCE_LEASES = Y)

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

### Key Rules

- **Label prefixes**: Roll-forward addition rows use `"+ "` prefix (text-formatted cell), subtraction rows use `"- "` prefix. These are string literals, not formulas.
- **Sign convention**: Interest expense and depreciation are stored as positive values in the Drivers sub-sections. They are negated with `=-` when they appear as subtractions in roll-forwards or on the IS/CFS.
- **Circular reference avoidance**: FL Interest = `Rate × Beginning Liability`, never average liability. This breaks the circular dependency (interest → payments → ending liability → average liability → interest).
- **Historicals link to Annual Historicals** (Debt Detail and Lease Detail sections) or to same-workbook statement tabs where noted. Projections use blue assumptions with source comments.
- **Blank separator rows** between each sub-block and section (Debt Balances, Roll-Forward, Interest, Leverage, Cash, OL Schedule, FL Schedule).
- **Section headers** ("Debt & Cash", "Operating Lease Schedule", "Finance Lease Schedule") are Tier-2 subheaders per firm-formatting (#C2D5EB fill, bold).
- **Sub-headers** (e.g., "Drivers", "Balance Sheet Items", "ROU Asset Roll-Forward") are bold italic below their parent section.
