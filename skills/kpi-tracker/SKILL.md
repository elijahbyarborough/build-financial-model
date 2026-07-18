---
name: kpi-tracker
description: "Build or update a quarterly KPI tracker tab on a financial model. First asks whether to create a new tab or update an existing one, then routes accordingly. For new tabs - reasons over earnings releases / investor decks to identify the most important KPIs, proposes a full KPI list for user review, then builds. For updates - takes the latest release, identifies the target column, flips the period label from E to A, shifts the forecast divider, and writes the new actuals. Industry-aware across commodities, rental, software, payments, retail, alt asset managers, banks, insurers, etc."
---

# KPI Tracker — Quarterly KPI Tab Builder & Updater

Build or update a quarterly KPI tracker tab on a financial model. The output is a 1–2 pager that a buyside analyst can scan to track quarterly performance against history and guidance.

## When to use

- User asks for a "KPI tab", "KPI tracker", "quarterly tracker", "scorecard", "KPI 1-pager", or "key metrics" tab on an existing model
- User says "update the KPI tab", "add the latest quarter to the tracker", "roll forward the KPIs"
- User uploads an earnings release / press release / 10-Q and wants the salient metrics organized into a quarterly tracker
- User says "track this company" or "build me a quarterly view of [company]"

## Required skill load

**ALWAYS load `firm-formatting` at the start of this workflow** (the copy in this plugin). Do not attempt to format from memory — the firm-formatting skill is the single source of truth for fills, fonts, borders, number formats, and color coding, and the kpi-tracker spec below assumes it is loaded. Loading it after you've already started building leads to drift and rework.

## Two operating modes (determines sourcing rules)

Detect the mode FIRST — it changes how historical values are sourced:

