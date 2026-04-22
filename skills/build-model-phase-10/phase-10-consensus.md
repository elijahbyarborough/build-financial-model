# Phase 10 — Consensus Comparison

**Prerequisite:** Phase 9 complete (Output tab built). A consensus data source must exist in the workbook (Bloomberg "Multiple Periods" tab, FactSet dump, Capital IQ export, or user-provided estimates). If none exists, ask the user to provide one or offer to pull via Bloomberg/FactSet formulas.

---

## What Happens in This Phase

Build the Consensus tab — a model-vs-street comparison sheet that shows the user's proprietary model estimates side-by-side with sell-side consensus for every major KPI. It answers: "Where does my model differ from the street, and by how much?"

1. **Scan** the Model Tab and the consensus data source to identify overlapping KPIs
2. **Build** the stacked triplet layout (Model / Consensus / Delta for each KPI)
3. **Map** columns between Model Tab, consensus source, and Consensus tab dynamically
4. **Cross-check** at least one historical year where both sources should match actuals
5. **Apply** conditional formatting (green = above consensus, red = below)

---

## Key Design Principles

1. **Stacked triplets are the core pattern** — Model / Consensus / Delta for every KPI. Non-negotiable structure.
2. **Scan both sources dynamically** — don't hardcode KPIs. Include whatever exists in BOTH the Model Tab and the consensus source.
3. **Handle unit mismatches** — consensus data often stores margins as whole numbers, volumes in different units. Cross-check a historical year to validate.
4. **Suppress near-zero historical deltas** — where model and consensus both reflect actuals, the delta should show `"-"` not a tiny rounding difference.
5. **Green/red conditional formatting** — makes divergences visible instantly. Green = model above consensus, Red = model below.
6. **No F2F2F2 fills** — this isn't a financial statement with subtotal hierarchy. Keep it visually flat so the color-coded deltas are the focal point.
7. **3+3 year default** — 3 historical years + 3 forward years unless data availability dictates otherwise.

---

## Tab Structure

### Header Block (Rows 1-6)

