<!-- CANONICAL COPY: build-model-phase-2/meth-tax.md — sync edits there first -->

## Tax

### Default Approach: Effective Tax Rate (Most Companies)

Most companies can be modeled with a simple effective tax rate. This is the default unless the company has material NOLs or complex tax structures.

- **Income Statement**: Apply an effective tax rate assumption to EBT
  - Effective tax rate: yellow background (#FFFF00) + blue text (#0000FF), source comment with historical average and statutory reference
  - `Income Tax Expense = EBT × Effective Tax Rate`
  - Default: use the trailing 3-year average effective rate unless management guides otherwise
  - If EBT is negative, set tax expense to 0 (do not model a tax benefit unless there is a clear DTA to support it)
- **Balance Sheet**: Model DTAs and DTLs on the Tax Schedule tab
  - Deferred Tax Assets: driven by tax loss carryforwards, SBC deductions, accruals timing
  - Deferred Tax Liabilities: driven by accelerated depreciation, intangible amortization timing
  - Net DTA/DTL change flows through the CF statement as a non-cash CFO adjustment
  - For most companies, project net DTL as a % of PP&E or as flat-to-growing based on historical trend

### NOL Companies (When Applicable)

When a company has material Net Operating Loss carryforwards (check the tax footnote), the Tax Schedule needs additional rows:

- **Beginning NOL Balance**: sourced from the most recent 10-K tax footnote
- **NOL Utilization**: `=MIN(NOL Balance, EBT × Section 382 Limit %)` — NOLs offset taxable income up to the Section 382 annual limit (if applicable) or 80% of taxable income post-TCJA
- **Ending NOL Balance**: `=Beginning - Utilization`
- **Cash Tax Rate**: `=MAX(0, (EBT - NOL Utilization) × Statutory Rate) / EBT` — the actual cash tax paid after NOL shielding
- **GAAP Tax Rate**: Use the effective rate for IS presentation (GAAP requires full provision regardless of NOL usage). The difference between GAAP and cash tax creates the DTA drawdown.

The key output: **DTA drawdown** = NOL Utilization × Statutory Rate. This is a non-cash charge that flows through CFO as a negative adjustment (DTA decrease = cash source).

### Tax Schedule Outputs

The Tax Schedule tab must output these rows for other tabs to reference:
1. **Income Tax Expense** → IS pulls this
2. **Net change in DTA/DTL** → CFS pulls this as a CFO non-cash adjustment
3. **Cash Taxes Paid** (memo) → useful for FCF sanity checks

---
