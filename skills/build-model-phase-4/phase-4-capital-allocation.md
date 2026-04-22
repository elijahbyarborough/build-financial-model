# Phase 4 — Capital Allocation

**Prerequisite:** Phase 3 complete (IS/BS/CFS projected, all checks pass, CFO exists).

---

## What Happens in This Phase

Build the Capital Allocation Build tab — a continuous cash waterfall from CFO through capital deployment, with full share count mechanics, M&A value tracking, dividend policy, and shareholder returns analysis. Then re-link every placeholder from Phase 3.

1. **Cash Available waterfall**: 10-line exhaustive bridge from CFO to deployable cash
2. **Capital Deployment**: acquisitions, dividends, and buybacks (residual sweep)
3. **M&A Value Build**: track cumulative M&A capital and compound at assumed IRR
4. **SBC section**: single source of truth for SBC on this tab (drives dilution + returns)
5. **Share count roll-forward**: Buyback Price dynamics, buyback accretion, SBC dilution, TSM
6. **Dividend Policy**: DPS-driven with growth assumption
7. **Shareholder Returns**: FCF less SBC, % of NI allocation ratios
8. **Re-link Phase 3 placeholders** (see below)
9. **Waterfall check**: Cash Available + all deployment = 0

---

## Tab Structure — Exact Section Order

The Capital Allocation Build tab has ONE Tier 1 section header ("Capital Allocation Build") and 7 Tier 2 subheaders in this exact order:

1. **Cash Available for Allocation** (subheader)
2. **Capital Deployment** (subheader)
3. **Acquisitions** (subheader) — the M&A value build
4. **Stock-Based Compensation** (subheader)
5. **Share Repurchases & Count** (subheader)
6. **Dividend Policy** (subheader)
7. **Shareholder Returns** (subheader)

This flow is logical: sources of cash first, then discretionary deployment, then the supporting detail sections (M&A tracking, SBC, share count mechanics, dividend policy), then the summary returns analysis.

---

## Section 1: Cash Available for Allocation

A 10-line exhaustive waterfall. Every CFS line item must flow through this block so that Cash Available truly reconciles all cash sources and uses before discretionary deployment. Nothing gets lost between the Cash Flow Statement and capital deployment.

| Row | Label | Historical Formula | Projection Formula | Format |
|-----|-------|-------------------|-------------------|--------|
| 1 | Cash From Operations | `=CFS!CFO row` | `=CFS!CFO row` | Currency, green font |
| 2 | Capital Expenditures | `=CFS!Capex row` | `=CFS!Capex row` | Number, green font |
| 3 | Free Cash Flow | `=CFO + Capex` | `=CFO + Capex` | **Major total** (F2F2F2 fill, bold, Currency) |
| 4 | Proceeds / Divestitures | `=CFS!Proceeds + CFS!Divestitures` | 0 (blue/yellow assumption) | Currency |
| 5 | Other Investing | `=CFS!Other Investing` | 0 (blue/yellow assumption) | Number |
| 6 | Net Debt Issuance / (Repayment) | `=CFS!Net LT Debt + CFS!Net ST Debt` | `='Debt Build'!Net Issuance row` | Currency |
| 7 | Finance Lease Payments | `=CFS!Finance Lease row` | assumption (blue/yellow, e.g. -$7mm) | Number |
| 8 | Other Financing | `=CFS!Other Financing row` | assumption (blue/yellow, e.g. -$2mm) | Number |
| 9 | Net Change in Cash | `=-CFS!Net Change in Cash row` | `=-('Debt Build'!Ending Cash - 'Debt Build'!Beginning Cash)` | Number |
| 10 | **Cash Available for Allocation** | `=FCF + rows 4-9` | `=FCF + rows 4-9` | **Major total** (F2F2F2 fill, bold, Currency) |

### Key Design Decisions

