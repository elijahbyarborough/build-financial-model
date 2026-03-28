## Debt Build — Canonical Section Ordering

The Debt Build tab follows a fixed section order. Every model uses this exact structure. Sections that don't apply (e.g., Finance Leases when HAS_FINANCE_LEASES = N) are omitted entirely — do not leave empty section headers.

Row numbers below are illustrative; actual rows depend on header placement (rows 1-5 are standard model headers). The key constraint is **section ordering**, not exact row numbers.

### Section 1: Debt Balances

| Row | Label | Source |
|-----|-------|--------|
| 1 | Short-Term Debt / Current Portion | `=BS!Short-Term Debt` (if applicable) |
| 2 | Long-Term Debt | `=BS!Long-Term Debt` |
| 3 | Total Debt | `=SUM(Short-Term + Long-Term)` |
| 4 | Less: Cash & Equivalents | `=BS!Cash` |
| 5 | Net Debt | `=Total Debt - Cash` |

### Section 2: Debt Roll-Forward

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Beginning Total Debt | `=Prior period Ending Total Debt` |
| 2 | Debt Issuance / (Repayment), Net | Blue assumption in projections. Historicals = `Ending - Beginning`. |
| 3 | Ending Total Debt | `=Beginning + Net Issuance` |

### Section 3: Interest Expense

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Debt Interest Expense | Historicals from Tegus/source. Projections = `Debt Rate × Average Total Debt`. |
| 2 | Finance Lease Interest Expense | `=FL Schedule Interest row` (only if HAS_FINANCE_LEASES = Y) |
| 3 | Total Interest Expense | `=SUM(Debt Int + FL Int)` — this is what the IS references. |
| 4 | Debt Interest Rate | Historical implied = `Debt Int / Avg Total Debt`. Projection = blue assumption (carry forward). |
| 5 | Finance Lease Interest Rate | `=FL Schedule Rate row` (only if HAS_FINANCE_LEASES = Y) |
| 6 | Check (Total Int - Debt Int - FL Int) | Must be 0. Validation row. |

### Section 4: Leverage Metrics

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

### Section 5: Operating Lease Schedule (only if HAS_OPERATING_LEASES = Y)

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

### Section 6: Finance Lease Schedule (only if HAS_FINANCE_LEASES = Y)

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
- **Sign convention**: Interest expense and depreciation are stored as positive values in the Drivers section. They are negated with `=-` when they appear as subtractions in roll-forwards or on the IS/CFS.
- **Circular reference avoidance**: FL Interest = `Rate × Beginning Liability`, never average liability. This breaks the circular dependency (interest → payments → ending liability → average liability → interest).
- **Historicals link to source** (Tegus Model tab, BS, IS). Projections use blue assumptions with source comments.
- **Blank separator rows** between each major section (Debt Balances, Roll-Forward, Interest, Leverage, OL Schedule, FL Schedule).
- **Section headers** (e.g., "Debt Build", "Operating Lease Schedule", "Finance Lease Schedule") are bold, left-aligned, in the label column.
- **Sub-headers** (e.g., "Drivers", "Balance Sheet Items", "ROU Asset Roll-Forward") are bold italic, indented or left-aligned below their parent section.