- **Plugin-model mode** — the workbook is an FC model built by the `build-model` phase system (a **Quarterly Historicals** tab exists). Historical actuals are pulled LIVE from Quarterly Historicals via cross-sheet formulas (green #008000 per firm-formatting). Do NOT hardcode historicals onto this tab: Quarterly Historicals is the model's single hardcode layer, and duplicating hardcodes here would fork the record. Intra-tab derived formulas (margins, Y/Y, FY derivations) stay live as always. Post-earnings updates land on Quarterly Historicals first; this tab refreshes through its links.
- **Standalone mode** — any other workbook (no Quarterly Historicals tab). Follow the portability rules below: build with formulas, then convert cross-sheet refs to hardcoded values before declaring done.

## Step 0 — FIRST QUESTION: Create new or update existing?

**Before anything else**, ask the user (use `ask_user_question` tool):

> "Do you already have a KPI tab on this model, or do you want me to create a new one?"
> - **Create new** — build a KPI tab from scratch
> - **Update existing** — add the latest quarter to an existing KPI tab

Branch on the answer:
- "Create new" → go to **Track A: Build new tab** below
- "Update existing" → go to **Track B: Update existing tab** below

---

## Track A — Build new tab

### A1. Required inputs (ASK BEFORE PROCEEDING)

**Do not proceed until both inputs are provided or explicitly waived.**

Ask the user:

1. **Reference tab in the workbook** — in plugin-model mode this is ALWAYS the **Quarterly Historicals** tab (do not ask; read its column-to-period mapping directly). In standalone mode: is there an existing tab (e.g., "Model", "Multiple Periods", a data tab) that holds quarterly historical data the KPI tab can pull from? If so, which tab and what's the column-to-period mapping convention? If not, the skill will hardcode all values from the release.

2. **Most recent earnings release / press release / investor deck** — upload the latest earnings release PDF (and the IR / investor deck if available). Without it, the skill cannot reason about which KPIs management curates or populate the most recent quarter's actuals correctly. **Refuse to build the tab if no release is available** — instead, ask where to retrieve it (EDGAR, IR website, manual upload).

If the user wants prior quarters back N years, ask whether they want the skill to retrieve them via EDGAR (using `edgar-retriever` skill) or whether they'll upload them individually.

Confirm:
- Time horizon (default: quarterlies back 4 years + FY columns)
- Tab placement (default: right after Task Tracker / first input tab)

Skip to A2 ONLY when these are answered.

### A1.5. Forward projection columns

**Always project forward quarterlies past the last actual through the end of the current FY + a full forward FY.** A KPI tab that stops at the last reported quarter is half-built — analysts need a placeholder for the next several quarters and the next full year so guidance, consensus, and our forecasts have somewhere to land.

Use the last actual quarter to determine which forward columns to add:

| Last actual | Forward columns to add |
|---|---|
| FQ4 of year N | FQ1–FQ4 of year N+1 + FY N+1 |
| FQ3 of year N | FQ4 of year N + FQ1–FQ4 of year N+1 + FY N + FY N+1 |
| FQ2 of year N | FQ3–FQ4 of year N + FQ1–FQ4 of year N+1 + FY N + FY N+1 |
| FQ1 of year N | FQ2–FQ4 of year N + FQ1–FQ4 of year N+1 + FY N + FY N+1 |

Conventions:
- **Guidance mid-point placement**: quarterly guidance lands in the next unreported quarter's column (typically Q1 of the next forward year, so it renders next to the freshly-flipped actual); full-year guidance lands in the guided FY column. Both may be populated when the company guides both ways — see the Guidance section spec.
- **Forward FY columns** are `SUM` (flow items) / `AVG` (weighted-avg items) / last-Q (period-end balances) formulas — see the FY Derivation Matrix.
- **Forecast divider** sits at the FIRST forecast column (medium left border `#212529`, weight Medium, spanning row 4 through last data row).

### A2. Read the earnings release / investor deck

Use the code-execution container to extract PDF text:
```bash
pdftotext -layout /files/input/.../release.pdf /tmp/release.txt
pdftotext -layout /files/input/.../deck.pdf /tmp/deck.txt
```
Read the extracted files carefully. Pay attention to:
- **Page-1 financial highlights bullets** — these ARE the KPIs management curates. 5–8 bullets typically.
- **Highlights / summary table** on page 1 or 2 — formal definition of headline metrics.
- **Segment results** — number of segments, what gets reported per segment.
- **Non-GAAP reconciliations / appendices** — confirms which "adjusted" versions matter.
- **Outlook / Guidance section** — what management commits to.
- **Capital allocation paragraphs** — leverage, buybacks, dividends, M&A.
- **Investor deck KPI slides** — useful to confirm what management trends.

If the user pointed to a reference tab, also inspect that tab's structure (column headers, row labels, period mapping).

### A3. Reason over KPIs using these lenses (in order)

1. **What does management lead with?** Page-1 bullets and the highlights table are non-negotiable.
2. **What's the buyside bull/bear pivot for this stock?**
3. **What does the model's revenue/cost build emphasize?** Look at the workbook's segment build tabs and reference tab — the top driver rows ARE the KPIs.
4. **Industry archetype heuristics** — use these to expand beyond what the release explicitly shows:

| Archetype | Distinctive KPIs to expect |
|---|---|
| **Industrial commodities** (cement, aggregates, chemicals) | Volumes (tons / units), price ($/unit), cash GP/unit, freight-adjusted metrics, mix-adjusted price |
| **Equipment rental** | Fleet productivity %, average OEC, time utilization, rental rate growth, rental gross margin, used equipment sales & margin |
| **Diversified software / industrial tech** | Organic revenue growth %, M&A contribution %, EBITDA, EBITDA margin, **core margin Δbps** (organic-only margin change), TTM FCF, leverage |
| **Payments networks** (Visa, Mastercard) | Payments volume YoY% (constant currency), cross-border volume YoY% (incl/excl intra-region), processed transactions, revenue mix by line, client incentives (contra-rev) |
| **Retail / consumer** (paint stores, restaurants, retail) | Same-store sales % / comp sales %, traffic, ticket, FX impact %, M&A contribution %, segment-level adjusted profit/margin |
| **Alt asset managers / asset managers** | Adj. Net Income / DE, Fee-Related Earnings (FRE), Spread-Related Earnings (SRE), FRE margin, FRE comp ratio, Total AUM, Fee-Generating AUM, Perpetual Capital AUM, Inflows, Origination, Gross capital deployment, Net Spread bps |
| **Banks** | NII, NIM, fees, efficiency ratio, NCO ratio, NPL ratio, ACL/loans, CET1, ROE, ROTCE, deposit beta |
| **Insurers** | Premiums earned, combined ratio (loss + expense), reserve development, NII, book value/share |
| **Healthcare/biotech** | Patient volumes, ASP, MLR, revenue by therapy area, R&D %, gross-to-net |
| **Industrial / multi-segment manufacturers** | Organic growth %, FX, M&A, segment EBIT, segment margin, backlog, book-to-bill |

5. **Universal sections every tracker should have:**
   - Headline financials (revenue, profit, EBITDA, EPS — GAAP + Adjusted)
   - Industry-specific operating drivers
   - Segment block (one per reported segment)
   - Capital allocation (capex, FCF, buybacks, dividends, leverage, ROIC)
   - Balance sheet snapshot (cash, debt, total assets)
   - Guidance / Outlook

### A4. Present the proposed KPI list (CHECKPOINT — REQUIRED)

**Do NOT start building the tab yet.** Present the full proposed list organized by section header so the user can add or remove items.

Format:

```
Here's the proposed KPI list for [Company] based on the [release name] (and [deck name] if applicable). Review and let me know what to add or remove.

### Headline Financials
- Total Revenue ($mm)
  - Y/Y Revenue growth, %
- Gross Profit ($mm)
  - Gross Margin, %
- SG&A ($mm)
- Adj. EBITDA ($mm)
  - Y/Y Adj. EBITDA growth, %
  - Adj. EBITDA Margin, %
  - Y/Y Margin Δ, bps
  - Y/Y Incremental Adj. EBITDA Margin, %
- Adj. EBIT ($mm)
  - Adj. EBIT Margin, %
- Adj. Net Income ($mm)
- Diluted Shares Outstanding (mm)
- GAAP Diluted EPS ($)
- Adj. Diluted EPS ($)
  - Y/Y Adj. EPS growth, %

### [Segment 1 Name]
- ...

### Capital Allocation
- Total Capex ($mm)
- CFO ($mm)
- Free Cash Flow ($mm)
- Cash for Acquisitions ($mm)
- Dividends Paid ($mm)
- Share Repurchases ($mm)

### Balance Sheet
- Cash & Equivalents ($mm)
- Total Debt ($mm)
- Net Debt ($mm)
- Net Debt / TTM Adj. EBITDA (x)
- Total Assets ($mm)

### Guidance
- [Item 1] — [unit, range or point]
- ...

Want to add/remove anything before I build the tab?
```

Wait for user response. Adjust per their feedback. Re-confirm if substantive changes.

### A5. Build the tab

Once user approves, build per these specs (full tab structure spec — see "Tab Construction Spec" section below).

### A6. Run final review

Run this skill's own "Final Review Checklist" (below) — NOT the generic firm-formatting section self-check, which would fail this tab by design (the KPI Tracker's no-border/no-bold body and breather rows are deliberate, firm-sanctioned deviations). Firm palette, Arial 10pt, number formats, and color coding still apply and are covered by the checklist below.

