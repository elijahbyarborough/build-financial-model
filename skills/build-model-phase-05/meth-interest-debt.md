<!-- CANONICAL COPY: build-model-phase-04/meth-interest-debt.md — sync edits there first -->

## Interest Expense & Debt

### Default Architecture: Aggregate Roll (per meth-debt-build)

The default debt build is the **single aggregate Total Debt roll-forward** with one net-issuance assumption, per `meth-debt-build.md` (canonical build methodology, build-model-phase-03). Most companies need nothing more.

### Escalation: Tranche-by-Tranche Build

Use tranche-level rolls ONLY when disclosure supports it AND the debt structure is material (multiple tranches, near-term maturities, meaningful floating-rate exposure). For each debt instrument:
- Beginning Balance + Draws - Repayments = Ending Balance
- Interest Expense = Average Balance × Interest Rate (or Beginning Balance × Rate if contractually specified)
- Track maturity dates, mandatory amortization, optional prepayment
- Revolver: only when explicitly modeled — as a plug for minimum cash balance, or set to zero with excess cash sweep. The default architecture does NOT use a revolver plug; the cash target + Phase 5 buyback plug is the model's cash authority.

### Interest Rate Assumptions
- Fixed-rate debt: use the contractual rate
- Floating-rate debt: base rate assumption (SOFR) + spread. Base rate assumption cell: yellow background (#FFFF00) + blue text (#0000FF) + source comment.
- **Interest income on cash balances**: lives as the optional Interest Income row in the Debt & Cash section (Sub-Block 3, per meth-debt-build) — conservative money-market rate × average cash. It feeds the Profit Build Memo Pre-Tax Income (`= EBIT − Total Interest Expense + Interest Income`). If the company earns material interest income and this row is missing, the Phase 4 EBT wiring check will fail.

### Cash & Equivalents
Cash & Equivalents lives in the Debt & Cash section of the BS & CFS Build, alongside the debt balances. Historical periods pull from the Annual Historicals tab (green text). Projection periods are a **hardcoded target balance assumption** (yellow bg + blue text) -- the analyst sets the target cash balance directly, just like debt balances.

The change in cash flows into the Capital Allocation Build as a source/use of cash:
- **Capital Allocation Build**: `Net Change in Cash = -(Target Cash - Prior Period Cash)`
- **Sign convention**: cash increase = negative (use of cash, reduces Cash Available for Allocation); cash decrease = positive (source of cash)
- Net Change in Cash sits in the **Cash Available for Allocation** section, NOT in Capital Deployment
- **BS Cash wiring**: `BS!Cash = CFS!Ending_Cash` — flow-based, per Phase 5 doctrine. The cash TARGET lives in the Debt & Cash section; the Phase 5 buyback plug forces CFS ending cash to converge to the target every period. Do NOT wire BS cash directly to the target cell.

This ensures the buyback plug on the Capital Allocation Build automatically adjusts when the analyst changes the target cash balance.

### Debt = Input (Capital Allocation Decision)
Debt assumptions (contractual amortization, planned issuance, optional prepayment) live in the Debt & Cash section of the BS & CFS Build tab. The Capital Allocation Build waterfall (Phase 5) incorporates net debt changes from the Debt & Cash section as an input to the cash waterfall. The Debt & Cash section owns the debt assumptions; the Cap Alloc waterfall passes them through.

---