- FCF is an intermediate **major total** (F2F2F2 fill), not just a subtotal
- Historical cells are ALL green font (cross-sheet pulls from CFS tab)
- Projection assumptions for Proceeds/Divest, Other Investing, Finance Leases, and Other Financing are blue/yellow with source comments, defaulting to 0 or small amounts
- **Net Debt Issuance in projections pulls from Debt Build** (NOT CFS tab) — the Debt Build is the driver tab
- **Net Change in Cash in projections** = negative of the change in the cash balance on the Debt Build. When cash is assumed flat (common), this = 0 and all FCF flows to deployment. When cash increases, this is a use of cash that reduces available capital
- Cash Available for Allocation = sum of ALL items (FCF + Proceeds + Other Investing + Net Debt + Finance Leases + Other Financing + Net Change in Cash)

---

## Section 2: Capital Deployment

Three lines in strict priority order, plus a check row:

| Row | Label | Historical Formula | Projection Formula | Notes |
|-----|-------|-------------------|-------------------|-------|
| 1 | Acquisitions, Net | `=CFS!Acquisitions row` | `=-Acquisitions assumption (from M&A section below)` | Priority 1 |
| 2 | Dividends Paid | `=CFS!Dividends row` | `=Total Dividends (from Dividend Policy section below)` | Priority 2 |
| 3 | Gross Share Repurchases | `=CFS!Share Repurchases row` | See Buyback Plug Formula below | **RESIDUAL SWEEP** |
| 4 | **Waterfall Check** | `=Cash Available + all deployment = 0` | `=Cash Available + all deployment = 0` | Should be ~0. Bold. |

### Buyback Plug Formula (MANDATORY)

Buybacks are the residual sweep — they absorb all excess cash after acquisitions, dividends, and every other CF flow. However, do NOT compute buybacks from the Cap Alloc waterfall residual. The waterfall is informational only.

Instead, compute buybacks as a direct plug against the Debt Build cash target. The formula enumerates every CF line item EXCEPT buybacks:

```
Buybacks = Target_Cash - Prior_End_Cash - CFO - CFI - SBC_Withholding - Dividends - Debt_Issuance - Debt_Repayment - Other_Financing - FX
```

Where:
- Target_Cash = Debt Build cash balance for the current period (e.g., `='Debt Build'!L10`)
- Prior_End_Cash = CFS ending cash from the prior period (e.g., `=CFS!K42`)
- All other terms reference their respective CFS tab rows for the current period
- Dividends references the Cap Alloc Build dividend row (which flows to CFS)

This guarantees CFS End Cash = Target Cash every period. The waterfall residual approach is fragile because any CFS line not explicitly included in the waterfall (e.g., SBC withholding payments, finance lease principal payments) causes buybacks to be mis-sized, which breaks the BS balance.

The Cap Alloc waterfall and its check row remain useful as an informational decomposition of cash sources and uses, but the actual buyback number must come from the direct plug formula.

### CFS End Cash Architecture

CFS End Cash must remain flow-based:
- `CFS!Net_Change_in_Cash = CFO + CFI + CFF + FX`
- `CFS!Beginning_Cash = Prior period CFS!Ending_Cash`
- `CFS!Ending_Cash = Beginning_Cash + Net_Change_in_Cash`
- `BS!Cash = CFS!Ending_Cash`

Do NOT override CFS!Ending_Cash or CFS!Net_Change_in_Cash to pull directly from the Debt Build cash target. That disconnects the flow identity (Beg + Flows = End) and causes the BS to break. The buyback plug formula is what forces End Cash to equal the target — the CFS formulas themselves must stay structural.

---

## Section 3: Acquisitions (M&A Value Build)

Turns M&A from a cash black hole into a trackable value creator. Five rows:

| Row | Label | Formula | Format | Notes |
|-----|-------|---------|--------|-------|
| 1 | Acquisitions, Net | `=Deployment Acquisitions row (same value, links up)` | Number | Links to deployment for auditability |
| 2 | Acquisition IRR | assumption (e.g. 15%) | Percentage, italic, blue/yellow | Key thesis assumption |
| 3 | Cumulative M&A Invested Capital | Year 1: `=ABS(Acquisitions)`. Year N: `=prior + ABS(year N acquisitions)` | Currency | Running total of all M&A capital deployed |
| 4 | M&A Portfolio Value at Exit | `=prior_value * (1 + IRR) + ABS(current year acquisitions)` | Currency, bold | Compounds entire M&A portfolio at assumed IRR |
| 5 | M&A Value Per Share ($/share) | `=IF(diluted_shares=0, "", portfolio_value / diluted_shares)` | Per-share, bold | Key output: per-share value from M&A |