---

## Track B — Update existing tab

### B1. Ask for the new release

> "Please upload the latest earnings release PDF (and investor deck if available). I'll use it to populate the most recent quarter."

**Refuse to proceed without a release** — without it, you can't update actuals.

If the user mentions multiple periods to backfill, ask whether to retrieve via EDGAR or upload one at a time.

### B2. Inspect the existing KPI tab

Read the current tab to identify:
- Row 4 period labels (e.g., `FQ1 2022A` ... `FY 2026E`)
- The column where the forecast divider currently sits (medium left border)
- Which columns are A (actuals) vs E (estimates)
- Row map (label column A — section headers, KPI row numbers)

### B3. Identify the target column and confirm with user

Determine which column should receive the new actuals:
- The new quarter's actuals go into the **first column currently labeled E** for that quarter (e.g., if release is for Q2-26 and column X is currently `FQ2 2026E`, then X is the target)
- The forecast divider currently sits to the LEFT of that column; after update it should move to the LEFT of the NEXT estimate column (one column to the right)

**Confirm with user before writing**:

> "I plan to:
> - Update column **[X]** (currently `FQ2 2026E`) with Q2-26 actuals from the [release name]
> - Flip the column label from `FQ2 2026E` → `FQ2 2026A`
> - Move the forecast divider one column right (now between `FQ2 2026A` and `FQ3 2026E`)
>
> Confirm before I proceed?"

