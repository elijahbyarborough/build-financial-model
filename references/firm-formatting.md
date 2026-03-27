---
name: firm-formatting
description: |
  FC firm-wide formatting and visual standards for Excel models, PowerPoint exhibits, and any financial output. This skill is the single source of truth for how every model, spreadsheet, and presentation looks. It enforces a strict visual hierarchy, number formatting with _) alignment padding, color coding, spacing, and a section-by-section workflow with self-checks.

  Triggers on: any Excel model, spreadsheet formatting, financial model reformat, PowerPoint deck, equity research output, valuation output, earnings workbook, returns analysis, or any task where visual consistency of financial outputs matters. Also triggers when the user says "reformat", "clean up this model", "make it look right", "firm format", or "standardize this".

  This skill is formatting and workflow ONLY. It does not contain modeling methodology (SBC treatment, valuation approach, projection rules, etc.) -- those belong in separate skills.
---

# FC Firm Conventions

**OVERRIDE PRIORITY: When this skill conflicts with ANY other skill's formatting defaults, this skill wins.** Firm visual standards always override generic plugin or skill defaults.

---

## FC Color Palette

Sourced from `Frist_Capital_Theme_v4.potx`. These colors are the firm standard across all outputs.

### Theme Colors
| Role | Hex | Name |
|------|-----|------|
| Primary text | #212529 | Near-black |
| Dark navy | #1C3553 | dk2 / accent1 |
| White | #FFFFFF | lt1 |
| Light gray bg | #F5F6F8 | lt2 |
| Steel blue | #5B8FA8 | accent2 |
| Sage green | #6B9E6F | accent3 |
| Amber/gold | #D4963A | accent4 |
| Terracotta red | #C05746 | accent5 |
| Gray | #7C7F88 | accent6 |

### Custom Colors
| Name | Hex |
|------|-----|
| Sky Blue | #C7DBE7 |
| Dark Teal | #436E71 |
| Dark Green | #5E8C3D |
| Red | #A8412F |
| Light Green | #88B868 |
| Light Red | #DA5C47 |
| Brown | #BA651C |
| Orange | #F28F3E |
| Yellow | #E5B00E |

### Derived Application Colors
| Usage | Hex | Source |
|-------|-----|--------|
| Section header fill | #1C3553 | Theme dk2 |
| Subheader fill | #C2D5EB | Derived light blue |
| Major total fill | #F2F2F2 | Excel "White, Bg1, Darker 5%" |
| Year header fill | None | -- |
| Key assumption bg | #FFFF00 | Standard yellow |

All formatting details — Visual Hierarchy, Standard Tab Header, Year Labels, Spacer Rows, Display Settings, Assumption Placement, Number Formats, Color Coding, and Section Boundary Rule — are defined in the **Excel Formatting Standards** section below. That section is the single source of truth.

---

## Workflow

### Phased Execution
This skill works in phases, not one-shot. Before starting, read the Task Tracker tab (per `build-model`) to understand current model state. Update the tracker before, during, and after each phase. If you identify issues outside your scope (broken formulas, missing linkages, incorrect methodology), add tasks to the tracker for the appropriate skill.

### Phase Boundaries
After completing each section: **STOP. Run the self-check checklist. Report status to user. Wait for "continue" before proceeding.**

### Scope
The user specifies the scope of work:
- **Section-by-section**: Reformat one section at a time (e.g., "do the Income Statement")
- **Tab-by-tab**: Finish one sheet completely before moving to the next
- **Full model**: One-shot reformat of the entire workbook

If the user doesn't specify, ask.

