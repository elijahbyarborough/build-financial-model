# Phase 9 — Output Tab

**Reference:** `references/phase-9-output.md`
**Prerequisite:** Phase 8 complete (QC passed, all gates clear).
**This is the final phase.**

---

## What Happens in This Phase

Build the Output tab — a single-page investment summary that consolidates the entire model onto one printable sheet. It pulls from exactly two source tabs (Model Tab and Returns) and contains ZERO hardcoded values except the Exit P/E assumption (which mirrors the Returns tab input).

This tab is the model's "front page" for investment committee presentations, deal memos, or quick reference.

1. **5-Year Forward Valuation & Returns** — condensed replica of the Returns tab analysis
2. **Summary Income Statement** — condensed P&L with margins and CAGR
3. **Key Drivers & KPIs** — major segment/business-level drivers
4. **Capital Allocation & Returns** — FCF, deployment, leverage, ROIC

---

## Tab Structure — Exact Section Order

### Row 1-2: Title Block

- **Row 1**: Company name + " Investment Summary" — bold, Arial 10pt (per firm-formatting; no oversized fonts)
- **Row 2**: Units subtitle: "($ in millions, except per share data)"

### Row 4: Tier 1 Header — "5-Year Forward Valuation"

### Rows 6-25: Returns Timeline Section

This is a condensed replica of the Returns tab. It has its own column structure (Entry → FY years → Exit) which differs from the rest of the Output tab.

### Row 27: Tier 1 Header — "Summary Income Statement"

### Below IS: Tier 1 Header — "Key Drivers & KPIs"

### Below KPIs: Capital Allocation & Returns (Tier 2 subheaders)

---

## Returns Timeline Section (Rows 6-25)

### Column Layout (Row 7 headers)

| Col E | Col F | Col G | ... | Col K | Col L |
|-------|-------|-------|-----|-------|-------|
| Entry | FY Year 1 | FY Year 2 | ... | FY Year N | Exit |

Note: Columns A-D are row labels only (no data). The actual year labels depend on the model's projection period — they are NOT hardcoded.

### Valuation Grid (Rows 8-13)

