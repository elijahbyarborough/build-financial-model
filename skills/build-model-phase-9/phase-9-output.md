# Phase 9 — Output Tab

**Prerequisite:** Phase 8 complete (QC passed, all gates clear).

---

## What Happens in This Phase

Build the Output tab — a single-page investment summary that consolidates the entire model onto one printable sheet. The Returns Timeline section pulls from the **Returns tab**. Everything below (IS, KPIs, Capital Allocation) pulls from the **Model Tab**. Contains ZERO hardcoded values except the Exit P/E assumption (which mirrors the Returns tab input).

This tab is the model's "front page" for investment committee presentations, deal memos, or quick reference.

1. **Returns Timeline** — condensed replica of the Returns tab analysis (Entry → 6 FY years → Exit)
2. **Summary Income Statement** — condensed P&L with margins and CAGR / Δ
3. **Key Drivers & KPIs** — major segment/business-level drivers
4. **Capital Allocation & Returns** — FCF, deployment, leverage, ROIC

---

## Column Widths

| Column | Width | Content |
|--------|-------|---------|
| A (row labels) | 165pt | All row labels |
| B (spacer) | 6pt | Empty visual separator |
| C–L (data columns) | 70pt each (uniform) | Historical, projection, and Returns data |
| M (CAGR / Δ) | 53pt | Compound growth or directional delta |

---

## Tab Structure — Exact Section Order

### Title Block (Rows 1-2)

| Row | Cell | Content | Font | Size | Weight | Style | Color | Fill | Alignment |
|-----|------|---------|------|------|--------|-------|-------|------|-----------|
| 1 | A1 | "{Company Name} ({Ticker}) Investment Summary" | Arial | 10pt | Bold | Normal | #FFFFFF | #1C3553 | Left |
| 1 | B1:M1 | (empty — carry navy fill) | Arial | 10pt | Bold | Normal | #FFFFFF | #1C3553 | — |
| 2 | A2 | "($ in millions, except per share data)" | Arial | 10pt | Normal | Italic | #212529 | #FFFFFF | Left |
| 2 | B2:M2 | (empty) | Arial | 10pt | Normal | Normal | #000000 | #FFFFFF | — |

