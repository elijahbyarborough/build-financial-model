# Phase 4 — Capital Allocation

**Reference:** `references/phase-4-capital-allocation.md`
**Prerequisite:** Phase 3 complete (IS/BS/CF projected, all checks pass, CFO exists).
**Next phase:** Phase 5 (Model Tab) → load `references/phase-5-model-tab.md`

---

## What Happens in This Phase

Build the Capital Allocation tab — a continuous cash waterfall from CFO to capital allocation decisions. Plus share count logic and debt decisions. Then re-link every placeholder from Phase 3.

1. **Cash waterfall**: CFO → less Capex → FCF → net debt → cash available → allocation decisions
2. **Debt decisions**: Net Debt Issuance / (Repayment) — debt is an input decided here
3. **Buyback modeling**: buybacks are ALWAYS the residual — absorb all remaining cash
4. **Dividend modeling**: DPS × beginning shares in projections
5. **Share count**: beginning → buyback shares → ending basic → diluted via TSM
6. **Re-link Phase 3 placeholders** (see below)
7. **Waterfall check**: Cash Available + Acquisitions + Dividends + Gross Share Repurchases = 0

---

## Re-Link Phase 3 Placeholders (REQUIRED)

Phase 3 set dividends, acquisitions, share repurchases, and diluted share count to zero/flat placeholders. After completing the waterfall and share count logic above, **replace every placeholder with a live formula linking to this tab**:

| Placeholder Location | Link To |
|---------------------|---------|
| CF tab → Dividends (CFF) | `='Capital Allocation Build'!Dividends` |
| CF tab → Share Repurchases (CFF) | `='Capital Allocation Build'!Share Repurchases` |
| CF tab → Acquisitions (CFI) | `='Capital Allocation Build'!Acquisitions` |
| IS tab → Diluted Share Count | `='Capital Allocation Build'!Diluted Shares` |
| PP&E Build → Acquisitions row | `='Capital Allocation Build'!Acquisition Budget` (sign-flipped) |

**After re-linking, re-verify all integrity checks** (BS Check = 0, CF Check = 0, NI ties, RE roll-forward) for every projection year. The model is now circular (waterfall depends on CF, CF depends on waterfall) — this is expected and requires iterative calculation enabled in Excel.

---

## Waterfall Flow (Top to Bottom)

- CFO (pulled from CF tab)
- Less: CapEx (from PP&E Build, negated)
- = Free Cash Flow
- Other Investing
- Net Debt Issuance / (Repayment)
- Other Financing
- Target Change in Cash Balance (assumption, default = 0)
- = **Cash Available for Capital Returns**
- Acquisitions (inline assumption — owned HERE)
- Dividends (DPS × Beginning Shares)
- **Gross Share Repurchases (RESIDUAL)**
- Less: SBC dilution offset
- = Net Share Repurchases
- **Waterfall Check** = 0

---

## Share Count

- Beginning basic shares
- Shares repurchased: `|Net Buyback $| / Entry Price` (Entry Price pulled from Returns tab)
- Ending basic: `Beginning - Repurchased`
- Diluted: `Basic + Dilutive impact via TSM`
- TSM: `In-the-Money Options × (1 - Exercise Price / Stock Price)`
- Diluted shares feed back to IS for EPS

---

## Key Rules

- **Buybacks are the residual** — never an assumption.
- **Acquisitions are a capital allocation decision** — budget lives HERE, PP&E Build pulls from this.
- **Waterfall check must = 0.** Debug before proceeding.
- **No freeze panes.**

**STOP. Update Task Tracker. Report waterfall check. Wait for "continue."**