| Row | Label | Entry | Middle FY Cols | Exit | Format |
|-----|-------|-------|---------------|------|--------|
| 8 | Date | `=Returns!Entry Date` | `=Returns!FY dates` | `=Returns!Exit Date` | mm/dd/yy, italic, gray |
| 9 | Diluted EPS | `=Returns!Entry EPS` | `=Returns!FY EPS` (green font) | `=Returns!Exit EPS` | $#,##0.00 |
| 10 | Exit P/E | `=Returns!Entry P/E` (implied) | blank | `=Returns!Exit P/E` (blue, bold) | 0.0"x" |
| 11 | EPS-Implied Price | `=Returns!Entry Price` | blank | `=Returns!Exit EPS-Implied Price` | **Major total** (F2F2F2 fill, bold, $#,##0.00) |
| 12 | M&A Value Per Share | blank | `=Returns!FY M&A Value` (green) | `=Returns!Exit M&A Value` | Italic, green font (#008000), $#,##0.00 |
| 13 | Total Price | `=Entry Price` | blank | `=Returns!Total Price` | **Major total** (F2F2F2 fill, bold, $#,##0.00) |

Key points:
- Row 11 (EPS-Implied Price) shows the earnings-only valuation
- Row 12 (M&A Value Per Share) shows incremental value from M&A program — italic green
- Row 13 (Total Price) = EPS-Implied + M&A Value — this is what the investor actually receives
- Entry column: Total Price = EPS-Implied Price = Entry Price (no M&A value accrued yet)
- Exit column: Total Price = EPS-Implied Price + M&A Value Per Share
- Middle FY columns: EPS and M&A Value shown for reference; Price rows are blank (only meaningful at Entry/Exit)
- All formulas are simple pulls from the Returns tab (`=Returns!cell`)

### Dividend Income (Rows 15-17)

| Row | Label | Entry | Middle FY Cols | Exit | Format |
|-----|-------|-------|---------------|------|--------|
| 15 | Annual DPS | blank | `=Returns!FY DPS` (green font) | blank | $#,##0.00, green |
| 16 | YEARFRAC Weight | blank | `=Returns!FY weights` | blank | 0.000"x", italic, gray (#7C7F88) |
| 17 | DPS Received | blank | `=Returns!FY DPS Received` | blank | $#,##0.00, bold |

### Cash Flow (Row 19)

| Row | Label | Entry | Middle FY Cols | Exit | Format |
|-----|-------|-------|---------------|------|--------|
| 19 | Net Cash Flow | `=Returns!Entry CF` (negative) | `=Returns!FY DPS Received` | `=Returns!Exit Total Price` | **Major total** (F2F2F2 fill, bold, $#,##0.00) |

Note: The Core Cash Flow (ex-M&A) row from the Returns tab is intentionally OMITTED on the Output tab. It is a helper row used only for the M&A Value decomposition calculation — the Output tab shows the result (M&A Value %) without the intermediate step.

### IRR Decomposition (Rows 21-25)

| Row | Label | Formula | Format |
|-----|-------|---------|--------|
| 21 | EPS CAGR | `=Returns!EPS CAGR` | 0.0%, italic |
| 22 | M&A Value | `=Returns!M&A Value` | 0.0%, italic |
| 23 | Dividend Yield | `=Returns!Dividend Yield` | 0.0%, italic |
| 24 | Multiple Change | `=Returns!Multiple Change` | 0.0%, italic |
| 25 | **IRR** | `=Returns!IRR` | 0.0%, bold italic, F2F2F2 fill |

Key points:
- These are single-cell values (only in the Entry column, E), not arrays
- Order: EPS CAGR → M&A Value → Dividend Yield → Multiple Change → IRR
- Bottom border below Multiple Change, top border on IRR

### Borders in Returns Section

- Medium solid below column headers (row 7) and above dates (row 8)
- Thin solid below Exit P/E (row 10), above EPS-Implied Price (row 11)
- Thin solid below YEARFRAC Weight (row 16), above DPS Received (row 17)
- Thin solid below Multiple Change (row 24), above IRR (row 25)

---

## Summary Income Statement (from Row 27)

Uses a DIFFERENT column layout from the Returns section above.

### Column Layout (Row 28 headers)

| Col A | Col B | Col C | Col D | ... | Col L | Col M |
|-------|-------|-------|-------|-----|-------|-------|
| Label | (empty) | FY Year 1 | FY Year 2 | ... | FY Year N | CAGR |

- Historical years use "FY ####A" format, projection years use "FY ####E" format
- The last column is always "CAGR"
- The actual year labels and number of historical vs. projection columns depend on the model

### IS Row Structure

Pull the major P&L items from Model Tab. Each major dollar item is followed by its margin % row (italic). Blank spacer rows between major sections for readability.

Structure pattern:
- **Total Revenue** (bold) → *Revenue Growth %* (italic)
- Cost of Goods Sold → **Gross Profit** (bold) → *Gross Margin %* (italic)
- **EBIT** (bold) → *EBIT Margin %* (italic)
- **EBITDA** (bold) → *EBITDA Margin %* (italic)
- **Net Income** (bold) → *Net Margin %* (italic)
- Diluted EPS
- Diluted Shares Outstanding

### CAGR Formula Logic

- For $ items: `=(last_year / first_year) ^ (1 / number_of_years) - 1`
- For margin % items: `=last_year - first_year` — simple basis-point change, NOT CAGR (margins do not compound)

### Formatting

All number formats follow `firm-formatting.md` with `_)` right-padding:

- Historical columns: green font (#008000); projection columns: default black font (Output tab exception per firm-formatting)
- $ items: `$#,##0_);($#,##0);"-"` (no decimals for millions)
- Per-share: `$#,##0.00_);($#,##0.00);"-"` (two decimals)
- Margins: `0.0%_);(0.0%);"-"`, italic
- Shares: `0.0`
- Multiples: `0.0"x"_);(0.0"x");"-"`

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

Formatting: Same conventions as IS section — bold for key totals, italic for % rows, green for historical, CAGR column with same logic.

---

## Capital Allocation & Returns

### Tier 2 Subheader — "Capital Allocation & Returns"

| Row | Label | Source | Format |
|-----|-------|--------|--------|
| | Cash From Operations | `='Model Tab'!CFO` | $#,##0 |
| | Capital Expenditures | `='Model Tab'!Capex` | $#,##0 |
| | **Free Cash Flow** | `='Model Tab'!FCF` | $#,##0, bold |
| | *FCF Margin %* | `='Model Tab'!FCF%` | 0.0%, italic |
| | *FCF Conversion %* | `='Model Tab'!FCF Conv` | 0.0%, italic |

FCF Conversion = FCF / Net Income.

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
| | Interest Coverage | `='Model Tab'!coverage` | 0.0"x" |

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
| Returns Timeline (rows 6-25) | Returns tab |
| Summary IS, Key Drivers, Capital Allocation | Model Tab |

### What Output WRITES to

**Nothing.** Pure read-only summary. Drives no other tabs.

The Output tab has exactly TWO source tabs: **Model Tab** and **Returns**. It never reaches back to individual build tabs.

---

## Formatting Conventions

### Header Hierarchy

- **Tier 1 headers**: Navy fill (#1C3553), white bold text, span all columns
- **Tier 2 subheaders**: Light blue fill (#C2D5EB), dark text
- **Column headers**: Bold, center-aligned, medium bottom border

### Data Formatting

All number formats defer to `firm-formatting.md` — use the standard format strings with `_)` right-padding and `"-"` for zeros:

- Historical columns: Green font (#008000); projection columns: default black (Output tab exception per firm-formatting)
- $ items: `$#,##0_);($#,##0);"-"`
- Per-share items: `$#,##0.00_);($#,##0.00);"-"`
- Margins/percentages: `0.0%_);(0.0%);"-"`, italic
- Multiples: `0.0"x"_);(0.0"x");"-"`
- Share counts: `0.0`

### Row Formatting Patterns

- Key totals (Revenue, GP, EBIT, EBITDA, NI, FCF, Total Deployed, ROIC, ROTIC): Bold
- All % rows: Italic
- Major totals in Returns section: F2F2F2 fill + bold
- M&A Value Per Share: Italic, green font (#008000)

### Border Patterns

- Medium solid borders below header rows
- Thin solid borders above major totals
- No vertical borders

---

## Key Design Principles

1. **Zero assumptions** — The Output tab contains no inputs except Exit P/E (mirrors Returns tab). Everything else is a pull formula.
2. **Two source tabs only** — All formulas reference either Model Tab or Returns tab. Never individual build tabs.
3. **One-page summary** — Designed to fit on one printed page or one screen.
4. **Consistent column structure** — IS, KPIs, and Capital Allocation share the same column layout (historical → projection → CAGR). Returns section has its own (Entry → FY → Exit).
5. **CAGR as the rightmost column** — Compound growth for $ items, simple basis-point change for margins.
6. **KPIs are company-specific** — The skill should identify and pull whatever operating drivers exist on the Model Tab, not hardcode specific metrics.

---

## Key Rules

- **Output/summary layer only** — no assumption inputs (except Exit P/E mirror). Every cell is a formula.
- **Two source tabs**: Model Tab and Returns. Never reach back to build tabs.
- **Returns section uses Entry → FY → Exit column layout.** IS/KPIs/Cap Alloc use historical → projection → CAGR layout.
- **CAGR for $ items, basis-point change for margins.**
- **No freeze panes. All formatting defers to `firm-formatting`.**

**STOP. Update Task Tracker. Report status. Model build is complete.**
