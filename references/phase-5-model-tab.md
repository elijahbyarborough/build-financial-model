# Phase 5 — Model Tab

**Reference:** `references/phase-5-model-tab.md`
**Prerequisite:** Phase 4 complete (capital allocation built, share count finalized, IS has final diluted EPS).
**Next phase:** Phase 6 (Returns) → load `references/phase-6-returns.md`
**Also load:** `references/methodology.md` (ROIC/ROTIC section).

---

## What Happens in This Phase

Populate the Model tab — the master consolidation sheet where all model outputs are gathered. Created blank in Phase 0, now populated after all upstream tabs are complete.

The Model Tab serves two purposes:
1. **Internal reference** — one place to see every key metric across the full historical + projection time series
2. **Feed for Output and Returns tabs** — the Output tab pulls from the Model Tab (for IS, KPIs, Cap Alloc, ROIC) and the Returns tab (for the returns timeline). The Returns tab pulls Diluted EPS from the Model Tab and DPS/M&A Value from the Capital Allocation Build.

The Model Tab is a pure pull-sheet with one exception: the ROIC/ROTIC section contains original calculations (NOPAT, Invested Capital, returns). Everything else is a one-cell formula referencing another tab.

---

## Tab Structure — Section Order

The Model Tab has a fixed section hierarchy. The sections always appear in this order:

1. **Title Block & Column Headers** (rows 1-5)
2. **Summary Income Statement** (Tier 1 header)
3. **Key Drivers & KPIs** (Tier 1 header)
4. **Capital Allocation & Returns** (Tier 2 subheader) + **Capital Deployment** (Tier 2 subheader)
5. **ROIC / ROTIC** (Tier 1 header)
6. **Summary Balance Sheet** (Tier 1 header)
7. **Summary Cash Flow Statement** (Tier 1 header)

This order flows logically: how the company earns (IS) → what drives the earnings (KPIs) → what's done with the cash (Cap Alloc) → how efficiently capital is deployed (ROIC) → the resulting financial position (BS) → the cash flow reconciliation (CF).

---

## Title Block & Column Headers (Rows 1-5)

- **Row 1**: Company name + " - Model" (Tier 1 header, navy fill)
- **Row 2**: Units subtitle "($ in millions, except per share data)"
- **Row 3**: Blank spacer
- **Row 4**: Year type labels: "FY ####A" for historical, "FY ####E" for projections
- **Row 5**: Fiscal year-end dates

Column layout: Column A for labels, then one column per year spanning ALL historical + projection years. The number of columns depends on the model.

---

## Section 1: Summary Income Statement

Scan the IS tab and pull every major line item, maintaining the IS tab's own ordering.

### Required Pattern

- **Revenue** (bold) → *Revenue Growth %* (italic)
- Each major cost/expense line
- **Gross Profit** (bold) → *Gross Margin %* (italic)
- Operating expenses (SG&A and/or other categories as they appear on the IS)
- **EBIT** (bold) → *EBIT Margin %* (italic)
- D&A (so EBITDA can be shown)
- **EBITDA** (bold, Major Total F2F2F2 fill) → *EBITDA Margin %* (italic)
- Below-the-line items (Interest, EBT, Tax) as they appear on IS
- **Net Income** (bold, Major Total F2F2F2 fill) → *Net Margin %* (italic)
- Diluted EPS
- Diluted Shares Outstanding

### Flexibility Rules

- If the IS has additional operating expense lines (R&D, restructuring, etc.), include them in the same position they appear on the IS
- If the company reports Adjusted EBITDA or other non-GAAP metrics on the IS, include those too
- The two mandatory Major Totals are EBITDA and Net Income — both get F2F2F2 fill
- Every dollar line item that has a corresponding margin % on the IS should have that margin % row (italic) directly below it here
- All formulas are one-cell pulls: `=IS!cell`

---

## Section 2: Key Drivers & KPIs