- Row 1 navy fill **must span the full width** A through M (including empty cells B–M)
- Row 2 has **no fill** (white background). Subtitle is italic dark (#212529), not gray.
- **No borders** on rows 1-2.

### Tier 1 Header — "5-Year Forward Valuation"

### Tier 2 Subheader — "Returns Timeline" (light blue #C2D5EB)

Immediately before the Returns column headers. This visually separates the title block from the returns data and matches the convention used for IS, KPIs, and Capital Allocation sections.

### Returns Timeline Section

This is a condensed replica of the Returns tab. It has its own column structure (Entry → 6 FY years → Exit) which differs from the rest of the Output tab.

### Tier 1 Header — "Summary Income Statement"

### Below IS: Tier 1 Header — "Key Drivers & KPIs"

### Below KPIs: Capital Allocation & Returns (Tier 2 subheaders)

---

## Returns Timeline Section

### Returns Timeline: 6 Forward FY Columns

The Returns Timeline must show 6 forward fiscal years between Entry and Exit, regardless of company. This is driven by the 5-year investment horizon crossing 6 calendar fiscal year-ends:

- **Entry**: Date = TODAY(), EPS = weighted blend of current FY and next FY using Year Fraction
- **FY Years 1-6**: Each fiscal year-end that falls within the 5-year holding period
- **Exit**: Date = Entry + 5 years, EPS = weighted blend of last two FY years

**YEARFRAC weighting**:
- First FY: weight = Year Fraction (portion of first FY remaining after entry)
- Middle FYs (2-5): weight = 1.0 (full years)
- Last FY: weight = 1 - Year Fraction (portion of last FY before exit)

The Output tab's Returns section mirrors this layout exactly. The IS/KPIs/CapAlloc sections below are independent and use their own column structure (3 historical + full projection horizon + CAGR / Δ).

### Column Layout

| Entry | FY Year 1 | FY Year 2 | FY Year 3 | FY Year 4 | FY Year 5 | FY Year 6 | Exit |
|-------|-----------|-----------|-----------|-----------|-----------|-----------|------|

Note: Row labels in columns A-B. The actual year labels depend on the model's projection period — they are NOT hardcoded.

### Returns Timeline Column Alignment

The Returns Timeline section uses **columns E–L** (8 columns): E = Entry, F–K = 6 FY years, L = Exit. Columns C–D and M are **empty** in the Returns section.

This is intentional — the Returns Timeline has 8 data points (Entry + 6 FY + Exit), which is fewer than the IS/KPIs/CapAlloc sections (10 data columns + CAGR in C–M). Aligning the Returns to E–L centers the 8-column block within the 10-column data area, with C–D empty on the left and M empty on the right. The IS/KPIs/CapAlloc sections use the full C–M range.

**Do NOT left-align the Returns to column C.** The E–L positioning is the standard across all models.

### Valuation Grid

| Row | Label | Entry | Middle FY Cols | Exit | Format |
|-----|-------|-------|---------------|------|--------|
| | Date | `=Returns!Entry Date` | `=Returns!FY dates` | `=Returns!Exit Date` | mm/dd/yy, italic, gray |
| | Diluted EPS | `=Returns!Entry EPS` | `=Returns!FY EPS` | `=Returns!Exit EPS` | $#,##0.00, **black font** |
| | Exit P/E | `=Returns!Entry P/E` (implied) | blank | `=Returns!Exit P/E` (blue, bold) | 0.0"x" |
| | EPS-Implied Price | `=Returns!Entry Price` | blank | `=Returns!Exit EPS-Implied Price` | **Major total** (F2F2F2 fill, bold, $#,##0.00) |
| | M&A Value Per Share | blank | `=Returns!FY M&A Value` | `=Returns!Exit M&A Value` | Italic, **black font**, $#,##0.00 |
| | Total Price | `=Entry Price` | blank | `=Returns!Total Price` | **Major total** (F2F2F2 fill, bold, $#,##0.00) |

**Returns section font color**: ALL data in the Returns Timeline uses **black font (#000000)**, not green — including middle FY columns (EPS, M&A Value, DPS). The only exception is light grey (#7C7F88) for Date and YEARFRAC Weight rows. Green font is used exclusively in the IS/KPIs/CapAlloc sections for historical data columns.

Key points:
- EPS-Implied Price shows the earnings-only valuation
- M&A Value Per Share shows incremental value from M&A done **during the forecast period only** — it reflects the compounding nature of acquisitions made after the entry date, NOT pre-forecast-period acquisitions
- Total Price = EPS-Implied + M&A Value — this is what the investor actually receives
- Entry column: Total Price = EPS-Implied Price = Entry Price (no M&A value accrued yet)
- Exit column: Total Price = EPS-Implied Price + M&A Value Per Share
- Middle FY columns: EPS and M&A Value shown for reference; Price rows are blank (only meaningful at Entry/Exit)
- All formulas are simple pulls from the Returns tab (`=Returns!cell`)

### Dividend Income

| Row | Label | Entry | Middle FY Cols | Exit | Format |
|-----|-------|-------|---------------|------|--------|
| | Annual DPS | blank | `=Returns!FY DPS` | blank | $#,##0.00, **black font** |
| | YEARFRAC Weight | blank | `=Returns!FY weights` | blank | 0.000"x", italic, gray (#7C7F88) |
| | DPS Received | blank | `=Returns!FY DPS Received` | blank | $#,##0.00, bold |

### Cash Flow

| Row | Label | Entry | Middle FY Cols | Exit | Format |
|-----|-------|-------|---------------|------|--------|
| | Net Cash Flow | `=Returns!Entry CF` (negative) | `=Returns!FY DPS Received` | `=Returns!Exit Total Price` | **Major total** (F2F2F2 fill, bold, $#,##0.00) |

Note: The Core Cash Flow (ex-M&A) row from the Returns tab is intentionally OMITTED on the Output tab. It is a helper row used only for the M&A Value decomposition calculation — the Output tab shows the result (M&A Value %) without the intermediate step.

### IRR Decomposition

| Row | Label | Formula | Format |
|-----|-------|---------|--------|
| | EPS CAGR | `=Returns!EPS CAGR` | 0.0%, italic |
| | M&A Value | `=Returns!M&A Value` | 0.0%, italic |
| | Dividend Yield | `=Returns!Dividend Yield` | 0.0%, italic |
| | Multiple Change | `=Returns!Multiple Change` | 0.0%, italic |
| | **IRR** | `=Returns!IRR` | 0.0%, bold italic, F2F2F2 fill |

Key points:
- These are single-cell values (only in the Entry column), not arrays
- Order: EPS CAGR → M&A Value → Dividend Yield → Multiple Change → IRR
- Bottom border below Multiple Change, top border on IRR

### Returns Timeline Font Colors

The Returns Timeline section uses a **simplified color scheme** — no green font. Green is reserved for the IS/KPIs/CapAlloc sections below (historical columns).

| Element | Font Color |
|---------|------------|
| Date row | Light grey #7C7F88, italic |
| YEARFRAC Weight row | Light grey #7C7F88, italic |
| All other data (EPS, P/E, Price, M&A Value, DPS, DPS Received, Cash Flow, IRR decomposition) | Black #000000 |
| Entry column header, FY headers, Exit header | Black #000000, bold |

**No green font (#008000) appears anywhere in the Returns Timeline section.** This differs from the IS/KPIs/CapAlloc sections, which use green for historical data columns.

### Borders in Returns Section

- Medium solid below column headers and above dates
- Thin solid below Exit P/E, above EPS-Implied Price
- Thin solid below YEARFRAC Weight, above DPS Received
- Thin solid below Multiple Change, above IRR

---

## Year Header Block

The year headers are a **standalone block** placed between the Returns Timeline and the Summary Income Statement. They are NOT part of any Tier 1 section.

**Layout (3 rows):**

1. **Year labels**: "FY 2023A", "FY 2024A", "FY 2025A", "FY 2026E", … "FY 2032E", "CAGR / Δ" — bold, center-aligned, text format (@). Medium solid bottom border on this row.
2. **Fiscal year-end dates**: `=Model!col4` for each year — mm/dd/yy, center-aligned. Medium solid top border on this row.
3. **Spacer row**: Empty, standard height.

The next row after the spacer is the "Summary Income Statement" Tier 1 navy header. No section repeats its own year row — these shared headers apply to IS, KPIs, and Capital Allocation alike.

---

## Output Column Structure (IS / KPIs / Capital Allocation)

Standard Output column layout for all sections below the Returns Timeline:

| Zone | Columns | Font Color | Description |
|------|---------|------------|-------------|
| Historical | 3 columns (LFY-2, LFY-1, LFY/Entry year) | Green (#008000) | Trailing context |
| Projection | N columns (matching model horizon, typically 6-7 years) | Dark (#212529) | Forward estimates |
| CAGR / Δ | 1 column | Center-aligned | Compound growth or directional delta |

Do NOT show the full 10-year historical. The Output is an investment summary — 3 years of trailing context is sufficient. The Model Tab retains the full historical record.

---

## Vertical Divider Borders (3-Zone Layout)

Three visual zones separated by **medium solid #808080 vertical borders**:

1. **Historical zone** (3 columns) — green font
2. **Projection zone** (6-7 columns) — dark font
3. **CAGR / Δ** (1 column) — center-aligned

Place medium solid #808080 borders on:
- Right edge of the last historical column AND left edge of the first projection column
- Right edge of the terminal year column AND left edge of the CAGR / Δ column

**Section-scoped dividers**: Vertical borders are scoped to each Tier 1 section, running from the section header row through the last data row in that section. They do **not** run continuously across the entire tab. Spacer rows between sections have no vertical dividers, creating clean visual breaks between:

- **Summary Income Statement** (header → last IS row)
- **Key Drivers & KPIs** (header → last KPI row)
- **Capital Allocation & Returns** (header → ROTIC)

These dividers do **NOT** appear in the Returns Timeline section (different column layout) or the Platinum List Indicators section.

---

## Horizontal Border Convention

Thin black borders to delineate groups and totals:

- Bottom thin border on the last row of each sub-group (above a subtotal or before a new group starts)
- Top thin border on subtotal/total rows (EBITDA, EBIT, NI, EPS, FCF, Total Deployed, ROIC, ROTIC)
- Both top + bottom thin borders on segment revenue totals

---

## CAGR / Δ Column Rules

**Header text**: "CAGR / Δ", center-aligned. All other data columns remain right-aligned.

**Base period**: Entry year → Terminal year (matching the investment horizon). Do NOT use first historical → last projection.

### What Gets a Value

| Row Type | Treatment | Format | Style |
|----------|-----------|--------|-------|
| Dollar-denominated lines (Revenue, EBITDA, EBIT, NI, EPS, CFO, FCF, Capex, Total Deployed, NOPAT, segment revenues, TAC, ex-TAC) | CAGR: `=(Terminal/Entry)^(1/N)-1` | `0.0%_);(0.0%);"-"` | Bold + italic |
| Margin / ratio lines (EBITDA Margin, Op Margin, NI Margin, FCF Margin, TAC Rate, OI Margin, ROIC, ROTIC, Payout %, FCF Conversion, EBITDA Conversion) | Delta: `=Terminal-Entry` | `+0.0%;-0.0%;"-"` (directional sign) | Italic only |
| Revenue mix % lines (Services %, Cloud %, Other Bets %) | Delta: `=Terminal-Entry` | `+0.0%;-0.0%;"-"` (directional sign) | Italic only |
| Growth rate lines (Y/Y Revenue Growth, Y/Y segment growth, Volume Growth %, Price Growth %, etc.) | **Leave blank** — a delta of a growth rate is noise | — | — |
| Share count, leverage multiples, interest coverage | **Leave blank** — not meaningful as CAGR or delta | — | — |

**Decision rule**: If the line is a level measured in dollars, show its CAGR. If the line is a rate or ratio (margin, conversion, ROIC), show the directional delta. If the line is already a rate of change (growth %), leave it blank.

---

## Summary Income Statement

### IS Section is Company-Dynamic

The IS section is dynamic per company. Include COGS → Gross Profit → Gross Margin only when the company reports a clean cost-of-revenues line. For companies that report operating expenses by function (R&D, S&M, G&A), go straight to EBIT/EBITDA without a gross profit subtotal.

### IS Row Structure

Pull the major P&L items from Model Tab. Each major dollar item is followed by its margin % row (italic). Blank spacer rows between major sections for readability.

Structure pattern (adapt to company):
- **Total Revenue** (bold) → *Revenue Growth %* (italic)
- Cost of Goods Sold → **Gross Profit** (bold) → *Gross Margin %* (italic) — only if company reports COGS
- Operating expense detail (as reported: R&D, S&M, G&A, or SG&A, etc.)
- **EBIT** (bold) → *EBIT Margin %* (italic)
- D&A
- **EBITDA** (bold, Major Total F2F2F2 fill) → *EBITDA Margin %* (italic)
- Below-the-line items (Interest, EBT, Tax) as applicable
- **Net Income** (bold, Major Total F2F2F2 fill) → *Net Margin %* (italic)
- Diluted EPS
- Diluted Shares Outstanding

---

## Key Drivers & KPIs

Tier 1 Header — "Key Drivers & KPIs"

Pull the major segment-level and business-level operating drivers from Model Tab. The specific metrics are company- and industry-dependent — the skill should identify the key drivers from the Model Tab and pull them here.

### Structural Pattern (repeats per segment)

- **Tier 2 subheader** for each segment/business line (light blue fill #C2D5EB)
- Volume/quantity metrics → growth %
- Pricing/ASP metrics → growth %
- Segment revenue → segment profitability
- Unit economics → margin %

Each segment block follows the same structure with a blank spacer row between segments. The exact rows depend on what the Model Tab tracks — this section mirrors whatever operating KPIs are on the Model Tab.

Formatting: Same conventions as IS section — bold for key totals, italic for % rows, green for historical, CAGR / Δ column with same rules ($ lines get CAGR, margins get delta, growth rates left blank).

---

## Capital Allocation & Returns

### Tier 2 Subheader — "Capital Allocation & Returns"

| Row | Label | Source | Format |
|-----|-------|--------|--------|
| | Cash From Operations | `='Model Tab'!CFO` | $#,##0 |
| | Capital Expenditures | `='Model Tab'!Capex` | $#,##0 |
| | **Free Cash Flow** | `='Model Tab'!FCF` | $#,##0, bold |
| | *FCF Margin %* | `='Model Tab'!FCF%` | 0.0%, italic |
| | *FCF Conversion %* | `='Model Tab'!FCF Conv` | `0.0%_);(0.0%);"—"`, italic |
| | *EBITDA Conversion %* | `='Model Tab'!EBITDA Conv` | `0.0%_);(0.0%);"—"`, italic |

FCF Conversion = FCF / Net Income. EBITDA Conversion = FCF / EBITDA. Use em-dash "—" as zero placeholder for conversion metrics.

### Tier 2 Subheader — "Capital Deployment"

| Row | Label | Source | Format |
|-----|-------|--------|--------|
| | Acquisitions, Net | `='Model Tab'!Acquisitions` | $#,##0 |
| | Dividends Paid | `='Model Tab'!Dividends` | $#,##0 |
| | Gross Share Repurchases | `='Model Tab'!Buybacks` | $#,##0 |
| | **Total Capital Deployed** | `='Model Tab'!Total Deployed` | $#,##0, bold |
| | *Acquisition-Adjusted Payout %* | `='Model Tab'!Adj Payout` | 0.0%, italic |
| | (blank spacer) | | |
| | Net Debt / EBITDA | `='Model Tab'!leverage` | 0.0"x" |
| | Interest Coverage (x) | `='Model Tab'!coverage` | 0.0"x" |
| | *Acquisition IRR* | `='Model Tab'!IRR` | 0.0%, italic |

**Acquisition IRR placement**: Below Interest Coverage, immediately before the ROIC / ROTIC subheader. References the Model Tab (not Capital Allocation Build directly), maintaining the two-source-tab rule. Historical columns are blank. Projection columns show the assumed IRR (green font for cross-sheet reference).

**Conditional inclusion**: Only include the Acquisition IRR row if the model includes material acquisition activity (i.e., non-zero Acquisitions, Net in the projection period). If the model has no projected acquisitions, omit this row entirely.

### Tier 2 Subheader — "ROIC / ROTIC"

| Row | Label | Source | Format |
|-----|-------|--------|--------|
| | NOPAT | `='Model Tab'!NOPAT` | $#,##0 |
| | Invested Capital | `='Model Tab'!IC` | $#,##0 |
| | **ROIC** | `='Model Tab'!ROIC` | 0.0%, bold |
| | Tangible Invested Capital | `='Model Tab'!Tangible IC` | $#,##0 |
| | **ROTIC** | `='Model Tab'!ROTIC` | 0.0%, bold |

---

## Cross-Tab Dependencies

### What Output READS from

| Section | Source Tab |
|---------|-----------|
| Returns Timeline | Returns tab |
| Summary IS, Key Drivers, Capital Allocation | Model Tab |

### What Output WRITES to

**Nothing.** Pure read-only summary. Drives no other tabs.

The Output tab has exactly TWO source tabs: **Model Tab** and **Returns**. It never reaches back to individual build tabs.

---

## Formatting Conventions

### Header Hierarchy

- **Tier 1 headers**: Navy fill (#1C3553), white bold text, span all columns including CAGR / Δ. Fill must extend across the entire row from A through the CAGR / Δ column. Do not leave column B or the CAGR / Δ column unfilled.
- **Tier 2 subheaders**: Bold, non-italic, indentLevel = 1. Light blue fill (#C2D5EB), black text (#000000), Arial 10pt. Fill must extend across the entire row from A through the CAGR / Δ column. Do not leave column B or the CAGR / Δ column unfilled.
- **Column headers**: Bold, center-aligned, medium bottom border

### Label Indentation (Output Tab)

Use Excel's indent feature (not leading spaces). The Output tab follows the firm-formatting 3-level convention:

| Indent Level | Used For | Examples |
|---|---|---|
| **0** | Tier 1/Tier 2 headers, key totals, standalone line items | Section headers, Net Revenue, EBITDA, EBIT, Net Income, Diluted EPS, FCF, Total Capital Deployed, Normalized EBITDA, Normalized EBIT, ROIC, ROTIC |
| **1** | Sub-items, margin/% rows, growth rates, memo bridge starting points, supporting line items | Y/Y Revenue Growth, OpEx, D&A, Operating Margin %, EBITDA Margin %, Net Income Margin %, FCF Margin %, Diluted Shares, EBITDA (memo in bridge), Interest Coverage, Net Debt/EBITDA |
| **2** | Addback/detail lines within a reconciliation bridge | + Litigation Provision, + Other / Non-Recurring, + SBC, + M&A Expense |

**Decision rule:** If the row is a dollar total that a reader would scan for (Revenue, EBITDA, NI, FCF, Normalized totals) → indent 0. If the row explains or decomposes a total (margins, growth, sub-expenses, bridge memo items) → indent 1. If the row is a detail line inside a bridge/reconciliation → indent 2. Max depth = 2.

### Data Formatting

All number formats defer to `firm-formatting.md` — use the standard format strings with `_)` right-padding:

- Historical columns: Green font (#008000); projection columns: default black (Output tab exception per firm-formatting)
- $ items: `$#,##0_);($#,##0);"-"`
- Per-share items: `$#,##0.00_);($#,##0.00);"-"`
- Margins/percentages: `0.0%_);(0.0%);"-"`, italic
- Conversion metrics (FCF Conversion, EBITDA Conversion): `0.0%_);(0.0%);"—"` (em-dash zero)
- Multiples: `0.0"x"_);(0.0"x");"-"`
- Share counts: `0.0`

### Spacer Row Heights (Output Tab Exception)

The Output tab overrides firm-formatting's uniform row height rule. Because it's a one-page investment summary designed for print and presentation, spacer rows use a 3-tier height system:

| Tier | Height | Placement |
|------|--------|-----------|
| Major break | 8pt | Before Tier 1 navy headers (between major sections) |
| Minor break | 5pt | Before Tier 2 light blue subheaders (between subsections) |
| Compact break | 3pt | Between data groups within a section (e.g., between Revenue Growth % and EBITDA) |

All data and header rows remain at standard 15pt. Only empty spacer rows are resized.

### Font Consistency

Every cell on the Output tab uses Arial 10pt. No exceptions — including header fills, spacer rows, and the CAGR / Δ column. Do not inherit Calibri or other fonts from template defaults.

### Label Readability

Column A width is fixed at ~154–177pt. All row labels must fit within this width without truncation. If a label is too long, abbreviate:

- "Google Network Members' Properties" → "Google Network Revenue"
- "Aggregate TAC Rate (% of Ad Revenue)" → "Agg. TAC Rate (% of Ad Rev)"
- "Interest Coverage (EBITDA / Interest)" → "Interest Coverage (x)"
- "Subscriptions, Platforms & Devices" → "Subs, Platforms & Devices"

After writing all labels, visually verify none are clipped by the column boundary.

### Row Formatting Patterns

- Key totals (Revenue, GP, EBIT, EBITDA, NI, FCF, Total Deployed, ROIC, ROTIC): Bold
- All % rows: Italic
- Major totals in Returns section: F2F2F2 fill + bold
- M&A Value Per Share: Italic, black font
- All Returns Timeline data: Black font (except Date/YEARFRAC Weight rows which are light grey #7C7F88)

---

## Key Design Principles

1. **Zero assumptions** — The Output tab contains no inputs except Exit P/E (mirrors Returns tab). Everything else is a pull formula.
2. **Two source tabs only** — All formulas reference either Model Tab or Returns tab. Never individual build tabs.
3. **One-page summary** — Designed to fit on one printed page or one screen.
4. **3-zone column layout** — Historical (3 cols, green) | Projection (6-7 cols, dark) | CAGR / Δ (1 col, centered), separated by medium solid vertical borders.
5. **CAGR / Δ decision rule** — Dollar lines get CAGR. Margin/ratio lines get directional delta. Growth rate lines left blank.
6. **Company-dynamic IS** — Adapt to company's reporting structure. Include gross profit only when COGS is reported.
7. **6 forward FY columns in Returns** — Always 6 FY years between Entry and Exit, driven by the 5-year horizon.
8. **KPIs are company-specific** — The skill should identify and pull whatever operating drivers exist on the Model Tab, not hardcode specific metrics.

---

## Key Rules

- **Output/summary layer only** — no assumption inputs (except Exit P/E mirror). Every cell is a formula.
- **Two source tabs**: Model Tab and Returns. Never reach back to build tabs.
- **Returns section uses Entry → 6 FY → Exit column layout.** IS/KPIs/Cap Alloc use 3 historical → projections → CAGR / Δ.
- **CAGR for $ items, delta for margins/ratios, blank for growth rates.**
- **Vertical dividers** between Historical | Projection | CAGR zones (IS/KPIs/CapAlloc only, not Returns).
- **Entry EPS and Entry P/E use black font**, not green.
- **Em-dash "—" zero placeholder** for conversion metrics.
- **No freeze panes. All formatting defers to `firm-formatting`.**

---

## Tab Completion Verification

Before reporting the Output tab complete, read and paste:

```
TAB VERIFICATION -- Output:
  freezePanes: [null -- if not null, STOP and fix]
  gridlines: [off -- if on, STOP and fix]
  font: [Arial 10pt -- if not, STOP and fix]
  tab color: [hex] -- expected: #1C3553
  Returns Timeline: Entry + [count] FY columns + Exit = [total] columns
  Vertical dividers between Historical|Projection|CAGR zones: [present/missing]
  IRR value: [value]
  Spot-check: Output Revenue FY1 = Model Tab Revenue FY1: [match/mismatch]
  Spot-check: Output EPS = Returns EPS: [match/mismatch]
```

If you do not paste this output, the user cannot verify compliance. No output = not verified.

---

## Definition of Done (Phase 9)

A phase is complete if and only if ALL of the following are true. Report completion by reading these values back to the user -- not by summarizing in prose.

1. **Task Tracker**: Every subtask row for Phase 9 shows Status = "COMPLETE". Cite the actual cell addresses you checked.
2. **All Output tab sections populated**: Returns Timeline, Summary IS, Key Drivers & KPIs, Capital Allocation & Returns.
3. **Returns Timeline** has Entry + 6 FY columns + Exit.
4. **Vertical dividers** present between Historical|Projection|CAGR zones (IS/KPIs/CapAlloc sections).
5. **CAGR/delta column** correctly populated (CAGR for $ items, delta for margins, blank for growth rates).
6. **All formulas reference only Model Tab or Returns tab** -- never individual build tabs.
7. **Tab Completion Verification** output pasted.
8. **Task Tracker Model State Block**: "Last Skill Run" updated, "Next Skill" = "build-model-phase-10".

If you write "Phase 9 complete" in chat before reading and reporting these values, you have made an error. Re-verify and correct.

**STOP. Report status. Wait for "continue."**