| Row | Content | Format |
|-----|---------|--------|
| 1 | Company Name -- Model vs. Consensus | Tier 1 header (navy fill #1C3553, white bold) |
| 2 | ($ in millions, except per share data) | Italic, Arial 10 |
| 3 | Blank spacer | |
| 4 | Year labels: "FY 2023A", "FY 2024A", ..., "FY 2028E" | Bold, centered, thin bottom border |
| 5 | Dates: "12/31/23", "12/31/24", ..., "12/31/28" | Italic, centered, text format, thin bottom border |
| 6 | Blank spacer | |

### Column Layout

- **Column A**: KPI labels (row names)
- **Column B**: Sub-row labels: "Model", "Consensus", "Delta" (identifies which of the three stacked rows)
- **Columns C onward**: One column per year. Typically 6 years (3 historical + 3 forward)

### Key Formatting

- **Gray medium vertical border** between the last historical column and the first projection column — visually separates actuals from estimates
- Year labels do NOT have colored fills
- Rows 4 and 5 have thin bottom borders

---

## Stacked Triplet Pattern (The Core Layout)

For every KPI, there are exactly three rows:

| Row | Col B Label | Font Style | Content |
|-----|-------------|-----------|---------|
| Model | "Model" (bold) | Bold, black, Arial 10 | `='Model Tab'!{col}{row}` |
| Consensus | "Consensus" (italic gray) | Italic, gray (#808080), Arial 10 | `='Consensus Source'!{col}{row}` |
| Delta | "Delta" (italic lighter gray) | Italic, lighter gray (#999999), Arial 9 | Delta formula (see below) |

After each KPI's triplet, there is a **light gray thin bottom border** on the Delta row to visually separate it from the next KPI group.

### Row Label Formatting

- **Column A**: KPI labels are bold for dollar items, bold italic for margin %. The label appears on the Model row only; Consensus and Delta rows have blank Column A.
- **Column B**: "Model" = bold black; "Consensus" = italic gray (#808080); "Delta" = italic lighter gray (#999999), font size 9.

---

## Delta Formulas

The delta formula depends on the KPI type:

### Dollar Items (Revenue, GP, EBITDA, NI, Capex, FCF, etc.)

```
=IF(OR(consensus=0, ABS(model/consensus - 1) < 0.0005), "-", model/consensus - 1)
```
Format: `0.0%` — shows percentage difference (e.g., "6.1%" means model is 6.1% above consensus).

The `ABS < 0.0005` threshold suppresses near-zero deltas (common in historical years where both source actual data) and displays `"-"`.

### Margin / Percentage Items (GP%, EBITDA%, NI%, etc.)

```
=IF(OR(consensus=0, ABS((model - consensus) * 10000) < 0.5), "-", (model - consensus) * 10000)
```
Format: `0" bps"` — shows basis point difference (e.g., "200 bps" means model margin is 200bps above).

**Unit mismatch warning**: Consensus sources often store margins as whole numbers (25.04 = 25.04%) while the Model Tab stores them as decimals (0.2504). The consensus formula must handle this with `/100` when pulling from Bloomberg-style sources.

### Per-Share Items (EPS)

```
=IF(OR(consensus=0, ABS(model/consensus - 1) < 0.0005), "-", model/consensus - 1)
```
Format: `0.0%` — same as dollar items.

### Multiples (Net Debt/EBITDA, Interest Coverage)

```
=IF(OR(consensus=0, ABS(model - consensus) < 0.05), "-", model - consensus)
```
Format: `0.0"x"` — shows absolute difference in turns (e.g., "0.3x" means model leverage is 0.3 turns higher).

---

## Conditional Formatting on Delta Rows

All delta rows have two conditional formatting rules:

- **Positive values** → dark green font (#006100): Model is above consensus
- **Negative values** → dark red font (#9C0006): Model is below consensus

This makes divergences immediately visible at a glance.

---

## Section Order

The Consensus tab mirrors the Model Tab's section structure:

1. **Summary Income Statement** (Tier 1 header)
2. **Key Drivers & KPIs** (Tier 1 header) — with Tier 2 subheaders per segment
3. **Capital Allocation & Returns** (Tier 1 header)

---

## Section 1: Summary Income Statement

KPIs to include (scan Model Tab and consensus source, include whatever exists in both):

- Total Revenue ($ + % delta)
- Gross Profit ($ + % delta)
- *Gross Margin %* (% + bps delta)
- EBIT / Operating Income ($ + % delta)
- *EBIT Margin %* (% + bps delta)
- D&A ($ + % delta)
- EBITDA ($ + % delta)
- *EBITDA Margin %* (% + bps delta)
- Net Income ($ + % delta)
- Diluted EPS ($/share + % delta)

### Flexibility Rules

- Only include KPIs that exist in BOTH the Model Tab and the consensus source — if consensus doesn't have EBIT, skip it
- If the consensus source has Adjusted EBITDA but the model uses reported EBITDA, show both with clear labeling
- Margin % rows should be italic (matching firm-formatting convention)
- No F2F2F2 major total fills on this tab — keep all data rows on white background

---

## Section 2: Key Drivers & KPIs

Structure: Tier 2 subheader for each segment, then the relevant KPIs for that segment.

What to include per segment (scan both sources):

- Volume/quantity metrics (if consensus has them)
- Pricing metrics (if consensus has them)
- Segment revenue
- Segment gross profit
- *Segment gross margin %*

### Flexibility Rules

- Only include segments that exist in both sources
- Not all consensus sources have segment-level data — if consensus only has consolidated metrics, skip this section or note "Consensus segment data not available"
- Some consensus sources have revenue by segment but not GP — include what's available, leave Model column blank where there's no model equivalent (and vice versa)

---

## Section 3: Capital Allocation & Returns

KPIs to include:

- Cash From Operations ($ + % delta)
- Capital Expenditures ($ + % delta)
- Free Cash Flow ($ + % delta)
- Net Debt / EBITDA (multiple format, absolute delta in turns)

### Flexibility Rules

- Leverage metrics use absolute delta (in turns), not percentage delta
- If consensus has additional metrics (dividends, share count, etc.), include them

---

## Column Mapping

The skill must map columns between three tabs:

- **Model Tab columns** → the year columns on that tab (e.g., I=FY2023, J=FY2024, ..., N=FY2028)
- **Consensus source columns** → the year columns on the consensus data tab (must be identified by reading the header row)
- **Consensus tab columns** → the output columns (C=first year, D=second year, etc.)

The skill should **read the column headers from both source tabs** to build the mapping dynamically — do NOT hardcode column letters.

---

## Consensus Data Format Handling

Bloomberg and FactSet consensus data often stores numbers differently from the model:

| Data Type | Common Issue | Fix |
|-----------|-------------|-----|
| Margins | Stored as whole numbers (25.04 = 25.04%) vs. model decimals (0.2504) | Divide by 100 in consensus formula |
| Revenue/EBITDA | Usually same units (millions) but verify | Compare a known historical year |
| EPS | Usually comparable directly | Verify |
| Volume/quantity | May use different units (thousands vs. millions) | Verify against a known historical period |

**Validation rule**: Cross-check at least one historical year where both sources should match actuals. If there's a significant discrepancy (>1%), the units or row mapping is likely wrong. Fix before proceeding.

---

## Cross-Tab Dependencies

### What Consensus READS from

| Source | Role |
|--------|------|
| Model Tab | All "Model" row values |
| Consensus data source (e.g., "Multiple Periods") | All "Consensus" row values |

### What Consensus WRITES to

**Nothing.** Pure read-only comparison sheet.

---

## Formatting Conventions

### Number Formats

| Type | Format |
|------|--------|
| $ items (model and consensus) | `$#,##0_);($#,##0);"-"` |
| Per-share (EPS, ASP) | `$#,##0.00_);($#,##0.00);"-"` |
| Margins | `0.0%_);(0.0%);"-"` |
| Multiples | `0.0"x"_);(0.0"x");"-"` |
| $ deltas | `0.0%_);(0.0%);"-"` (percentage difference) |
| Margin deltas | `0" bps"` (basis points) |
| Multiple deltas | `0.0"x"_);(0.0"x");"-"` (absolute turns) |
| Suppressed deltas | display as `"-"` (text dash) |

### Headers

- **Tier 1** (Summary IS, Key Drivers, Capital Allocation): Navy fill (#1C3553), white bold text
- **Tier 2** (segment names): Light blue fill (#C2D5EB)

### Borders

- Thin bottom border on rows 4 and 5 (year headers)
- Light gray thin bottom border on every Delta row (separates KPI triplets)
- No other borders (except vertical dividers below)

### Vertical Divider Borders

A single vertical divider separates historical columns from projection columns — medium solid #808080 on the right edge of the last historical column and the left edge of the first projection column.

**Section-scoped dividers**: Vertical borders are scoped to each Tier 1 section, running from the section header row through the last data row (including the final Delta row) in that section. They do not run continuously across the entire tab. Spacer rows between sections have no vertical dividers, creating clean visual breaks between:

- **Summary Income Statement** (header → last Delta row)
- **Key Drivers & KPIs** (header → last Delta row)
- **Capital Allocation & Returns** (header → last Delta row)

### Freeze Panes

**No freeze panes.** Consistent with all other tabs.

---

## Key Rules

- **Pure pull-sheet** — contains NO assumptions or drivers. Every cell is a formula.
- **Stacked triplets** — Model / Consensus / Delta for every KPI. No exceptions.
- **Scan dynamically** — include KPIs that exist in both sources. Don't force metrics that aren't available.
- **Cross-check units** — validate at least one historical year before building forward columns.
- **Conditional formatting** — green (#006100) for positive deltas, red (#9C0006) for negative.
- **No F2F2F2 fills** — white background on all data rows.
- **No freeze panes. Gridlines off.**

---

## Tab Completion Verification

Before reporting the Consensus tab complete, read and paste:

```
TAB VERIFICATION -- Consensus:
  freezePanes: [null -- if not null, STOP and fix]
  gridlines: [off -- if on, STOP and fix]
  font: [Arial 10pt -- if not, STOP and fix]
  tab color: [hex] -- expected: #1C3553
  KPI triplet count: [count] (Model/Consensus/Delta groups)
  Historical cross-check (1 year): Model Revenue=[val], Consensus Revenue=[val], match=[yes/no]
  Conditional formatting applied (green/red on deltas): [yes/no]
  Vertical divider between historical/projection columns: [present/missing]
```

If you do not paste this output, the user cannot verify compliance. No output = not verified.

---

## Definition of Done (Phase 10)

A phase is complete if and only if ALL of the following are true. Report completion by reading these values back to the user -- not by summarizing in prose.

1. **Task Tracker**: Every subtask row for Phase 10 shows Status = "COMPLETE". Cite the actual cell addresses you checked.
2. **All stacked triplets populated** (Model/Consensus/Delta for every overlapping KPI).
3. **Delta formulas correct by type**: % difference for $ items, bps for margins, absolute turns for multiples.
4. **Historical cross-check** passed: at least 1 year where Model and Consensus both reflect actuals, with near-zero delta.
5. **Conditional formatting** applied: green (#006100) for positive deltas, red (#9C0006) for negative.
6. **Tab Completion Verification** output pasted.
7. **Task Tracker Model State Block**: "Last Skill Run" updated, "Next Skill" = "build-model-phase-11".

If you write "Phase 10 complete" in chat before reading and reporting these values, you have made an error. Re-verify and correct.

**STOP. Report status. Wait for "continue."**