Scan ALL build tabs (revenue builds, segment builds, etc.) and identify the key operating drivers. Each segment or business line gets its own Tier 2 subheader.

### How to Determine What to Pull

- Look at every Build tab in the model (anything with "Build" in the name that drives revenue or segment economics)
- For each segment/build, identify: volume/quantity metrics, pricing metrics, growth rates, segment revenue, segment profitability, unit economics, margins
- Mirror those metrics here in the same order they appear on the build tab

### Structural Pattern per Segment

- **Tier 2 Subheader**: Segment/Business Name (light blue fill #C2D5EB)
- Volume/quantity driver → growth %
- Pricing/ASP driver → growth %
- Segment revenue
- Segment gross profit (or contribution margin)
- Unit profitability metric (if applicable)
- Segment margin %

### Flexibility Rules

- The number of segments depends on the company — could be 1 (single-segment) or 5+
- The specific KPIs are industry-dependent:
  - **Materials/Manufacturing**: Volume, ASP, unit margins
  - **SaaS/Software**: ARR, net retention, customers, ARPU, CAC payback
  - **Retail**: Store count, same-store sales, revenue per sqft, average ticket
  - **Financial services**: AUM, fee rate, NIM, loan growth
  - **Healthcare**: Patient volumes, reimbursement rates, payor mix
- The skill should identify what the Build tabs track and mirror it — don't force metrics that don't exist in the model
- If there's a Consolidation tab that aggregates segments, its totals can appear after all segment blocks
- Blank spacer row between segments

---

## Section 3: Capital Allocation & Returns

Pull FCF generation and deployment metrics from the Capital Allocation Build tab.

### Tier 2 Subheader — "Capital Allocation & Returns"

| Row | Label | Source | Format |
|-----|-------|--------|--------|
| | Cash From Operations | `='Capital Allocation Build'!CFO` | Currency, green font |
| | Capital Expenditures | `='Capital Allocation Build'!Capex` | Number, green font |
| | **Free Cash Flow** | `='Capital Allocation Build'!FCF` | **Major Total** (F2F2F2 fill, bold, Currency) |
| | *FCF Margin %* | `=FCF / IS!Revenue` | 0.0%, italic |
| | *FCF Conversion %* | `=FCF / IS!Net Income` | 0.0%, italic |
| | *EBITDA Conversion %* | `=FCF / EBITDA` | 0.0%, italic |

### Tier 2 Subheader — "Capital Deployment"

| Row | Label | Source | Format |
|-----|-------|--------|--------|
| | Acquisitions, Net | `='Capital Allocation Build'!Acquisitions` | Currency |
| | Dividends Paid | `='Capital Allocation Build'!Dividends` | Number |
| | Gross Share Repurchases | `='Capital Allocation Build'!Buybacks` | Number |
| | **Total Capital Deployed** | `=SUM(Acquisitions + Dividends + Buybacks)` | Currency, bold |
| | *Payout %* | `=ABS(Dividends + Buybacks) / IS!Net Income` | 0.0%, italic |
| | *Acquisition-Adjusted Payout %* | `=ABS(All Deployed) / IS!Net Income` | 0.0%, italic |
| | (blank spacer) | | |
| | Net Debt / EBITDA | `='Debt Build'!Net Debt / EBITDA` | 0.0"x" |
| | Interest Coverage | `=EBITDA / ABS(IS!Interest Expense)` | 0.0"x" |

### Flexibility Rules

- If the Capital Allocation Build has additional deployment categories (e.g., minority investments, JV contributions), include them
- If the company doesn't pay dividends, still include the Dividends Paid row (it will show 0 or "-")
- Leverage and coverage metrics always come last in this section
- FCF Margin denominator should use total consolidated revenue, not segment revenue

---

## Section 4: ROIC / ROTIC

This is the ONE section with original calculations. The formulas are computed here, not pulled from another tab.

### Tier 1 Header — "ROIC / ROTIC"

**NOPAT Calculation:**

| Row | Label | Formula | Format |
|-----|-------|---------|--------|
| | EBIT | `=IS!EBIT` | Currency, green font |
| | *Effective Tax Rate* | `=ABS(IS!Tax / IS!EBT)` | 0.0%, italic |
| | **NOPAT** | `=EBIT * (1 - Effective Tax Rate)` | **Major Total** (F2F2F2 fill, bold, Currency) |

**Invested Capital:**

| Row | Label | Formula | Format |
|-----|-------|---------|--------|
| | Total Equity | `=BS!Total Equity` | Currency, green font |
| | Total Debt | `='Debt Build'!Total Debt` | Currency, green font |
| | Cash & Equivalents | `=-'Debt Build'!Cash` | Number (negated — subtracted from IC) |
| | **Invested Capital** | `=Equity + Debt - Cash` | **Major Total** (F2F2F2 fill, bold, Currency) |
| | *Average Invested Capital* | `=(Current IC + Prior IC) / 2` | Currency, italic |
| | **ROIC** | `=IF(Avg IC=0, "", NOPAT / Avg IC)` | 0.0%, bold |

**Tangible Invested Capital:**

| Row | Label | Formula | Format |
|-----|-------|---------|--------|
| | Goodwill | `=-BS!Goodwill` | Number (negated — subtracted) |
| | Intangible Assets | `=-BS!Intangibles` | Number (negated — subtracted) |
| | **Tangible Invested Capital** | `=IC - Goodwill - Intangibles` | **Major Total** (F2F2F2 fill, bold, Currency) |
| | *Average Tangible IC* | `=(Current + Prior) / 2` | Currency, italic |
| | **ROTIC** | `=IF(Avg Tangible IC=0, "", NOPAT / Avg Tangible IC)` | 0.0%, bold |

### Flexibility Rules

- If the company has no goodwill/intangibles (rare), the Tangible IC section can be omitted or will equal IC
- All return % rows use IF guards: `=IF(denominator=0, "", NOPAT/IC)`
- If the BS doesn't separately break out Goodwill and Intangibles, use whatever intangible asset lines exist

---

## Section 5: Summary Balance Sheet

Scan the BS tab and pull major category totals. The goal is a condensed view, not a line-by-line replica.

### Required Pattern

- Major current asset categories or Total Current Assets
- PP&E, Net
- Goodwill (if material)
- Intangible Assets (if material)
- Other Non-Current Assets (catch-all)
- **Total Assets** (bold, Major Total F2F2F2 fill)
- (blank spacer)
- Major current liability categories or Total Current Liabilities
- Long-Term Debt
- Other Non-Current Liabilities (catch-all)
- **Total Liabilities** (bold)
- **Total Equity** (bold)
- **Total Liabilities & Equity** (bold, Major Total F2F2F2 fill)
- BS Check → `=Total Assets - Total L&E` (must = 0)

### Flexibility Rules

- The level of detail depends on what the BS tab tracks — if it breaks out 10 current asset lines, the Model Tab can consolidate to 3-4 major categories
- Always show Goodwill and Intangibles separately (needed for ROTIC calculation above)
- BS Check is mandatory — it's a core model integrity check

---

## Section 6: Summary Cash Flow Statement

Scan the CF tab and pull major line items, plus the three FCF definitions.

### Required Pattern

**Operating:**
- Net Income
- D&A
- Stock-Based Compensation
- Other Operating / Working Capital (can be combined or broken out)
- **Cash From Operations** (bold)

**Investing:**
- Capital Expenditures
- Acquisitions
- Other Investing (catch-all)
- **Cash From Investing** (bold)

**Financing:**
- Net Debt Issuance / (Repayment)
- Share Repurchases
- Dividends Paid
- Other Financing (catch-all)
- **Cash From Financing** (bold)

**FCF Definitions:**
- **Unlevered FCF** (bold, Major Total F2F2F2 fill)
- **Levered FCF** (bold, Major Total F2F2F2 fill)
- **FCF to Equity** (bold, Major Total F2F2F2 fill)

**Cash Reconciliation:**
- Net Change in Cash
- Ending Cash Balance
- CF Check → must = 0

### Flexibility Rules

- All three FCF definitions should be pulled from the CF tab (where they're calculated)
- If the CF tab has additional operating items (deferred taxes, gain on sale, etc.), they can be grouped into "Other Operating" here
- The level of detail in financing should match what matters: if the company has complex debt, show Net LT Debt and Net ST Debt separately
- CF Check is mandatory — core model integrity check

---

## Cross-Tab Dependencies

### What Model Tab READS from

| Section | Source Tab(s) |
|---------|--------------|
| Summary IS | IS tab |
| Key Drivers & KPIs | Individual Build tabs (whatever exists) |
| Capital Allocation | Capital Allocation Build, Debt Build |
| ROIC / ROTIC | IS (EBIT, Tax Rate), BS (Equity, Goodwill, Intangibles), Debt Build (Debt, Cash) |
| Summary BS | BS tab |
| Summary CF | CF tab |

### What Model Tab is READ BY

| Consumer | What it pulls |
|----------|--------------|
| Output tab | IS summary, KPIs, Cap Alloc metrics, ROIC/ROTIC (Output also pulls returns timeline from Returns tab) |
| Returns tab | Diluted EPS (Returns also pulls DPS and M&A Value Per Share from Capital Allocation Build) |

---

## Formatting Conventions

### Headers

- **Tier 1** (IS, KPIs, ROIC, BS, CF): Navy fill (#1C3553), white bold text
- **Tier 2** (Cap Alloc, Deployment, each segment): Light blue fill (#C2D5EB)

### Data

- Historical: Green font (#008000) — all data cells
- Projection: Default black font
- $ items: `$#,##0_);($#,##0);"-"` (millions, no decimals)
- Per-share: `$#,##0.00_);($#,##0.00);"-"`
- Margins/%: `0.0%_);(0.0%);"-"`, italic
- Multiples: `0.0"x"_);(0.0"x");"-"`
- Shares: `0.0`

### Major Totals (F2F2F2 fill, bold, thin top border)

EBITDA, Net Income, NOPAT, Invested Capital, Tangible IC, Total Assets, Total L&E, uFCF, LFCF, FCFE

### Borders

- Thin solid above major totals
- Medium solid below Tier 1 headers
- No vertical borders

---

## Key Design Principles

1. **Scan, don't hardcode** — The skill should scan whatever tabs exist and assemble the Model Tab dynamically. Different companies = different segments = different KPIs = different BS/CF structures.
2. **Consolidation hub** — Every key metric lives here. The Output and Returns tabs pull from Model Tab, not from individual build tabs.
3. **One original calculation section** — ROIC/ROTIC is the only section with formulas that aren't simple pulls. Everything else is `=OtherTab!cell`.
4. **Full time series** — Spans all historical AND projection years.
5. **Two mandatory checks** — BS Check and CF Check, both must = 0.
6. **Faithful to source** — Don't invent metrics that aren't on the source tabs. Mirror what exists, condensed where appropriate.
7. **Section order is fixed, contents are flexible** — IS always comes first, then KPIs, then Cap Alloc, then ROIC, then BS, then CF. Within each section, the specific rows depend on the company.

---

## Key Rules

- **Output/summary layer only** — no assumption inputs. Every cell is a formula (except ROIC/ROTIC calculations).
- **Scan build tabs dynamically** — don't hardcode specific KPI rows. Mirror whatever the build tabs track.
- **ROIC/ROTIC formulas**: see `references/methodology.md` for the full specification.
- **All number formats defer to `firm-formatting.md`** — use standard format strings with `_)` padding.
- **No freeze panes. Gridlines off.**

**STOP. Update Task Tracker. Report status. Wait for "continue."**