If multiple quarters are being backfilled (e.g., user uploads Q1 + Q2), repeat the confirmation listing each target column.

### B4. Read the new release and extract values

Use `pdftotext -layout` to extract the release. Map each KPI in the existing tab to a value in the release. For metrics not disclosed in the quarterly release (e.g., segment-level capex, geographic revenue mix sometimes), leave existing values untouched and note these gaps in the chat reply.

### B5. Write actuals to the target column

For each KPI row:
- Write the new value to the target column cell
- Apply blue text color (`#0000FF` — hardcoded)
- Add a cell note on the anchor cell of each section: `"Source: [Company] [Period] Earnings Release, [Date], [Table/Section]"`

For derived rows (margin %, Y/Y growth, per-unit) — DO NOT overwrite. The existing formulas should compute correctly off the new $ inputs. After writing, verify a few computed values by spot-checking against the release (e.g., Adj EBITDA margin should match what the release reports).

### B6. Flip period label E → A

Update row 4 label for the target column from `FQN YYYYE` → `FQN YYYYA`.

**Fiscal-year close (FQ4) branch**: when the flipped quarter is FQ4, the fiscal year is now fully actual — ALSO flip the FY column's label `FY YYYYE` → `FY YYYYA` and verify its derivation formulas resolve to actuals (per the FY Derivation Matrix).

### B7. Move forecast divider right

Find the column with the existing medium left border (`#212529`, weight Medium). Clear that border, then re-place it on the first column that is still a forecast:

- **FQ1–FQ3 flipped**: the next column to the right (the following quarter).
- **FQ4 flipped**: TWO columns right — past the now-actual FY column — so the divider lands on the first forecast quarter of the next year. Never leave the divider on a fully-actual FY column.

The divider spans row 4 through the last data row. Rows 1–3 (title/units area) stay clean.

### B8. Update FY column derivation (if applicable)

If the FY column for the year being updated contains formulas (`=SUM(W:Z)` etc.), they should auto-update. If anything broke (e.g., a row had a hardcoded FY value instead of a formula), re-apply the FY derivation matrix (see "FY Derivation Matrix" below).

### B9. Report back to user

Concise summary:
- Column updated
- Label flipped
- Divider moved
- List of any KPIs not disclosed in the quarterly release that were left unchanged
- Any spot-checks done

---

## Tab Construction Spec (used by Track A)

### Header structure (rows 1–6 — KPI Tracker layout, a registered variant of the firm standard; do not apply to other tabs)

| Row | Content | Border |
|---|---|---|
| 1 | `[Company Name] (TICKER) – Quarterly KPI Tracker` — navy fill `#1C3553`, white bold Arial 10, **MERGED A:[last col]** | None |
| 2 | `($ in millions, except per-unit and per-share data)` — italic, no fill | None |
| 3 | Blank spacer | None |
| 4 | Period labels: `FQ1 2022A`, ..., `FY 2026E` — bold black, centered, Arial 10 | **Medium bottom border on `A4:[lastcol]4` — FULL width including label column A and spacer column B**, per firm-formatting's Horizontal Border Extent (matches every other tab's year-row border) |
| 5 | Period-end dates: `03/31/22`, etc., format `MM/DD/YY` (firm header-date format), centered, regular Arial 10 | **NO border anywhere on row 5.** |
| 6 | Blank spacer | None |
| 7+ | First section header (Tier 1) | — |

**Freeze panes at `B6`** (per the firm-formatting Freeze Pane Standard): column A (labels) and the 5-row header block (title, units, spacer, period labels, dates — rows 1–5) stay pinned as you scroll. This is the same `B6` variant the Model Tab uses (both have a header one row longer than the Standard Tab Header). Gridlines off.

### Row heights and column widths

