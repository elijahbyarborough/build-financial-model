<!-- CANONICAL: this file is canonical in build-model-phase-03. Copies elsewhere sync FROM here. -->

## BS & CFS Build — Debt & Cash Section and Lease Schedules

The **Debt & Cash** section (Tier-2 header) follows a fixed sub-block order, and the two lease schedules are separate Tier-2 sections that follow it — they anchor the **bottom** of the BS & CFS Build tab, below the backbone sections (PP&E, Goodwill, Working Capital, Debt & Cash), per the canonical tab layout in `phase-3-drivers.md`. If the build surfaces additional self-contained schedules beyond leases, group them here at the bottom too, so the sections above always reference *down* into them. Every model uses this exact structure (inapplicable sections omitted; LOW-materiality lease simplification allowed per `meth-lease-full.md`). Sections/sub-blocks that don't apply (e.g., the Finance Lease Schedule when HAS_FINANCE_LEASES = N) are omitted entirely — do not leave empty headers. If `LEASE_MATERIALITY = LOW`, the simplified straight-line runoff from `meth-lease-full.md` may replace the full lease schedules.

Row numbers below are illustrative; actual rows depend on header placement (rows 1-5 are standard model headers). The key constraint is **ordering**, not exact row numbers.

### Sub-Block 1: Debt Balances

| Row | Label | Source |
|-----|-------|--------|
| 1 | Short-Term Debt / Current Portion | Historicals: `=Annual Historicals` (Debt Detail / BS face), green ref. Projections: ST/LT split of Total Debt via historical current/total ratio or disclosed maturities. |
| 2 | Long-Term Debt | Historicals: `=Annual Historicals`, green ref. Projections: `=Total Debt - Short-Term Debt`. |
| 3 | Total Debt | Historicals: `=SUM(Short-Term + Long-Term)`. Projections: `=Sub-Block 2 Ending Total Debt` (the roll-forward is the authority). |
| 4 | Less: Cash & Equivalents | `=Cash & Equivalents row (Sub-Block 5)` |
| 5 | Net Debt | `=Total Debt - Cash` |

**The BS pulls FROM this section in Phase 4** (`BS!Short-Term Debt`, `BS!Long-Term Debt` = these rows). This section never references the BS tab — historicals come from Annual Historicals, projections from the roll-forward — so there is no mutual-reference loop.

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
| 4 | Interest Income (optional) | Only if the company earns material interest income. Historicals link to Annual Historicals; projections = conservative money-market rate × average cash (Sub-Block 5). Kept SEPARATE from Total Interest Expense; feeds the Profit Build Memo Pre-Tax Income (`= EBIT - Total Interest Expense + Interest Income`). |
| 5 | Debt Interest Rate | Historical implied = `Debt Int / Avg Total Debt`. Projection = blue assumption (carry forward). |
| 6 | Finance Lease Interest Rate | `=FL Schedule Rate row` (only if HAS_FINANCE_LEASES = Y) |
| 7 | Check (Total Int - Debt Int - FL Int) | Must be 0. Validation row. |

### Sub-Block 4: Leverage Metrics

| Row | Label | Notes |
|-----|-------|-------|
| 1 | EBITDA | Green ref to the Profit Build Consolidated P&L Bridge |
| 2 | *Cumulative Acquired EBITDA (memo)* | Added by Phase 5 when an M&A program exists: `='Capital Allocation Build'!Cumulative Acquired EBITDA` (green, italic). Blank/omitted until then |
| 3 | **Credit-Adjusted EBITDA** | `=EBITDA + Cumulative Acquired EBITDA`. Equals plain EBITDA when no M&A program. **All ratios below use THIS row** — acquired businesses bring EBITDA that lenders count, and dividing acquisition-funded debt by organic-only EBITDA overstates leverage. Memo concept only: never feeds the IS/CFS/EPS/Returns |
| 4 | Total Debt | Echo from Sub-Block 1 |
| 5 | Total Finance Leases | `=FL Liability` (if applicable) |
| 6 | Total Obligations | `=Total Debt + OL Liability + FL Liability` |
| 7 | Net Debt | Echo from Sub-Block 1 |
| 8 | Net Obligations | `=Net Debt + OL Liability + FL Liability` |
| 9 | Total Debt / EBITDA | Ratio (Credit-Adjusted EBITDA denominator) |
| 10 | Total Obligations / EBITDA | Ratio (Credit-Adjusted EBITDA denominator) |
| 11 | Net Debt / EBITDA | Ratio (Credit-Adjusted EBITDA denominator) |
| 12 | Net Obligations / EBITDA | Ratio (Credit-Adjusted EBITDA denominator) |
| 13 | Interest Coverage (EBITDA / Interest) | `=Credit-Adjusted EBITDA / ABS(Total Interest)` |

