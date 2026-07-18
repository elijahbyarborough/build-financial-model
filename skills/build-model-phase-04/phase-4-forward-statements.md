# Phase 4 — Forward Statement Build (Model View Projections)

**Prerequisite:** Phase 3 complete (Profit Build and BS & CFS Build populated with historicals + drivers, Driver Review passed).

---

## What Happens in This Phase

Project the Model View forward on the IS, BS, and CFS tabs by linking to the two build tabs populated in Phase 3. The statements carry the Model View only — historical columns link to Annual Historicals (done in Phase 1); this phase fills the projection columns.

1. **IS projections**: Pull revenue, costs, and EBITDA detail from the Profit Build. Pull D&A from the BS & CFS Build (PP&E & Capex section). Pull interest from the BS & CFS Build (Debt & Cash section). Pull tax from the Profit Build (Tax section). Compute EBITDA, EBIT, EBT, NI, EPS, margins.
2. **BS projections**: Pull PP&E from the BS & CFS Build (PP&E & Capex section), Goodwill, Intangibles, and the M&A Assets row from the BS & CFS Build (Goodwill & Intangibles section), debt and cash from the BS & CFS Build (Debt & Cash section), WC items from the BS & CFS Build (Working Capital section). Build the equity roll-forward (see BS — Equity Roll below). Compute totals and BS Check.
3. **CFS projections**: Pull NI from IS, D&A from the BS & CFS Build, WC deltas and noncurrent operating deltas from the BS & CFS Build (Working Capital section), DTL changes from the Profit Build (Tax section), capex from the BS & CFS Build, debt changes from the BS & CFS Build. Compute CFO, CFI, CFF, ending cash, CF Check.

---

### Step 0 — Verify Build Tabs Complete (Phase 3 Owns Them)

**Phase 3 built ALL forward projections on the build tabs** — revenue/costs, Debt & Cash (incl. lease schedules), PP&E & Capex (incl. FL adjustments), Working Capital, and Tax. This phase does NOT build or modify build-tab projections; it assembles the statements from them. Full build-tab methodology lives in Phase 3's meth files (`meth-debt-build.md`, `meth-ppe-build.md`, `meth-working-capital.md`, `meth-tax.md`, and `meth-lease-full.md` here).

Before assembling any statement, read and confirm on the build tabs (stop and return to Phase 3 if any fails):

1. Profit Build: every segment section projected through the terminal year; Consolidated P&L Bridge totals = SUM of segments
2. BS & CFS Build: Debt & Cash projected (incl. OL/FL schedules when lease flags set); PP&E & Capex projected (FL adjustments applied if `FL_IN_PPE=Y`); Goodwill & Intangibles projected (amortization schedule; M&A Assets = 0 placeholder); Working Capital projected for every mapped BS item
3. Profit Build Tax section: projected, with the Memo Pre-Tax Income row (= EBIT − Total Interest Expense + Interest Income, if any) populated

**Assembly order:** Income Statement → Balance Sheet → Cash Flow Statement.

---

## The CFS is the BS Bridge

In projections, every CFS line item is a delta from a BS item or a build-tab section. The assembly rule:

- **CFO non-cash items** = deltas from build-tab sections (ΔWC from the Working Capital section, ΔDTL from the Profit Build Tax section, ΔARO from the Working Capital section's noncurrent items, D&A from the PP&E & Capex section)
- **CFI** = negated capex from the PP&E & Capex section + acquisitions (set to 0 — placeholder for Phase 5)
- **CFF** = debt changes from the Debt & Cash section + dividends (0 placeholder) + buybacks (0 placeholder) — Phase 5 will re-link these
- **Cash = plug** (beginning + all flows = ending)

**If any BS item changes in projections and has no CF flow, the model is broken.** Consult the BS→CF mapping from Phase 1. Every mapped item must have its delta flowing through the CFS.

### CFS Memo Block — FCF Definitions (Phase 6 pulls these)

At the bottom of the CFS, build a three-row memo block (historical and projection columns):

1. **Levered FCF** `= CFO − Capex`
2. **Unlevered FCF** `= Levered FCF + After-Tax Interest Expense`, where After-Tax Interest = Total Interest Expense × (1 − Effective Tax Rate)
3. **FCF to Equity** `= Levered FCF + Net Debt Issuance / (Repayment)`

The Model Tab (Phase 6) pulls all three by name — label them exactly as above.

### CFS — Additional Required Lines (Phase 5 patch targets)

- **SBC Withholding (CFF)**: shares withheld for employee taxes on vesting. Historicals as reported; projections = 0 placeholder unless reported historically (Phase 5's buyback plug enumerates it).
- **FX Effect on Cash**: only if the company reports one. Historicals as reported; projections = 0 placeholder.

### BS — Equity Roll (Phase 5 Will Re-Patch)

Build the equity section as an explicit roll-forward so Phase 5's patch targets exist:

- **Retained Earnings** `= Prior RE + IS!Net Income + Dividends (0 placeholder)`
- **Common Stock & APIC** `= Prior CS&APIC − IS!SBC + SBC Withholding (0 placeholder) + Buybacks (0 placeholder)` — with SBC stored as a negative expense on the IS, `−IS!SBC` is the positive equity credit
- **Treasury-stock companies**: buybacks accumulate in a negative **Treasury Stock** row instead (`= Prior Treasury + Buybacks`); CS&APIC then excludes the buyback term

---

### Lease & PP&E Projection Verification (built in Phase 3 — verify, don't rebuild)

When lease flags are set, spot-verify the Phase 3 build-tab projections before wiring statements (methodology: `meth-lease-full.md` here, `meth-ppe-build.md` / `meth-debt-build.md` in Phase 3):

- OL schedule: ROU and Total Liability roll-forwards formula-driven; Current Liability formula-driven (never a static carry-forward)
- FL schedule: Interest = Beginning Liability × Implicit Rate (beginning, never average); Cash Payment = Depreciation + Interest; Liability roll ties
- PP&E & Capex if `FL_IN_PPE=Y`: "Other" capex references New FL Additions (same tab); D&A % driver EXCLUDES FL depreciation historically; projected Total D&A = Operating D&A + FL Depreciation
- If `FL_IN_PPE=N`: PP&E section clean; FL depreciation pulled separately to the CFS from the Finance Lease Schedule

---

## Reconciliation Checks: Historical Only

The statements carry the Model View only. The as-reported record lives on the Annual Historicals tab, so the reconciliation checks prove the Model View consolidation lost nothing:

- **IS**: Model NI = Annual Historicals reported NI
- **BS**: Model Total Assets = Annual Historicals reported Total Assets
- **CFS**: Model Ending Cash = Annual Historicals reported ending cash

These checks apply to **historical periods only** — Annual Historicals has no projections to compare against. All must equal $0 difference.

---

## Capital Allocation Placeholders (Phase 5 Will Overwrite)

This phase uses zero/flat placeholders for all items that depend on the Capital Allocation Build, which does not exist yet:

- **Dividends (CFF)**: set to 0 in all projection years
- **Share Repurchases (CFF)**: set to 0 in all projection years
- **Acquisitions (CFI)**: set to 0 in all projection years
- **SBC Withholding (CFF)**: 0 in projection years (unless reported historically — historicals as reported)
- **FX Effect on Cash**: 0 in projection years (only present if the company reports one)
- **Diluted Share Count**: hold flat at most recent historical diluted share count
- **M&A Assets (BS)**: 0 in all projection years (Phase 5 re-links to Cumulative M&A Invested Capital)
- **Equity roll terms**: the Dividends term in Retained Earnings and the SBC Withholding / Buybacks terms in CS&APIC (or Treasury) reference the 0-placeholder CFS lines above

Phase 5 (Capital Allocation) will overwrite all of these with live formulas linking to the Capital Allocation Build tab. EPS, CFS, and BS update automatically once the links are in place.

---

### IS — Lease Expense Flows

- **Operating lease cost**: Flows to operating expenses (inside SG&A, COGS, or as a separate "Lease Expense" line, matching the company's reported presentation)
- **Finance lease depreciation**: Included in total D&A expense. If `FL_IN_PPE=Y`, this is already inside the PP&E & Capex section's D&A. If `FL_IN_PPE=N`, pull separately: `Total IS D&A = PP&E section D&A + FL Depreciation from the Finance Lease Schedule`
- **Finance lease interest**: Included in total interest expense. Pull from the Debt & Cash section: `='BS & CFS Build'!FL_Interest`. Add to any other interest (term loan, revolver, etc.) for total interest expense.

### BS — Lease Line Items

**Assets**:
- **Operating Lease ROU Assets**: From the Working Capital section (sourced from the Operating Lease Schedule on the same tab). Placed per Lease BS Map OL_ROU_ASSET_NC.
- **Finance Lease ROU Assets**: If FL_IN_PPE=Y, already inside Net PP&E. If FL_IN_PPE=N, placed per Lease BS Map FL_ROU_ASSET_NC (own line or through the Working Capital section into host line).
- **Net PP&E**: From the PP&E & Capex section. Includes FL ROU only if `FL_IN_PPE=Y`.

**Liabilities**:
- **Finance Lease Current Liability**: Per Lease BS Map FL_LIAB_C — either its own BS line or embedded in the mapped host line via Working Capital section disaggregation.
- **Finance Lease Noncurrent Liability**: Per Lease BS Map FL_LIAB_NC — either its own BS line or embedded in the mapped host line via Working Capital section disaggregation.
- **Operating Lease Liabilities**: Per Lease BS Map OL_LIAB_C and OL_LIAB_NC — typically their own dedicated BS lines, sourced from the Working Capital section (which pulls from the Operating Lease Schedule).

### CFS — Lease Cash Flow Rules (HARD STOP)

**CF from Operating Activities**:
- **D&A add-back (sign-critical)**: Includes finance lease depreciation.
  - If `FL_IN_PPE=Y`: `D&A addback = -'BS & CFS Build'!Total_D&A` — Total D&A already INCLUDES FL depreciation, and the PP&E & Capex section stores D&A as NEGATIVE, so the leading minus yields a positive add-back. Do NOT add FL depreciation again.
  - If `FL_IN_PPE=N`: `D&A addback = -'BS & CFS Build'!Total_D&A + 'BS & CFS Build'!FL_Depreciation` — FL depreciation is stored POSITIVE in the Finance Lease Schedule, so it is ADDED (plus, not minus).
- **Working capital changes**: Include the net change in operating lease balances ONLY (Change in OL ROU - Change in OL Liability — zero in projections by construction). **FL liability changes must NOT flow through working capital**: the FL principal reduction is already the CFF finance lease payment line, and new FL additions are non-cash. Routing FL liability deltas through WC double-counts principal and injects a non-cash inflow.

**CF from Financing Activities**:
- **Finance lease payment**: `= -'BS & CFS Build'!FL_Depreciation` (the DEPRECIATION row, which equals the principal portion)
- **NEVER pull from the total payment row** (Depreciation + Interest). The interest component is already inside Net Income, which flows through CFO. Pulling total payment double-counts interest as a cash outflow.
- **This is the #1 lease modeling error.** If the BS check is non-zero by approximately the amount of FL interest expense, check this line first.

**Verification after CF assembly**:
1. Confirm FL financing payment = principal only (matches depreciation, not total payment)
2. Confirm D&A add-back includes FL depreciation
3. Confirm BS Check = 0 across all projection years

---

## Integrity Checks

All checks must pass for **every period** (historical AND projected):

1. **BS Check = $0**: Total Assets - Total L&E = 0
2. **CF Check = $0**: Beginning Cash + all CF - Ending Cash = 0
3. **NI ties IS→CFS**: Net Income on IS = Net Income on CFS
4. **RE roll-forward**: Beginning RE + NI - Dividends = Ending RE
5. **EBT wiring check**: IS EBT = Profit Build Memo Pre-Tax Income (= EBIT − Total Interest Expense + Interest Income, if any). The Tax section computed tax off its memo EBT; if the assembled IS EBT diverges, the tax line is stale — re-check interest (and interest income) wiring

If any check fails in projections, the most likely cause is a BS item changing without a corresponding CF flow. Use the BS→CF mapping to diagnose — find the BS line that changed and trace whether its delta appears on the CFS.

**Freeze panes at `B5` on IS/BS/CFS (Freeze Pane Standard). All formatting defers to `firm-formatting`.**

---

## Tab Completion Verification

Before reporting any tab's forward projections complete, read and paste:

```
TAB VERIFICATION -- [Tab Name] (Forward Projections):
  freezePanes: [expected value per the firm-formatting Freeze Pane Standard -- STOP and fix if wrong]
  gridlines: [off -- if on, STOP and fix]
  font: [Arial 10pt -- if not, STOP and fix]
  column widths uniform: [yes/no]
  BS Check (all periods incl projections): [0 or value]
  CF Check (all periods incl projections): [0 or value]
```

If you do not paste this output, the user cannot verify compliance. No output = not verified.

---

## Definition of Done (Phase 4)

A phase is complete if and only if ALL of the following are true. Report completion by reading these values back to the user -- not by summarizing in prose.

1. **Task Tracker**: Every subtask row for Phase 4 shows Status = "COMPLETE". Cite the actual cell addresses you checked.
2. **BS Check = 0** for ALL periods (historical AND projected). Read and report actual values.
3. **CF Check = 0** for ALL periods (historical AND projected). Read and report actual values.
4. **NI ties IS→CFS** for all projection periods. Read both cells and confirm match.
5. **RE roll-forward** holds for all projection periods.
6. **All build-tab deltas wired to CFS**: every BS item with a build-tab section has its delta flowing through CFS.
7. **EBT wiring check passes**: IS EBT = Profit Build Memo Pre-Tax Income for all projection periods. Read both rows and confirm.
8. **Capital allocation placeholders** in place (dividends=0, buybacks=0, acquisitions=0, SBC withholding=0, FX=0, shares=flat, M&A Assets=0, equity roll terms wired to the placeholder lines).
9. **Tab Completion Verification** output pasted for IS, BS, CFS.
10. **Task Tracker Model State Block**: "Last Skill Run" updated, "Next Skill" = "build-model-phase-05".

If you write "Phase 4 complete" in chat before reading and reporting these values, you have made an error. Re-verify and correct.

**STOP. Report status. Wait for "continue."**