| Element | Spec |
|---|---|
| All row heights | **15pt uniform** across every row |
| Col A (labels) | ~240pt (~30 chars) |
| Col B (spacer) | ~8pt |
| Data cols (C onward) | Excel column width **9.5** ≈ 63pt in Office.js `columnWidth` |

These widths keep the tracker on a single landscape page and let column A hold the longest KPI labels (e.g., "Y/Y Incremental Adj. EBITDA Margin, %") without truncation.

### Font enforcement

**Office.js merges sometimes revert font to Calibri.** This bites the row 1 title band most often, but any large-range write or merge can do it. After every merge or large write, **explicitly reapply Arial 10 across the full used range** (and reapply white + bold for the row 1 navy band). Verify with a spot-check `read` on a few cells before declaring the tab done.

### Period column structure

- 4 quarters + FY total per year, repeating: C–G (Q1–Q4 + FY 2022), H–L (Q1–Q4 + FY 2023), M–Q, R–V, W–AA. Use 5-column groups consistently.
- Forecast quarters use suffix `E`; actuals use `A`.

### Spacing & Indentation Standard (CANONICAL)

This is the single source of truth for vertical spacing and indentation on every KPI tab. The rhythm is **header → blank → tight sub-block → blank → tight sub-block → blank → next header**. The blank row above every section header is what creates the breathing room between sections.

#### Vertical spacing

| Boundary | Blank rows |
|---|---|
| Row 6 (post-header band) → first section header (row 7) | 1 blank row (row 6) |
| Section header row → first data row | **1 blank row** between them |
| Within a section, between sub-blocks (e.g. Revenue block → OI block → Net Income block) | 1 blank row |
| Within a sub-block (a `$` line + its derived `Y/Y %`, `margin %`, `bps Δ`, `ex-FX %`, `incremental margin`) | **0 blank rows** — kept tight as a unit |
| Last data row of section → next section header | 1 blank row |

A "sub-block" = one primary `$` (or count) line plus all of its derived sub-rows. Keep sub-blocks tight; separate sub-blocks with a single blank row.

**Why the breather row below every section header.** The blank row between a navy header band and the first data row is REQUIRED on KPI tracker tabs, even though the firm-formatting default is "no spacer below section headers." Two reasons: (a) data rows packed directly under the navy band feel cramped, and (b) the FY gray fill rectangle reads better with a leading blank row at the top. The breather row itself has no fill in non-FY columns, but the FY columns (G, L, Q, V, AA, AF) still get `#F2F2F2` per the FY column emphasis rule below — so the FY gray rectangle stays continuous from breather → data rows → trailing spacer.

#### Indentation

Use Excel's **real indent feature** (`range.format.indentLevel`), never leading spaces.

| Row type | Indent level |
|---|---|
| Section headers (navy band) | 0 |
| Primary `$` / count / balance line (e.g. `WW Net Sales ($mm)`, `Operating Income ($mm)`, `Cash & Equivalents ($mm)`) | 0 |
| Derived sub-rows beneath a primary line — Y/Y growth %, margin %, bps Δ, ex-FX %, incremental margin %, mix % | **1** |
| Sub-segment / line-item primary rows (e.g. product lines under "Revenue by Product/Service Line", individual debt instruments under a debt block) | 0 — these are primary lines, not derivatives |

Apply the indent across the FULL row width (`A:[lastcol]`), not just column A, so the data values shift in lockstep with the label.

#### Bold

**No bold anywhere in the body.** Bold lives ONLY on:
- Row 1 title band
- Row 4 period labels
- Section header bands (navy, white text)

Major Total rows (Total Capex, FCF, Net Debt, Total Assets) stay regular weight — emphasis comes from the FY gray fill and the section's indentation hierarchy, not from bold or borders.

#### Borders

**No borders anywhere in the body.** The only borders allowed on a KPI tab:
- Medium bottom border on `A4:[lastcol]4` (under period labels — full width from column A, per firm Horizontal Border Extent)
- Forecast divider: medium left border (`#212529`, weight Medium) on the first forecast column, spanning row 4 through last data row

That's it. No row 5 top border, no thin top borders on Major Totals, no internal section borders, no FY-column edge borders. The visual hierarchy comes from the navy section bands, FY gray fill, indentation, and italics — not from borders.