### Design Rationale

- The Acquisition IRR assumption should have a source comment referencing the company's historical M&A track record, management guidance, or analyst judgment
- Portfolio Value uses a compounding model: existing portfolio appreciates at IRR, new acquisitions are added at cost (`ABS` of negative acquisition cash flow)
- Value Per Share uses the diluted share count from the Share Repurchases & Count section below — this creates a cross-reference within the tab
- Default Acquisition IRR suggestion: 15% (can be calibrated to company history)

---

## Section 4: Stock-Based Compensation (SBC)

Two rows that serve as the SINGLE source of truth for SBC across the model's capital allocation analysis:

| Row | Label | Historical Formula | Projection Formula | Format |
|-----|-------|-------------------|-------------------|--------|
| 1 | SBC % of Revenue | `=IF(Revenue=0, "", IS!SBC / IS!Revenue)` | assumption (blue/yellow, e.g. 0.75%) | Percentage, italic |
| 2 | SBC ($mm) | `=IS!SBC row` | `=SBC % * IS!Revenue` | Currency ($#,##0.0) |

SBC feeds two downstream calculations:
- **SBC Dilution** in the Share Count section: `SBC $ / Buyback Price` = shares diluted by SBC grants
- **FCF less SBC** in the Shareholder Returns section: `FCF - SBC` = cash truly available to equity holders

This dual-use is why SBC needs its own section — it is not just an IS line item on this tab, it is a key input to both dilution and returns analysis.

---

## Section 5: Share Repurchases & Count

Full share count roll-forward with 8 rows:

| Row | Label | Historical | Projection Formula | Format |
|-----|-------|-----------|-------------------|--------|
| 1 | Buyback Price ($/share) | n/a or manually entered | Hardcoded assumption each year (blue/yellow). Default: Year 1 = current share price, Year N = `prior * (1 + appreciation%)` where appreciation default is 15%. Analyst can override individual years. | Per-share ($#,##0.00), blue/yellow |
| 2 | Beginning Basic Shares (mm) | hardcoded or sourced | `=prior period Ending Basic Shares` | Decimal (0.0) |
| 3 | Shares Repurchased (mm) | n/a or calculated | `=IF(Buyback Price=0, 0, ABS(Gross Share Repurchases) / Buyback Price)` | Decimal (0.0) |
| 4 | SBC Dilution (mm) | n/a or calculated | `=IF(Buyback Price=0, 0, SBC $ / Buyback Price)` | Decimal (0.0) |
| 5 | Ending Basic Shares (mm) | hardcoded or sourced | `=Beginning - Repurchased + SBC Dilution` | Decimal (0.0), bold |
| 6 | Dilutive Securities (TSM) | n/a | assumption (blue/yellow, e.g. 1.0mm) | Decimal (0.0), blue/yellow |
| 7 | **Diluted Shares Outstanding (mm)** | hardcoded or sourced | `=Ending Basic + Dilutive Securities` | **Major total** (F2F2F2 fill, bold, 0.0) |
| 8 | Y/Y Change in Diluted Shares | calculated | `=IF(prior Diluted=0, "", (current Diluted / prior Diluted) - 1)` | Percentage, italic |

### Key Design Decisions

- **Buyback Price** is the assumed share price the company pays when repurchasing stock. Each year is a hardcoded blue/yellow assumption. Default approach: start at the current share price and grow at an assumed appreciation rate (default 15%/yr), but the analyst can override individual years. This is separate from the investor's Entry Price on the Returns tab — Buyback Price drives the capital allocation model, Entry Price drives the returns analysis. Buyback Price drives both the number of shares retired by buybacks AND the dilution from SBC grants.
- **Shares Repurchased** = `ABS(buyback dollars from deployment) / Buyback Price`. Buyback dollars come from the residual sweep, so shares repurchased is a derived output, not an input.
- **SBC Dilution** = `SBC dollars (from SBC section) / Buyback Price`. This is the net share dilution from stock-based compensation at the assumed share price.
- **Ending Basic** = `Beginning - Repurchased + SBC Dilution`. Net share count change reflects both buyback accretion and SBC dilution.
- **Dilutive Securities (TSM)** is a flat assumption for Treasury Stock Method dilution from outstanding options/RSUs not captured in the annual SBC dilution flow.
- **Y/Y Change in Diluted Shares** is a quick visual sanity check — should show steady net accretion (negative %) if buybacks exceed SBC dilution.

---

## Section 6: Dividend Policy

Three rows that drive dividends from a per-share basis:

| Row | Label | Historical | Projection Formula | Format |
|-----|-------|-----------|-------------------|--------|
| 1 | DPS ($/share) | hardcoded or sourced | `=ROUND(prior DPS * (1 + DPS Growth %), 2)` | Per-share ($#,##0.00) |
| 2 | DPS Growth % | calculated from history | assumption (blue/yellow, e.g. 5.0%) | Percentage, italic, blue/yellow |
| 3 | Total Dividends Paid | calculated | `=-(DPS * Beginning Basic Shares)` | Currency, bold (negative = cash out) |

### Key Design Decisions

- Dividends are **DPS-driven**, NOT calculated as a % of NI or FCF. This is more realistic — companies set DPS levels and grow them, they do not target a payout ratio.
- `ROUND(, 2)` is important — real DPS are quoted to the penny. Without rounding, you get values like $2.173333 which look unrealistic.
- DPS Growth % default: 5% or calibrate to the company's historical DPS CAGR. Source comment should reference dividend history.
- Total Dividends uses **Beginning Basic Shares** (not ending, not diluted) — this matches how dividends are actually paid (declared on shares outstanding at record date, roughly beginning of period).
- Total Dividends is **NEGATIVE** (cash outflow). The deployment row "Dividends Paid" pulls this value directly (`=Total Dividends row`).
- The Dividend Policy section is the driver; the Capital Deployment "Dividends Paid" line is a pull formula, not a standalone assumption.

---

## Section 7: Shareholder Returns

Seven rows providing a comprehensive view of how earnings are allocated:

| Row | Label | Formula | Format |
|-----|-------|---------|--------|
| 1 | Free Cash Flow (FCF) | `=FCF row from Cash Available section` | Currency, green font (same-tab pull) |
| 2 | Less: Stock-Based Compensation | `=-SBC $ from SBC section` | Number |
| 3 | FCF less SBC | `=FCF + Less SBC` | Currency, bold |
| 4 | (FCF - SBC) as % of NI | `=IF(NI=0, "", FCF less SBC / IS!Net Income)` | Percentage, italic |
| 5 | Acquisitions as % of NI | `=IF(NI=0, "", ABS(Acquisitions) / IS!Net Income)` | Percentage, italic |
| 6 | Buybacks as % of NI | `=IF(NI=0, "", ABS(Buybacks) / IS!Net Income)` | Percentage, italic |
| 7 | Dividends as % of NI | `=IF(NI=0, "", ABS(Dividends) / IS!Net Income)` | Percentage, italic |

### Design Rationale

- **FCF less SBC** is the "true" free cash flow available to equity holders after accounting for the real economic cost of stock-based compensation. SBC is a non-cash expense that dilutes equity holders — subtracting it from FCF gives the cash genuinely available for shareholder returns.
- The four **% of NI** rows show how each dollar of earnings is allocated across the capital deployment waterfall. This is a powerful analytical tool for understanding capital allocation discipline over time.
- All four % of NI rows use `ABS()` for the deployment items (which are negative) so the percentages display as positive values.
- All % rows use the `IF(NI=0, "")` guard to avoid `#DIV/0!` in years with zero or negative net income.
- This section spans ALL historical and projection columns to show the full capital allocation trend.

---

## Cross-Tab Dependencies

### What Cap Alloc READS from Other Tabs (Inputs)

| Line Item | Historical Source | Projection Source |
|-----------|-----------------|-------------------|
| CFO | CFS tab | CFS tab |
| Capex | CFS tab | CFS tab |
| Proceeds / Divestitures | CFS tab (Proceeds + Divestitures rows) | Assumption (default 0) |
| Other Investing | CFS tab | Assumption (default 0) |
| Net Debt Issuance | CFS tab (LT + ST debt rows) | Debt Build tab (net issuance row) |
| Finance Lease Payments | CFS tab | Assumption |
| Other Financing | CFS tab | Assumption |
| Net Change in Cash | CFS tab (Net Change row, negated) | Debt Build tab (`-(ending cash - beginning cash)`) |
| SBC | IS tab | IS tab (which itself pulls from the SBC assumption here via % * Revenue) |
| Net Income (for % ratios) | IS tab | IS tab |
| Revenue (for SBC %) | IS tab | IS tab |

### What Cap Alloc WRITES to Other Tabs (Outputs)

In projection years, the CFS tab pulls these three items FROM Cap Alloc Build:

- `CFS!Acquisitions` = `Cap Alloc!Acquisitions, Net` (deployment row)
- `CFS!Share Repurchases` = `Cap Alloc!Gross Share Repurchases` (deployment row)
- `CFS!Dividends Paid` = `Cap Alloc!Dividends Paid` (deployment row)

This is a **ONE-WAY dependency**: Cap Alloc drives CFS for discretionary deployment items. The CFS tab does NOT drive Cap Alloc for these items. This avoids circular references.

### Dependency Chain

```
Debt Build (debt schedule, cash balance)
    |
    v
Capital Allocation Build (reads debt/cash, computes deployment)
    |
    v
CFS Tab (pulls acquisitions, buybacks, dividends from Cap Alloc)
    |
    v
BS Tab (pulls ending cash, debt balances)
```

---

## Sign Conventions

Explicit sign conventions for the Capital Allocation Build tab:

**Cash Available block:**
- Positive = cash inflow (CFO, proceeds from sales, debt issuance)
- Negative = cash outflow (capex, lease payments, cash balance increase)
- Cash Available total is POSITIVE (net cash to deploy)

**Capital Deployment block:**
- ALL items are NEGATIVE (cash deployed outward)
- Acquisitions: negative (cash spent on M&A)
- Dividends: negative (cash paid to shareholders)
- Buybacks: negative (cash spent retiring shares)

**Waterfall Check:**
- `= Cash Available (positive) + All Deployment Items (all negative) = 0`

**Buyback plug formula:**
- Buybacks are computed as a direct plug against the Debt Build cash target (see Buyback Plug Formula above)
- The result is negative (cash deployed outward), consistent with other deployment items

---

## Key Assumptions Summary

All assumptions on the Cap Alloc Build tab (blue text #0000FF, yellow background #FFFF00, with source comments):

| Assumption | Section | Default | Source Comment Guidance |
|-----------|---------|---------|----------------------|
| Proceeds / Divestitures | Cash Available | 0 | "Analyst assumption -- no asset sales modeled" |
| Other Investing | Cash Available | 0 | "Analyst assumption -- no other investing activity" |
| Finance Lease Payments | Cash Available | Match recent history or declining | "Source: [filing], lease schedule" |
| Other Financing | Cash Available | Small negative (e.g. -$2mm) | "Analyst assumption -- misc financing" |
| Acquisitions, Net | M&A section | Company-specific | "Source: management guidance / analyst assumption" |
| Acquisition IRR | M&A section | 15% | "Source: Analyst assumption -- based on [rationale]" |
| SBC % of Revenue | SBC section | Match recent history (e.g. 0.75%) | "Source: 5yr historical average" |
| Buyback Price ($/share) | Share Count | Current share price, growing at appreciation % | "Source: market data as of [date]; appreciation per analyst assumption" |
| Dilutive Securities (TSM) | Share Count | ~1mm or calibrate | "Source: latest proxy, TSM calculation" |
| DPS ($/share) | Dividend Policy | Last actual DPS | "Source: latest dividend declaration" |
| DPS Growth % | Dividend Policy | 5% or historical CAGR | "Source: 5yr DPS CAGR" |

---

## Re-Link Phase 3 Placeholders (REQUIRED)

Phase 3 set dividends, acquisitions, share repurchases, and diluted share count to zero/flat placeholders. After completing all sections above, **replace every placeholder with a live formula linking to this tab**:

| Placeholder Location | Link To |
|---------------------|---------|
| CFS tab → Dividends (CFF) | `='Capital Allocation Build'!Dividends Paid` (deployment row) |
| CFS tab → Share Repurchases (CFF) | `='Capital Allocation Build'!Gross Share Repurchases` (deployment row) |
| CFS tab → Acquisitions (CFI) | `='Capital Allocation Build'!Acquisitions, Net` (deployment row) |
| IS tab → Diluted Share Count | `='Capital Allocation Build'!Diluted Shares Outstanding` |
| PP&E Build → Acquisitions row | `='Capital Allocation Build'!Acquisitions, Net` (sign-flipped) |
| BS tab → M&A Assets | Cumulative `ABS(acquisitions)` from Capital Allocation Build (= Cumulative M&A Invested Capital row) |

---

## Post-Wiring BS Equity Adjustment (MANDATORY)

Phase 3 builds BS equity projection formulas with zero placeholders for buybacks and dividends. After Phase 4 wires these flows into the CFS tab, the BS equity formulas MUST be patched to reflect them. If you skip this step, the BS will not balance.

1. **Retained Earnings**: Add dividends (which reduce RE).
   `= Prior_RE + IS!Net_Income + CFS!Dividends_Paid`
   Dividends are negative on the CFS tab, so adding them reduces RE. Phase 3's formula is `= Prior_RE + IS!Net_Income` which is incomplete once dividends are non-zero.

2. **Common Stock & APIC**: Add buybacks (which reduce equity via share retirements).
   `= Prior_CSAPIC - IS!SBC + CFS!SBC_Withholding + CFS!Buybacks`
   Buybacks are negative on the CFS tab, so adding them reduces CSAPIC. Phase 3's formula is `= Prior_CSAPIC - IS!SBC + CFS!SBC_Withholding` which is incomplete once buybacks are non-zero.

   Note: Alphabet retires repurchased shares rather than holding treasury stock, so buybacks flow through CSAPIC. For companies that use treasury stock accounting, create a Treasury Stock line on the BS instead: `= Prior_Treasury - CFS!Buybacks` (treasury is a contra-equity, stored as negative).

These formulas must reference the CFS tab cells directly (not Cap Alloc Build) to maintain the single-direction flow: Cap Alloc Build → CFS → BS.

---

## Verification Gate (Updated)

After ALL wiring is complete (Cap Alloc → CFS → BS → IS), force a full recalculation and verify ALL of the following:

1. BS Check = $0 all projection years
2. CF Check = $0 all projection years
3. NI Check = $0 all projection years (IS Net Income ties to CFS)
4. Waterfall Check = $0 all projection years
5. CFS End Cash = Debt Build Cash Target all projection years
6. CFS End Cash = BS Cash all projection years (cash tie-out)

If any check fails, diagnose and fix before proceeding. The most common failure mode is: BS Check ≠ 0 because the BS equity formulas were not patched (see Post-Wiring BS Equity Adjustment above).

The model is now circular (waterfall depends on CFS, CFS depends on waterfall) — this is expected and requires iterative calculation enabled in Excel.

---

## Formatting Notes

Defer to `firm-formatting` for all formatting rules. Key format decisions for this tab:

- **FCF** and **Cash Available for Allocation**: Major Totals (F2F2F2 fill, bold, Currency format)
- **Diluted Shares Outstanding**: Major Total (F2F2F2 fill, bold)
- **Waterfall Check**, **FCF less SBC**, **Ending Basic Shares**, **Total Dividends**, **M&A Portfolio Value**: bold subtotals
- All percentage rows (IRR, SBC %, DPS Growth %, Y/Y share change, % of NI ratios): italic
- Share counts: decimal format (0.0), not integer
- DPS and Buyback Price: per-share format ($#,##0.00)
- Section Boundary Rule applies within each sub-section for `$` placement
- Historical data cells: green font (cross-sheet pulls)
- Projection assumptions: blue font + yellow background
- Projection formulas: black font

---

## Key Rules

- **Buybacks are the residual plug** — computed directly against the Debt Build cash target, not from the waterfall. See Buyback Plug Formula.
- **Acquisitions are a capital allocation decision** — budget lives HERE, PP&E Build pulls from this.
- **Dividends are DPS-driven** — never a % of NI or FCF.
- **Buyback Price is hardcoded per year** — drives both buyback share count and SBC dilution. Separate from the investor's Entry Price on the Returns tab.
- **SBC section is the single source of truth** — feeds dilution and returns analysis.
- **Post-wiring BS equity patch is mandatory** — RE and CSAPIC/Treasury must be updated after wiring. See Post-Wiring BS Equity Adjustment.
- **CFS End Cash stays flow-based** — never override with a direct Debt Build pull. See CFS End Cash Architecture.
- **All 6 verification checks must pass.** See Verification Gate.
- **No freeze panes.**

---

## Common Failure Modes

1. **BS imbalance after wiring buybacks/dividends**: BS equity formulas (Retained Earnings, CSAPIC/Treasury Stock) were not updated to include the new CFS flows. See Post-Wiring BS Equity Adjustment.

2. **CFS End Cash diverges from cash target**: Buybacks were computed from the waterfall residual instead of the direct plug formula. A CFS line item (typically SBC withholding) was missing from the waterfall, causing buybacks to be oversized. See Buyback Plug Formula.

3. **CFS End Cash ≠ BS Cash**: CFS!Ending_Cash was overridden to pull from Debt Build instead of being computed as Beg + Net Change. See CFS End Cash Architecture.

4. **Waterfall Check ≠ 0 but BS balances**: The informational waterfall is missing a line item. This is cosmetic if the buyback plug formula is used, but should still be fixed for auditability. Common missing items: SBC withholding, FX effects, finance lease principal payments.

---

## Tab Completion Verification

Before reporting the Capital Allocation Build tab complete, read and paste:

```
TAB VERIFICATION -- Capital Allocation Build:
  freezePanes: [null -- if not null, STOP and fix]
  gridlines: [off -- if on, STOP and fix]
  font: [Arial 10pt -- if not, STOP and fix]
  column widths uniform: [yes/no]
  tab color: [hex] -- expected: #5B8FA8
  BS Check (all periods): [0 or value]
  CF Check (all periods): [0 or value]
  NI Check (all periods): [0 or value]
  Waterfall Check (all periods): [0 or value]
  CFS End Cash = Debt Build Target (all periods): [match/mismatch]
  CFS End Cash = BS Cash (all periods): [match/mismatch]
```

If you do not paste this output, the user cannot verify compliance. No output = not verified.

---

## Definition of Done (Phase 4)

A phase is complete if and only if ALL of the following are true. Report completion by reading these values back to the user -- not by summarizing in prose.

1. **Task Tracker**: Every subtask row for Phase 4 shows Status = "COMPLETE". Cite the actual cell addresses you checked.
2. **All 6 verification checks pass** (BS=0, CF=0, NI=0, Waterfall=0, CFS End Cash=Target, CFS End Cash=BS Cash). Read and report actual values for every projection period.
3. **Phase 3 placeholders re-linked**: Dividends, Buybacks, Acquisitions, Diluted Shares all reference Capital Allocation Build.
4. **Post-wiring BS equity adjustment** done: RE includes dividends, CSAPIC/Treasury includes buybacks.
5. **Iterative calculation enabled** and circular refs resolving.
6. **Tab Completion Verification** output pasted.
7. **Task Tracker Model State Block**: "Last Skill Run" updated, "Next Skill" = "build-model-phase-5".

If you write "Phase 4 complete" in chat before reading and reporting these values, you have made an error. Re-verify and correct.

**STOP. Report status. Wait for "continue."**