### Execution Order (Per Section)
1. **Read** the section. Identify every row's tier (Section Header, Subheader, Year Row, data, Subtotal, Major Total).
2. **Format** the section top-to-bottom: fills, fonts, borders, number formats, alignment, indentation.
3. **Apply number format rules**: detect row type (dollar, percentage, multiple, per-share, integer, date) and apply the correct format string with `_)` padding. Apply the Section Boundary Rule for `$` placement.
4. **Apply font color coding**: blue for hardcodes, black for formulas, green for cross-sheet refs, red for SPG()/external data, gray for NM/NA.
5. **Insert/remove spacer rows** per the spacer rules.
6. **Self-check**: verify every rule in the checklist below. Fix any violations before moving on.

### Self-Check Checklist (Run After Every Section)
- [ ] Every row assigned to exactly one of the 5 tiers?
- [ ] All fills correct per tier? No stray fills?
- [ ] All borders correct per tier? No double-bottom borders anywhere?
- [ ] All text is Arial 10pt? No other fonts or sizes crept in?
- [ ] Number formats all include `_)` padding?
- [ ] Section Boundary Rule applied? ($ on first row + total row of each monetary block, plain number in middle)
- [ ] Percentage rows are italic (label + data)?
- [ ] Multiple rows detected and formatted with `"x"` suffix?
- [ ] Per-share rows detected and formatted with `$#,##0.00`?
- [ ] Font color coding correct? (blue/black/green/red/gray)
- [ ] Labels left-aligned with proper indent levels (real indent feature, not spaces)?
- [ ] Data cells right-aligned?
- [ ] Year headers centered?
- [ ] Spacer rows only above section headers and between blocks, never below headers?
- [ ] No merged cells in the data area?
- [ ] Key assumptions have yellow (#FFFF00) background?
- [ ] Uniform column widths for all year/data columns?
- [ ] Gridlines removed on this tab?
- [ ] No frozen panes on this tab?
- [ ] Standard Tab Header (rows 1–6) matches spec? (navy title row, italic unit label, year row, FYE dates, blank spacer, then first section header) — skip for Returns/Tracker tabs.
- [ ] Year labels follow the Year Label Convention? (`FY 2026E` not `FY2026E`, `2026E`, or `FY 2026`)

### Agentic Judgment
When rules collide (e.g., a row is both a subtotal and the last row before a new section header), **make the best judgment call silently**. Apply the tier that preserves the clearest visual hierarchy for the reader. Only escalate to the user if the decision is structurally ambiguous (e.g., "should this row be a subtotal or a major total?" when the answer genuinely depends on the user's intent).

Document what you did if asked, but don't interrupt the workflow for minor formatting arbitration.

---

# Excel Formatting Standards

Complete specification for all Excel model formatting. This is the authoritative reference -- when in doubt, this file wins.

---

## Font

- **Arial 10pt everywhere.** Data cells, labels, formulas, headers, subheaders, totals. All 10pt.
- Bold where specified by the visual hierarchy (headers, subheaders, totals). Never a different size.
- No other fonts. Never Times New Roman. Never Calibri. Never Aptos.

---

## Standard Tab Header (Rows 1–6)

**Applies to every tab EXCEPT Returns and Tracker.** Every qualifying tab opens with this exact 6-row structure:

| Row | Content | Fill | Font | Alignment | Border |
|-----|---------|------|------|-----------|--------|
| **1** | `Company Name (TICKER) – Tab Name` | #1C3553 (navy), full width | White (#FFFFFF), bold, Arial 10pt | Left | None |
| **2** | `($ in millions)` | None | Black (#212529), italic, Arial 10pt | Left (label cell only) | None |
| **3** | `FY 2024A  FY 2025E  FY 2026E …` | None | Bold black, Arial 10pt, centered | Centered | Medium bottom |
| **4** | `12/31/24  12/31/25 …` | None | Regular black, Arial 10pt, centered | Centered | None |
| **5** | Blank spacer | None | — | — | None |
| **6** | First Tier 1 section header | Per Tier 1 spec | Per Tier 1 spec | Per Tier 1 spec | Per Tier 1 spec |

- **Row 1** navy fill spans label column through all data columns. Data cells are empty but carry the fill.
- **Row 2** is never bold. Label cell only; data columns empty.
- **Row 3** is the standard Tier 3 year row. Year format is text (`@`), following the Year Label Convention (see below). Every tab hardcodes its own year headers (black text, locally owned).
- **Row 4** dates are `MM/DD/YY`, centered, regular weight, no fill, no border. Label cell blank.
- **Row 5** is completely empty — no formatting, fills, or borders.
- **Row 6** begins the tab body with normal Tier 1 formatting.

---

## Visual Hierarchy -- Full Specification

### Tier 1: Section Header
- **Examples**: "Income Statement", "Balance Sheet", "Cash Flow Statement", "KPI Dashboard"
- **Fill**: #1C3553 (dark navy), full width (label column through all year columns)
- **Text**: White (#FFFFFF), bold, Arial 10pt, left-aligned in label cell
- **Border**: None
- **Row height**: Same as all other rows (uniform)
- **Spacer**: One blank spacer row ABOVE (never below). Data or subheader starts on the very next row.

### Tier 2: Subheader
- **Examples**: "Revenue Drivers", "Operating Expenses", "Working Capital", "Debt Schedule"
- **Fill**: #C2D5EB (light blue), full width
- **Text**: Bold, black (#212529), Arial 10pt, left-aligned in label cell
- **Border**: None
- **Row height**: Same as all other rows (uniform)
- **Spacer**: No spacer row below. Data starts immediately on the next row.

### Tier 3: Year Row
- **Examples**: The row containing "FY 2024A", "FY 2025E", "FY 2026E", etc.
- **Fill**: None
- **Text**: Bold, black (#212529), Arial 10pt, **centered** in each data column
- **Border**: **Medium bottom border** spanning all data columns
- **Label cell**: Can contain descriptor like "($ in millions)" or be blank. Left-aligned if populated.
- **Year format**: Text format `@` following the Year Label Convention below. Every tab hardcodes its own year headers (black text, locally owned — never cross-sheet references).
- **Row height**: Same as all other rows (uniform)

### Tier 4: Subtotal
- **Examples**: "Gross Profit", "Total Revenue", "Total Operating Expenses", "Operating Income"
- **Fill**: None
- **Text**: Bold, black (#212529), Arial 10pt
- **Border**: Thin top border on data cells (label column through all year columns)
- **Number format**: Follows Section Boundary Rule (typically Currency format since subtotals are usually the last row of a block)
- **Row height**: Same as all other rows (uniform)

### Tier 5: Major Total
- **Examples**: "EBITDA", "Net Income", "Total Assets", "Total Liabilities & Equity", "Free Cash Flow"
- **Fill**: #F2F2F2 (Excel "White, Background 1, Darker 5%"), full width
- **Text**: Bold, black (#212529), Arial 10pt
- **Border**: Thin top border on data cells (label column through all year columns)
- **Number format**: Currency format (has `$`)
- **Row height**: Same as all other rows (uniform)

### Data Rows (default -- not a numbered tier)
- **Fill**: None
- **Text**: Regular weight, Arial 10pt
- **Font color**: Determined by cell type (see Color Coding below)
- **Border**: None
- **Number format**: Determined by row type (see Number Formats below)
- **Label alignment**: Left-aligned with indent level
- **Data alignment**: Right-aligned

---

## Spacer Rows

- **Row spacers, not column spacers.** Never use blank columns for separation.
- Place spacer rows **above section headers** (not below them).
- Place spacer rows **between numeric blocks** that need visual breathing room (e.g., between a total row and the next group of line items within the same section).
- **Never place a spacer row directly below a section header or subheader.** Data starts immediately.
- Spacer rows are the same height as all other rows (uniform).
- Spacer rows are completely empty — no formatting, no fills, no borders.

---

## Year Label Convention

| Period Type | Format | Examples |
|-------------|--------|----------|
| Fiscal year — actual | `FY YYYYA` | `FY 2024A`, `FY 2025A` |
| Fiscal year — estimate | `FY YYYYE` | `FY 2026E`, `FY 2027E` |
| Fiscal quarter — actual | `FQN YYYYA` | `FQ1 2025A`, `FQ3 2024A` |
| Fiscal quarter — estimate | `FQN YYYYE` | `FQ4 2026E`, `FQ1 2027E` |

**Rules:**
- **Always include the prefix** (`FY` or `FQ1`/`FQ2`/`FQ3`/`FQ4`). Never use bare years like `2025E`.
- **Always include a space** between the prefix and the year. `FY 2026E`, not `FY2026E`.
- **Always include the suffix** — `A` for actuals, `E` for estimates. Never use bare `FY 2026`.
- **Every tab hardcodes its own year headers.** Year labels are locally owned (black text). They are never cross-sheet references.
- **Calendar year prefix**: Use `CY` instead of `FY` only when the company's FYE is not December AND you need to distinguish calendar year from fiscal year.
- **Cell format**: Text (`@`). Year labels are strings, not dates or numbers.

---

## Indentation

Use Excel's **actual indent feature** (cell format > alignment > indent level). Never use leading spaces.

| Row Context | Indent Level |
|-------------|-------------|
| Section header label | 0 |
| Subheader label | 0 |
| Direct child of section/subheader | 1 |
| Sub-child (nested driver, component) | 2 |
| Subtotal / Major Total label | 0 |

Maximum indent depth: 2 levels.

---

## Color Coding (Font Colors)

| Cell Type | Font Color | Hex |
|-----------|-----------|-----|
| Hardcoded inputs / assumptions | Blue | #0000FF |
| Formulas / calculations | Black | #212529 |
| Cross-sheet references | Green | #008000 |
| External data links (SPG(), etc.) | Red | #FF0000 |
| NM / NA / not meaningful | Gray | #7C7F88 |

**Output tab exception**: The Output tab is designed for print. On the Output tab, use green font (#008000) for **historical columns** and default black for **projection columns** to visually delineate actual vs. estimated years. This overrides the cross-sheet = green rule for that tab only, since every cell on the Output tab is a cross-sheet reference and applying green everywhere would defeat the purpose.

**Structural labels are always black** — year headers, row labels, section headers, unit labels, and other non-data text are black (#212529) even though they are technically hardcoded. The blue = hardcoded rule applies only to data assumption values, not to structural elements of the model. Year headers in particular are always black and locally hardcoded per the Year Label Convention.

**Key assumption cells** (hardcoded inputs that are critical model drivers): Blue text (#0000FF) + yellow background (#FFFF00).

### Assumption Placement: Same Row, Not Separate

Assumption values go directly in the **projection columns of the existing metric row** — the same row that holds the historical values. Do NOT create a separate "assumption" row underneath a metric row. One row carries both: historical values (black text) on the left, assumption values (yellow bg + blue text + source comment) on the right.

**Correct:**
```
Y/Y Volume Growth    19.0%   (9.7%)   ...   25.0%   [2.0%]   [2.0%]   [1.5%]   [1.5%]
                     ← historicals (black) →         ← assumptions (yellow bg, blue text) →
```

**Wrong — do NOT create a separate assumption row:**
```
Y/Y Volume Growth    19.0%   (9.7%)   ...   25.0%
Volume Growth Assumption                             [2.0%]   [2.0%]   [1.5%]   [1.5%]
```

**Rules:**
- The row label stays the same across historical and projected periods (e.g., "Y/Y Volume Growth" — not renamed to "Volume Growth Assumption" in projections)
- Historical cells in that row: black text, computed from actuals (formula-driven)
- Projection cells in that row: yellow bg (#FFFF00) + blue text (#0000FF) + source comment — these ARE the assumptions
- Never collect assumptions into a separate block, section, or row

**When a driver metric differs from the output metric** (e.g., DSO days → AR balance, capex as % of revenue → capex dollars), the driver assumption and the output are inherently different rows. In this case, place the assumption row directly above or below the output row it drives — never in a separate assumptions block. The assumption row still follows the same-row pattern: historical values (black) on the left, projected assumptions (yellow/blue) on the right.

---

## Number Formats

**CRITICAL: Every numeric format includes `_)` right-padding** so parenthetical negatives align with positive numbers. This is non-negotiable.

### The Two Dollar Formats

There are exactly two monetary formats. The ONLY difference is the `$` sign:

| Name | Format String | Shows |
|------|--------------|-------|
| **Currency** | `$#,##0_);($#,##0);"-"` | `$1,234` |
| **Number** | `#,##0_);(#,##0);"-"` | `1,234` |

Which one to use is determined by the **Section Boundary Rule** (below).

### Other Formats

| Type | Format String | Usage | Notes |
|------|--------------|-------|-------|
| Per-share | `$#,##0.00_);($#,##0.00);"-"` | EPS, DPS, price per share | Always has `$`, always 2 decimals, regardless of row position |
| Percentage | `0.0%_);(0.0%);"-"` | Margins, growth rates, yields, rates | Rows are **italic** (both label and data cells) |
| Multiple | `0.0"x"_);(0.0"x");"-"` | EV/EBITDA, P/E, P/FCF, leverage ratios | The `"x"` suffix is literal |
| Integer | `#,##0` | Share counts, unit volumes | No `_)` padding needed (no parenthetical negatives expected) |
| Year | `@` (text format) | Year headers | Prevents "2,026" display |
| Date | `MM/DD/YYYY` | Date cells | |

### Negatives and Zeros
- **Negatives**: Always parentheses `(1,234)`, never minus sign `-1,234`
- **Zeros**: Always display as dash `"-"`, never `0` or blank

### Row Type Detection

The skill must **intelligently detect** what format each row needs. Key signals:

| Signal | Format to Apply |
|--------|----------------|
| Label contains "margin", "growth", "rate", "yield", "%", "as % of" | Percentage |
| Label contains "EPS", "DPS", "per share", "share price" | Per-share |
| Label contains "x", "multiple", "EV/", "P/E", "P/FCF", "leverage", "turns" | Multiple |
| Label contains "shares", "units", "volume", "count", "# of" | Integer |
| Row is in a monetary section (revenue, expense, balance, cash flow) | Currency or Number per Section Boundary Rule |

When detection is ambiguous, make a judgment call based on context. Don't ask -- just apply the most logical format.

---

## Section Boundary Rule (CRITICAL)

This rule controls where `$` signs appear within each monetary block.

**Definition**: A "monetary block" is a contiguous group of dollar-denominated line items between two structural rows (headers, subheaders, or total rows). It does NOT include percentage rows, multiple rows, or per-share rows even if they appear inline.

**Rule**:
| Row Position in Block | Format | Shows |
|----------------------|--------|-------|
| **First monetary row** | Currency `$#,##0_);($#,##0);"-"` | `$12,345` |
| **Middle monetary rows** | Number `#,##0_);(#,##0);"-"` | `12,345` |
| **Last monetary row / Subtotal / Total** | Currency `$#,##0_);($#,##0);"-"` | `$12,345` |

**Concrete example -- Revenue section:**
```
Revenue Drivers              (subheader, #C2D5EB fill)
Equipment Rentals   $12,345  ← first row: Currency (has $)
Sales of Rental       3,456  ← middle: Number (no $)
Sales of New          1,234  ← middle: Number (no $)
Service & Other         890  ← middle: Number (no $)
Total Revenue       $17,925  ← subtotal: Currency (has $), bold, thin top border
```

**Exceptions -- these ALWAYS use their own format regardless of position:**
- Percentage rows → percentage format, italic
- Multiple rows → multiple format with `"x"`
- Per-share rows → per-share format with `$#,##0.00`
- Dollar-denominated assumption rows (e.g., "Non-Rental Capex $mm") → always Currency format
- Single-line monetary items (only one row in the block) → Currency format

---

## Borders

| Context | Border | Weight |
|---------|--------|--------|
| Year row | Bottom only | **Medium** |
| Subtotal row | Top only | Thin |
| Major total row | Top only | Thin |
| All other rows | None | -- |

**Never use double-bottom borders.** Not on grand totals, not on check rows, not anywhere.

All borders span from label column through all year/data columns.

---

## Column Structure

- **Uniform data column widths**: All year/data columns must be the same width. Use ~13 characters wide (~95 pixels / ~90 Excel units) as default -- wide enough to display `($12,345,678)` without truncation.
- **Label column**: Width as needed for the longest label at max indent, plus comfortable padding. Typically 30-40 characters.
- **No merged cells**: Never merge cells in the data area.
- **No blank columns**: Use row spacers for separation, not column spacers.

---

## Tab Colors

| Category | Tabs | Color | Hex |
|----------|------|-------|-----|
| Primary output | Returns, Model | Dark navy | #1C3553 |
| Session management | Task Tracker | Amber/gold | #D4963A |
| Analytical build | Revenue Build, Costs Build, PP&E Build, Debt Build, WC Build, Capital Allocation Build, Tax Schedule, M&A Build | Steel blue | #5B8FA8 |
| Data capture | Historical Data | Dark Teal | #436E71 |
| Reference/external | BAMSEC, Tegus, broker sources, Data Pull, Data Pull (Values), Source Notes | Gray | #7C7F88 |

---

## Percentage and Margin Rows

- **Italic** for both the label cell and all data cells
- Use percentage format `0.0%_);(0.0%);"-"`
- Common examples: Gross Margin %, EBITDA Margin %, Revenue Growth %, Operating Margin %
- These rows are exempt from the Section Boundary Rule (they never get Currency or Number format)

---

## Python/openpyxl Reference

openpyxl constants for Python-based formatting are not included here — Claude for Excel uses cell-level operations, not Python. If Python formatting is needed, derive constants from the specifications above.

---

## Display Settings

Apply on every tab in the workbook:
- **Remove gridlines.** Gridlines off. The visual hierarchy provides all structure.
- **Do not freeze panes.** No freeze panes on any tab.

---

## Claude for Excel: Micro-Prompt Patterns

When operating inside Claude for Excel, the skill reformats by issuing cell-level operations. Key patterns:

### Reading Before Writing
Always read the current state of a section before reformatting. Identify:
1. Where section headers and subheaders are (by label text and/or existing formatting)
2. Where year rows are (by detecting year values like 2024, 2025E, etc.)
3. Where totals are (by label keywords: "Total", "Subtotal", "Gross Profit", "EBITDA", "Net Income", etc.)
4. What type each data row is (dollar, percentage, multiple, per-share, integer)
5. Which cells are hardcoded vs. formulas vs. cross-sheet refs

### Section-by-Section Execution
1. Process one section at a time (from section header to the row before the next section header or end of data)
2. Apply all formatting rules to that section
3. Run the self-check checklist
4. Move to the next section

### Handling Edge Cases
- **Row that is both the last row of one block and could be the "first" of the next**: Treat it as the total of the preceding block (Currency format, subtotal/major total tier).
- **Percentage row sitting between two monetary rows**: Does not break the monetary block for Section Boundary Rule purposes. The monetary rows above and below it still form a continuous block.
- **Single-item monetary block**: Apply Currency format (it's simultaneously first and last).
- **Label ambiguity**: If a label could be either a percentage or a dollar amount, check the actual cell values. If values are between -1 and 1, it's likely a percentage. If values are in the hundreds/thousands/millions, it's a dollar amount.