### Major Total rows

(e.g., Total Capex, Total Assets, FCF, Net Debt)
- Regular weight, no border (per the no-bold-in-body and no-borders-in-body rules in the canonical spec above)
- Emphasis comes from the FY gray fill in columns G/L/Q/V/AA/AF and the section's indentation hierarchy (Major Total at indent 0, the components feeding into it at indent 1)
- **No** light-gray fill on quarter columns (only on FY columns — never as a row band)

### FY column emphasis

Apply `#F2F2F2` light-gray fill to FY columns on **EVERY row from one row below a section header through one row before the next section header** — including spacer rows inside the section AND the trailing spacer row before the next section. The fill should form a clean continuous rectangle in each FY column for each section block.

**Column letters (G, L, Q, V, AA, AF) are illustrative** — they assume the default layout (labels in A, spacer in B, data starting at C, 4 back-years + forward columns in 5-column Q1–Q4+FY groups). Re-derive the actual FY column letters from your tab's real start column and period count; never apply the literal letters to a different geometry.

**DO NOT stop the fill at the last data row** — the trailing spacer must also be filled, otherwise the bottom edge of every section looks ragged.

Section header navy still wins on the header row itself. Bold the FY year label on row 4.

### Forecast / Actual divider

Apply medium left border (`#212529`, weight Medium) to the column representing the first forecast period. Spans rows 4 through last data row. Stops before row 4 (rows 1–3 stay clean).

### Number formats (firm spec, with `_)` padding)

| Type | Format string |
|---|---|
| Currency ($mm) | `$#,##0_);($#,##0);"-"` |
| Number (no $) | `#,##0_);(#,##0);"-"` |
| Percentage | `0.0%_);(0.0%);"-"` |
| Per-share | `$#,##0.00_);($#,##0.00);"-"` |
| Per-unit ($/ton, $/cy) | `$#,##0.00_);($#,##0.00);"-"` |
| Multiple / leverage (x) | `0.0"x"_);(0.0"x");"-"` |
| Bps | `#,##0_);(#,##0);"-"` |
| Integer (volumes in 000s) | `#,##0` |
| Year | `@` (text) |
| Date | `MM/DD/YY` (header-date rows) |

### Color coding

- Hardcoded inputs → blue text (`#0000FF`)
- Formulas → near-black (`#212529`)
- Section headers → white on navy
- Cross-sheet refs: **plugin-model mode** → green (`#008000`) live links to Quarterly Historicals; **standalone mode** → no green, convert cross-sheet links to hardcoded values for portability
- **No yellow highlights** on assumption cells — guidance mid-points stay blue text + cell note only
- Bold ONLY on: row 1 title, row 4 year labels, section headers

### Hardcoding cross-sheet references (STANDALONE MODE ONLY — required for portability)

**Skip this section entirely in plugin-model mode** — there, historicals stay as live green links to Quarterly Historicals, which is the model's single hardcode layer.

In standalone mode, historical actuals pulled from a reference tab (e.g., `=Model!BU7`) must be **CONVERTED TO HARDCODED VALUES** before declaring the tab done. The KPI tab is meant to be portable — a buyside analyst should be able to copy it to another workbook or share a snapshot without breaking links.

Workflow:

1. **Build with formulas first** (faster — pulls live data from the reference tab)
2. **Verify values tie to the release**
3. **Convert all `=Model!...` formulas to hardcoded values** (read the computed value, write it back as a literal)
4. **KEEP formulas only for derived calculations that reference cells WITHIN the KPI tab itself**: margins, Y/Y growth, FCF (CFO − Capex), Total Capital Return (Dividends + Buybacks), Net Debt (Total Debt − Cash). These stay live so any future hardcode update flows through the math.
5. **Apply blue text** (`#0000FF`) to the now-hardcoded historical cells; computed/formula cells stay default near-black (`#212529`).

In standalone mode this applies to BOTH Track A (new build) and Track B (update existing).

## FY Derivation Matrix (CRITICAL)

