## Interest Expense & Debt

### Debt Build (Tranche-by-Tranche)
For each debt instrument:
- Beginning Balance + Draws - Repayments = Ending Balance
- Interest Expense = Average Balance × Interest Rate (or Beginning Balance × Rate if contractually specified)
- Track maturity dates, mandatory amortization, optional prepayment
- Revolver: modeled as plug for minimum cash balance or set to zero with excess cash sweep

### Interest Rate Assumptions
- Fixed-rate debt: use the contractual rate
- Floating-rate debt: base rate assumption (SOFR) + spread. Base rate assumption cell: yellow background (#FFFF00) + blue text (#0000FF) + source comment.
- Interest income on cash balances: separate line, use conservative money market rate

### Cash & Equivalents
Cash & Equivalents lives on the Debt Build alongside the debt tranches. Historical periods pull from the BS tab (green text). Projection periods are a **hardcoded target balance assumption** (yellow bg + blue text) -- the analyst sets the target cash balance directly, just like debt balances.

The change in cash flows into the Capital Allocation Build as a source/use of cash:
- **Capital Allocation Build**: `Net Change in Cash = -(Target Cash - Prior Period Cash)`
- **Sign convention**: cash increase = negative (use of cash, reduces Cash Available for Allocation); cash decrease = positive (source of cash)
- Net Change in Cash sits in the **Cash Available for Allocation** section, NOT in Capital Deployment
- The BS Cash line pulls FROM the Debt Build (`='Debt Build'!cell`), not from the CFS tab

This ensures the buyback plug on the Capital Allocation Build automatically adjusts when the analyst changes the target cash balance.

### Debt = Input (Capital Allocation Decision)
Debt assumptions (contractual amortization, planned issuance, optional prepayment) live on the Debt Build tab. The Capital Allocation Build waterfall (Phase 4) incorporates net debt changes from the Debt Build as an input to the cash waterfall. The Debt Build owns the debt assumptions; the Cap Alloc waterfall passes them through.