In Phase 3, build rows 2-3 with Credit-Adjusted = plain EBITDA (the Capital Allocation Build doesn't exist yet); Phase 5 links row 2 when it sets up the M&A program. Display-layer only — nothing computational depends on these ratios, so the cross-tab reference creates no circularity.

### Sub-Block 5: Cash & Equivalents (Target Balance)

| Row | Label | Notes |
|-----|-------|-------|
| 1 | Cash & Equivalents | Historicals: `=BS!Cash` (green). Projections: hardcoded target balance assumption — yellow bg + blue text + source comment (e.g., "management minimum cash guidance" or "held flat at current level"). |
| 2 | Change in Cash (memo) | `=Current − Prior`. Italic. |

**This row is the model's cash authority in projections.** The BS cash line pulls FROM this row in Phase 4 (`BS!Cash = Debt & Cash target`), and the Capital Allocation Build references the change in cash as a use/source in its waterfall (`Net Change in Cash = -(Target − Prior)`; an increase in the cash balance is a use of capital). CFS ending cash stays flow-based and is forced to this target by the Phase 5 buyback plug.

### Operating Lease Schedule (own Tier-2 section; only if HAS_OPERATING_LEASES = Y)

Section placement: immediately after the Debt & Cash section. Row list, in order:

- **Drivers**: New Operating Leases (historical = `Ending ROU - Beginning ROU + Lease Cost`; projection = blue assumption, trailing 3yr avg) · Implied Useful Life (`=Prior Total Liability / Annual Lease Cost`, carried forward) · Operating Lease Cost (`=Prior Total Liability / Useful Life` — NOT revenue-driven) · % of Revenue memo (italic)
- **Balance Sheet Items**: ROU Asset Net and Total Liability (identical roll-forwards: `=Beginning + New - Lease Cost`) · Liability Current (**formula-driven split ONLY — never hardcoded**: `=MIN(Total, Next-Year Lease Cost)` or historical current/total ratio) · Liability Non-Current (`=Total - Current`) · Wtd Avg Discount Rate (blue assumption, carried forward)

**Schedule mechanics, formulas, CF rules, and edge cases per `meth-lease-full.md` (canonical in build-model-phase-01).**

### Finance Lease Schedule (own Tier-2 section; only if HAS_FINANCE_LEASES = Y)

Section placement: immediately after the Operating Lease Schedule. Row list, in order:

- **Drivers**: New Finance Leases (historical = `Ending Gross ROU - Beginning Gross ROU`; projection = blue assumption) · Implied Depreciation Life (historical = `Beginning Net ROU / Depreciation`, carried forward) · Depreciation Expense (`=Beginning Net ROU / Life` — straight-line on the beginning balance; new additions begin depreciating the following year, which keeps the implied-life inversion internally consistent) · Interest Rate (historical = `FL Interest / Beginning Liability`; projection = blue assumption) · Interest Expense (`=Rate × Beginning Liability` — beginning, NOT average, to avoid the circular ref)
- **ROU Asset Roll-Forward**: `Ending Net ROU = Beginning + New - Depreciation` (gross + accumulated-depreciation roll where the company disclosure supports it, per meth-lease-full)
- **Liability Roll-Forward**: `Ending = Beginning + New + Interest Accrued - Cash Payments`, where Cash Payments `= Depreciation + Interest`
- **Balance Sheet Items**: ROU Asset Net (`=ROU roll-forward ending`) · Liability Current (**formula-driven split ONLY — never hardcoded**) · Liability Non-Current (`=Total - Current`) · Total Liability (`=Liability roll-forward ending`)
- **Finance Lease Expense (IS memo)**: Depreciation · Interest · Total (`=Dep + Int`)

**Schedule mechanics, formulas, CF rules (principal-only CFF payment), and edge cases per `meth-lease-full.md` (canonical in build-model-phase-01).**

### Key Rules

- **Label prefixes**: Roll-forward addition rows use `"+ "` prefix (text-formatted cell), subtraction rows use `"- "` prefix. These are string literals, not formulas.
- **Sign convention**: Interest expense and depreciation are stored as positive values in the Drivers sub-sections. They are negated with `=-` when they appear as subtractions in roll-forwards or on the IS/CFS.
- **Circular reference avoidance**: FL Interest = `Rate × Beginning Liability`, never average liability. This breaks the circular dependency (interest → payments → ending liability → average liability → interest).
- **Historicals link to Annual Historicals** (Debt Detail and Lease Detail sections) or to same-workbook statement tabs where noted. Projections use blue assumptions with source comments.
- **Blank separator rows** between each sub-block and section (Debt Balances, Roll-Forward, Interest, Leverage, Cash, OL Schedule, FL Schedule).
- **Section headers** ("Debt & Cash", "Operating Lease Schedule", "Finance Lease Schedule") are Tier-2 subheaders per firm-formatting (#C2D5EB fill, bold).
- **Sub-headers** (e.g., "Drivers", "Balance Sheet Items", "ROU Asset Roll-Forward") are bold italic below their parent section.