| Row type | FY method |
|---|---|
| $ flows (revenue, GP, EBITDA, EBIT, NI, capex, $ COGS) | `=SUM(Q1:Q4)` |
| Volume / unit counts (tons, transactions, etc.) | `=SUM(Q1:Q4)` |
| Period-end balances (cash, debt, total assets, headcount) | `=Q4` (last quarter only) |
| Diluted shares outstanding (weighted avg) | `=AVERAGE(Q1:Q4)` |
| Per-share metrics (EPS, DPS) | `=SUM(Q1:Q4)` (sum-of-quarterly EPS — standard convention) |
| Prices, per-unit costs ($/ton, $/cy) | **Volume-weighted average**: `=SUMPRODUCT(prices, volumes) / SUM(volumes)` |
| Per-unit derived metrics (cash GP/ton = price − cash COGS/ton; GP/ton = GP$ × scaling / volume) | **Compute from FY inputs**, not from weighted-avg of quarterly values |
| Margin / ratio rows (GM, EBITDA margin, GP margin, leverage) | `=FY numerator / FY denominator` |
| Y/Y growth rows | `=FY[t] / FY[t-1] − 1` |
| Y/Y bps Δ on margin rows | `=(margin[t] − margin[t-1]) × 10000` |
| Y/Y incremental margin | `=(num[t] − num[t-1]) / (den[t] − den[t-1])` |
| Mix-adj price growth (not derivable) | Leave blank `""` |

### Y/Y growth on quarterly columns

For quarter columns: `=current_quarter / same_quarter_prior_year − 1`. Same-quarter-prior-year is always 5 columns to the left given the 5-column-per-year layout. For the first year of data, leave blank.

### TTM rows (e.g., Net Debt / TTM Adj. EBITDA)

Quarterly columns: `=balance / SUM(last 4 quarter columns of EBITDA)` — skip FY columns when summing.
FY columns: `=balance / FY EBITDA` directly.

### Forward formulas — handle blank inputs

Forward quarter formulas (Y/Y growth, margin, incremental margin, per-unit derived) must wrap source cells with `IF(OR(num="",den=""),"",...)` rather than relying on bare `IFERROR`.

The reason: empty forward cells return `0` from arithmetic, not an error. So `=A1/B1-1` against blank inputs returns `-100%`, not `#DIV/0!`, and `IFERROR` never fires. The result is misleading `-100%` or `0.0%` outputs littering the forward columns.

Pattern:
```
=IF(OR(num_cell="",den_cell=""),"",num_cell/den_cell-1)            // Y/Y growth
=IF(OR(num="",den=""),"",num/den)                                  // margin
=IF(OR(num_t="",num_t1="",den_t="",den_t1=""),"",
   (num_t-num_t1)/(den_t-den_t1))                                  // incremental margin
```

Use `IFERROR` ONLY as a secondary wrapper for the rare case where division by zero is genuinely possible on populated cells.

## Guidance section

- Section header "Guidance" — Tier 1 navy
- One row per guided item
- Placement: quarterly guidance → the next unreported quarter's column; full-year guidance → the guided FY column; populate the **mid-point** of the range (or the point estimate if no range)
- **Sourcing by mode**: plugin-model → guidance is CAPTURED on the Quarterly Historicals Guidance section (blue there) and these rows LINK to it (green cross-sheet refs); standalone → enter mid-points here directly, blue text + cell note: `"Source: [Company] [Period] Earnings Release — guided [low%-high%] / [$low-$high]M, mid-point shown"`
- Italic for % rows; **no yellow background** either mode

## Source citations (MANDATORY)

For every cell holding data pulled directly from a release:
- Add a cell note: `"Source: [Company] [Period] Earnings Release, [Date], [Table/Section]"`
- Apply to anchor cells per section (one per section is sufficient if the whole block came from the same source)

## Final Review Checklist

