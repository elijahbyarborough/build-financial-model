<!-- CANONICAL COPY: build-model-phase-3/meth-tax.md — sync edits there first -->

## Profit Build — Tax Section

Tax lives in the **Tax section of the Profit Build tab** — the last Tier-2 section, built after the P&L Bridge and the Debt & Cash section of BS & CFS Build (its memo pre-tax income needs EBIT and interest).

### Default Approach: Effective Tax Rate (Most Companies)

Most companies can be modeled with a simple effective tax rate. This is the default unless the company has material NOLs or complex tax structures.

- **Memo Pre-Tax Income**: `= EBIT (Consolidated P&L Bridge) − Total Interest Expense + Interest Income, if any (both from the 'BS & CFS Build' Debt & Cash section, green refs; interest income is the optional Sub-Block 3 row)`. This row makes the Tax section self-contained; in Phase 4 the IS EBT must tie to it (validation check).
- **Income Tax Expense**: `= Memo Pre-Tax Income × Effective Tax Rate`
  - Effective tax rate: yellow background (#FFFF00) + blue text (#0000FF), source comment with historical average and statutory reference
  - Default: use the trailing 3-year average effective rate unless management guides otherwise
  - If pre-tax income is negative, set tax expense to 0 (do not model a tax benefit unless there is a clear DTA to support it)
- **Balance Sheet**: Model DTAs and DTLs in this section
  - Deferred Tax Assets: driven by tax loss carryforwards, SBC deductions, accruals timing
  - Deferred Tax Liabilities: driven by accelerated depreciation, intangible amortization timing
  - Net DTA/DTL change flows through the CF statement as a non-cash CFO adjustment
  - For most companies, project net DTL as a % of PP&E or as flat-to-growing based on historical trend
- Historical tax expense, ETR, and DTA/DTL balances link to Annual Historicals (green cross-sheet refs)

### NOL Companies (When Applicable)

When a company has material Net Operating Loss carryforwards (check the tax footnote — captured in the Annual Historicals Miscellaneous section), the Tax section needs additional rows:

- **Beginning NOL Balance**: sourced from the most recent 10-K tax footnote via Annual Historicals
- **NOL Utilization**: `=MIN(NOL Balance, Pre-Tax Income × Section 382 Limit %)` — NOLs offset taxable income up to the Section 382 annual limit (if applicable) or 80% of taxable income post-TCJA
- **Ending NOL Balance**: `=Beginning - Utilization`
- **Cash Tax Rate**: `=MAX(0, (Pre-Tax Income - NOL Utilization) × Statutory Rate) / Pre-Tax Income` — the actual cash tax paid after NOL shielding
- **GAAP Tax Rate**: Use the effective rate for IS presentation (GAAP requires full provision regardless of NOL usage). The difference between GAAP and cash tax creates the DTA drawdown.

The key output: **DTA drawdown** = NOL Utilization × Statutory Rate. This is a non-cash charge that flows through CFO as a negative adjustment (DTA decrease = cash source).

### Tax Section Outputs

The Tax section must output these rows for other tabs to reference:
1. **Income Tax Expense** → IS pulls this in Phase 4
2. **Net change in DTA/DTL** → CFS pulls this as a CFO non-cash adjustment
3. **Cash Taxes Paid** (memo) → useful for FCF sanity checks