- [ ] Year labels follow `FQN YYYYA` / `FY YYYYA` convention
- [ ] All number formats include `_)` padding
- [ ] Spacing rhythm correct: 1 blank row above every section header; 1 blank row between sub-blocks within a section; 0 blank rows inside a sub-block (primary $ line + its derived %/bps rows kept tight)
- [ ] One blank breather row directly below EVERY section header (between navy band and first data row); FY gray fill extends through the breather
- [ ] Indentation: every derived sub-row (Y/Y %, margin %, bps Δ, ex-FX %, incremental %) at indent level 1, applied across full row width A:[lastcol]; primary $/count/balance lines at indent 0
- [ ] No bold anywhere in the body — bold ONLY on row 1 title, row 4 period labels, and section header bands
- [ ] No borders anywhere in the body — only row 4 bottom border (`A4:[lastcol]4`, full width) and the forecast divider remain
- [ ] Italic on all % and bps rows (label + data)
- [ ] Forecast divider in place (medium left border on first forecast column)
- [ ] Sourcing matches mode: plugin-model → historicals are green live links to Quarterly Historicals, no hardcoded historicals on this tab; standalone → no green font (cross-sheet refs converted to values)
- [ ] No yellow highlights
- [ ] All hardcoded values from releases have source cell notes (standalone mode)
- [ ] FY-from-quarters formulas match the derivation matrix
- [ ] Gridlines off; freeze panes at `B6` (per the firm-formatting Freeze Pane Standard — KPI Tracker's 5-row header block)
- [ ] Forward projections extend through current FY (if last actual is mid-year) + full next FY + Q1 next year holds guidance
- [ ] Section header navy band spans FULL width `A:[lastcol]` (merged), white bold text re-applied after merge
- [ ] Row height = 15 uniform; col A ≈ 240pt; col B ≈ 8pt; data cols width 9.5 (≈63pt)
- [ ] Arial 10 verified across full used range (merges sometimes revert to Calibri)
- [ ] Forward formulas use `IF(OR(...="",...=""),"",...)` to handle blank inputs cleanly
- [ ] FY gray fill spans EVERY row of each section block (data rows + internal spacers + trailing spacer), forming a clean rectangle
- [ ] Standalone mode only: all cross-sheet `=Model!...` refs converted to hardcoded values; only intra-tab derived formulas remain

## Anti-patterns to avoid

- ❌ Don't skip Step 0 — must ask "create new vs update existing" before anything else (exception: build-model Phase 2 skips Step 0 by design — the phase context already answers it)
- ❌ Don't dump every metric from the release — be curatorial; the tracker is a 1-2 pager
- ❌ Don't skip the A4 / B3 checkpoints — always confirm with user before building or writing
- ❌ Don't apply yellow highlighting on assumption cells
- ❌ Don't apply gray row banding on Major Total rows — only FY columns get gray fill
- ❌ Don't use leading-space indentation in labels — use Excel's real `indentLevel` feature, applied across the full row width so labels and data shift together
- ❌ Don't bold body rows — Major Totals, sub-totals, and emphasis rows stay regular weight; bold is reserved for row 1, row 4, and section header bands
- ❌ Don't add borders anywhere in the body — no row-5 top border, no thin top borders on Major Totals, no internal section dividers; only row 4 bottom border and the forecast divider
- ❌ Don't put blank rows inside a sub-block — a primary $ line and its derived %/bps rows are one tight unit, separated from the next sub-block by exactly one blank row
- ❌ Don't pack the first data row directly under the navy section-header band — every section needs a breather row below the header
- ❌ Don't use `0` as a placeholder for missing data — use blank or `""` formula
- ❌ Don't move the forecast divider without also flipping the period label E→A
- ❌ Don't leave columns A and B unfilled in the section header navy band — merge across FULL width
- ❌ Don't trust merge operations to preserve fonts — always re-apply Arial 10 + bold + color after a merge
- ❌ Don't use bare `IFERROR` for forward formulas — wrap source cells with `IF(OR(...="",...=""),"",...)`
- ❌ Don't skip loading `firm-formatting` at the start of the workflow
- ❌ Don't skip the forward-projection rule (always extend through end of current FY + next full FY)
- ❌ Don't stop FY gray fill at the last data row — extend it through the trailing spacer so each section forms a clean filled rectangle in the FY columns
- ❌ Don't ship a STANDALONE tab with `=Model!...` formulas — hardcode all cross-sheet refs before declaring done
- ❌ Don't hardcode historicals onto the tab in PLUGIN-MODEL mode — Quarterly Historicals is the single hardcode layer; the tracker links to it
