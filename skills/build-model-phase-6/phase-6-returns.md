# Phase 6 — Returns

**Reference:** `references/phase-6-returns.md`
**Prerequisite:** Phase 5 complete (Model tab populated).
**Next phase:** Phase 7 (Formatting) → load `references/phase-7-formatting.md`

---

## What Happens in This Phase

Build the Returns tab — a compact, single-section analysis grid with YEARFRAC calendarization, M&A value tracking, dividend income, dual cash flow rows, and a full IRR decomposition into four named components.

1. **Entry & exit assumptions**: entry price, entry date, YEARFRAC calendarization
2. **Valuation grid**: calendarized EPS, implied entry P/E, exit P/E assumption, EPS-implied price, M&A value per share, total price
3. **Dividend income**: YEARFRAC-weighted DPS received per period
4. **Dual cash flow rows**: Net Cash Flow (total) and Core Cash Flow (ex-M&A)
5. **IRR decomposition**: EPS CAGR, M&A Value, Dividend Yield, Multiple Change, total IRR

---

## Tab Structure — Layout Overview

The Returns tab has a compact, single-section design with three Tier 1 headers:

1. **Company Name — Returns Analysis** (title row)
2. **Entry & Exit Assumptions** (input section)
3. **5-Year Forward Valuation & Return Analysis** (the analysis grid)

Unlike the build tabs, this tab is NOT organized as a long vertical build. It is a wide grid with columns representing: **Entry, each FY projection year, and Exit**. The analysis flows top-to-bottom within each column.

---

## Section 1: Entry & Exit Assumptions (5 rows)

| Row | Label | Value/Formula | Format | Notes |
|-----|-------|--------------|--------|-------|
| 1 | Entry Price ($/share) | Blue/yellow assumption (e.g. $266.54). Can use `=CIQ()` if available | $#,##0.00, blue/yellow | The price at which the investor enters the position |
| 2 | Entry Date | `=TODAY()` or user-specified date | mm/dd/yyyy | Drives all YEARFRAC calculations |
| 3 | Next FYE Date | `=DATE(YEAR(Entry Date), 12, 31)` | mm/dd/yyyy | The next fiscal year-end after entry. Adjust for non-Dec FYE companies |
| 4 | Year Fraction | `=YEARFRAC(Entry Date, Next FYE Date, 1)` | 0.0%, italic | The fraction of the first fiscal year remaining after entry. Critical for calendarization. |
| 5 | Exit Multiple Type | "P/E" (text) | Text | Identifies which multiple is used for exit valuation |

### Key Design Decisions

- **Entry Date = `TODAY()`** makes the model dynamic — returns update daily as the holding period changes
- **Year Fraction** is the master calendarization input. If entry is mid-year (e.g. March 15 with Dec FYE), Year Fraction ~ 0.79, meaning ~79% of the first fiscal year's earnings accrue to the holding period
- **YEARFRAC uses basis=1** (Actual/Actual) for precision
- **Next FYE Date** handles the fiscal year boundary. For companies with non-December FYEs, this formula needs adjustment (e.g., `=DATE(YEAR(Entry Date), 6, 30)` for June FYE)

---

## Section 2: Column Headers

| Column | Label | Date Formula |
|--------|-------|-------------|
| Entry | "Entry" | `=Entry Date` |
| FY Year 1 | First projection year label | `=DATE(first projection year, 12, 31)` |
| FY Year 2 | Second projection year label | `=DATE(second projection year, 12, 31)` |
| ... | ... | ... |
| FY Year N | Last projection year in holding period | `=DATE(last projection year, 12, 31)` |
| Exit | "Exit" | `=DATE(YEAR(Entry Date) + 5, MONTH(Entry Date), DAY(Entry Date))` |

### Key Design Decisions

- The **Exit date is exactly 5 years from Entry Date**, NOT a fiscal year-end. This creates a 5-year holding period IRR.
- The FY columns correspond to each projection fiscal year-end that falls within the 5-year holding period — the actual years depend on the model's projection period (e.g., if the model is built in 2025, they would be FY 2026E-2031E; if built in 2027, they would be FY 2028E-2033E)
- Column header row is bold, center-aligned
- Date row is italic, center-aligned, mm/dd/yy format
- The number of FY columns = the number of projection years that fall within the 5-year holding period (typically 5-6 FY columns plus Entry and Exit)

---

## Section 3: Valuation Grid

| Row | Label | Entry Column | Middle FY Columns | Exit Column | Format |
|-----|-------|-------------|-------------------|-------------|--------|
| 1 | Diluted EPS | `=YearFrac * FY1_EPS + (1-YearFrac) * FY2_EPS` | `=Model Tab!EPS` (green pull) | `=YearFrac * LastFY_EPS + (1-YearFrac) * NextFY_EPS` | $#,##0.00, per-share |
| 2 | Exit P/E | `=Entry Price / Entry EPS` (implied, bold) | blank or n/a | Blue/yellow assumption (e.g. 26.0x) | 0.0"x" multiple format |
| 3 | EPS-Implied Price | `=Entry Price` (=$D$5) | blank | `=Exit EPS * Exit P/E` | **Major total** ($#,##0.00, F2F2F2 fill) |
| 4 | M&A Value Per Share | blank or 0 | `=Cap Alloc Build!M&A Value Per Share` (green pull) | `=YearFrac * LastFY_M&A + (1-YearFrac) * NextFY_M&A` | Italic, green font, $#,##0.00 |
| 5 | Total Price | `=EPS-Implied Price` (=Entry Price) | blank | `=EPS-Implied Price + M&A Value Per Share` | **Major total** ($#,##0.00, F2F2F2 fill) |

### Calendarization Mechanics (CRITICAL)

The entry and exit dates almost never fall on fiscal year-ends. The model must calendarize EPS (and M&A Value) to reflect partial-year ownership:

**Entry EPS** = `Year Fraction * FY1 EPS + (1 - Year Fraction) * FY2 EPS`

This blends the first two fiscal years' EPS proportional to how much of each fiscal year falls within the holding period start.

**Exit EPS** = `Year Fraction * Last FY EPS + (1 - Year Fraction) * Next FY EPS`

Same blending logic at exit. The Year Fraction is reused because Entry and Exit dates are exactly 5 years apart, so they have the same position within their respective fiscal years.

### Entry P/E Is IMPLIED, Not Assumed

- `Entry P/E = Entry Price / Calendarized Entry EPS`
- This is a derived metric, NOT a blue/yellow assumption
- It shows the implied multiple the investor is paying at entry
- Bold formatting distinguishes it as a computed value

### Exit P/E IS the Key Assumption

- Exit P/E is a blue/yellow assumption (e.g. 26.0x)
- This is the thesis on what multiple the market will assign at exit
- It is one of the most important assumptions in the entire model

### M&A Value Per Share

- Middle FY columns pull from Capital Allocation Build's M&A Value Per Share row (green font)
- Exit column calendarizes using the same YEARFRAC blend as EPS
- This represents the per-share value created by the M&A program, additive to the earnings-based valuation
- Entry column is typically blank or zero (no M&A value accrued yet at entry)

### Total Price = EPS-Implied Price + M&A Value Per Share

- This construct separates the "organic" earnings-driven value from M&A-driven value accretion
- At entry, Total Price = Entry Price (no M&A value yet)
- At exit, Total Price = (Exit EPS x Exit P/E) + M&A Value Per Share
- This split is key for the IRR decomposition later

---

## Section 4: Dividend Income

| Row | Label | Entry | Middle FY Columns | Exit | Format |
|-----|-------|-------|-------------------|------|--------|
| 1 | Annual DPS | blank | `=Cap Alloc Build!DPS row` (green pull) | blank | $#,##0.00, green font |
| 2 | YEARFRAC Weight | blank | First FY: `=Year Fraction`. Middle FYs: `1`. Last FY: `=1-Year Fraction` | blank | 0.000"x", italic, gray font (#7C7F88) |
| 3 | DPS Received | blank | `=Annual DPS * YEARFRAC Weight` | blank | $#,##0.00, bold |

### Key Design Decisions

- **Annual DPS** pulls from the Dividend Policy section of Capital Allocation Build (green font cross-tab reference)
- **YEARFRAC Weight** accounts for partial ownership in the first and last fiscal years:
  - First FY: Weight = Year Fraction (you only own shares for the remaining portion of the fiscal year)
  - Middle FYs: Weight = 1 (full year of dividends)
  - Last FY: Weight = 1 - Year Fraction (you sell partway through the fiscal year)
- **DPS Received** = Annual DPS x Weight gives the actual dividend income received in each period
- Gray font (#7C7F88) for the YEARFRAC Weight row signals it is a helper/intermediate calculation
- The YEARFRAC Weight row uses a multiplier format (0.000"x") which clearly communicates it is a weighting factor

---

## Section 5: Cash Flow Rows

| Row | Label | Entry | Middle FY Columns | Exit | Format |
|-----|-------|-------|-------------------|------|--------|
| 1 | Net Cash Flow | `=-Entry Price` (negative, cash out) | `=DPS Received` | `=Total Price` (positive, cash in) | **Major total** ($#,##0.00, F2F2F2 fill, bold) |
| 2 | Core Cash Flow (ex-M&A) | `=-Entry Price` | `=DPS Received` | `=EPS-Implied Price` (excludes M&A) | Italic, gray font (#7C7F88), $#,##0.00 |

### Key Design Decisions

- **Net Cash Flow** is the XIRR input — it represents the complete investor cash flow: pay Entry Price at entry, receive dividends during holding, receive Total Price (including M&A value) at exit
- **Core Cash Flow (ex-M&A)** strips out M&A value from the exit price. This isolates the "organic" return (EPS growth + multiple change + dividends) from M&A value accretion
- Entry is always negative (cash outflow to buy shares)
- Middle columns = DPS Received (cash inflows from dividends, same for both rows)
- Exit for Net Cash Flow = Total Price (EPS-Implied + M&A Value)
- Exit for Core Cash Flow = EPS-Implied Price only (no M&A Value)
- The difference between `XIRR(Net Cash Flow)` and `XIRR(Core Cash Flow)` = the M&A contribution to IRR

---

## Section 6: IRR Decomposition

This is the most analytically powerful part of the Returns tab. It decomposes the total IRR into four named components:

| Row | Label | Formula | Format | Notes |
|-----|-------|---------|--------|-------|
| 1 | EPS CAGR | `=(Exit EPS / Entry EPS)^(1/YEARFRAC(Entry Date, Exit Date, 1)) - 1` | 0.0%, italic | Annualized EPS growth over holding period |
| 2 | M&A Value | `=IRR - XIRR(Core Cash Flow)` | 0.0%, italic | Residual: total return minus organic return = M&A contribution |
| 3 | Dividend Yield | `=SUM(all DPS Received) / Entry Price / YEARFRAC(Entry Date, Exit Date, 1)` | 0.0%, italic | Annualized dividend income as % of entry price |
| 4 | Multiple Change | `=(Exit P/E / Entry P/E)^(1/YEARFRAC(Entry Date, Exit Date, 1)) - 1` | 0.0%, italic | Annualized P/E expansion or compression |
| 5 | **IRR** | `=XIRR(Net Cash Flow, Dates)` | 0.0%, bold italic, F2F2F2 fill | Total return: `=IFERROR(XIRR(range, dates), "")` |

### Key Design Decisions

- The four components are **additive approximations** — they will not sum exactly to IRR because of compounding interactions, but they show the SOURCE of returns
- **EPS CAGR** is the primary earnings growth engine. Uses compound annual growth rate formula on calendarized entry/exit EPS.
- **M&A Value** is calculated as a residual: total IRR minus the XIRR of Core Cash Flow (ex-M&A). This isolates exactly how much of the return comes from the M&A portfolio value build.
- **Dividend Yield** is the annualized cash income. Simple average: total dividends received / entry price / years held.
- **Multiple Change** captures P/E expansion or compression. If exit P/E > entry P/E, this is positive (tailwind). If exit P/E < entry P/E, this is a headwind.
- **IRR** uses `=IFERROR(XIRR(...), "")` to handle edge cases where XIRR cannot converge
- All decomposition rows are italic (convention for derived/analytical metrics)
- IRR row is bold italic with F2F2F2 fill (major total treatment)
- The decomposition rows only appear once (not per-column) — they are summary statistics for the entire holding period. They sit in the Entry/first data column.

---

## XIRR Input Arrays

The XIRR function requires two aligned arrays: cash flows and dates.

**Net Cash Flow array (for IRR):**

```
Dates: [Entry Date, FY1 Date, FY2 Date, ..., FYn Date, Exit Date]
CFs:   [-Entry Price, DPS Received 1, DPS Received 2, ..., DPS Received n, Total Price]
```

**Core Cash Flow array (for organic return):**

```
Dates: [Entry Date, FY1 Date, FY2 Date, ..., FYn Date, Exit Date]
CFs:   [-Entry Price, DPS Received 1, DPS Received 2, ..., DPS Received n, EPS-Implied Price]
```

The only difference is the exit cash flow: Total Price vs. EPS-Implied Price.

---

## Cross-Tab Dependencies

### What Returns READS from Other Tabs

| Data Point | Source | Cell Pattern |
|-----------|--------|-------------|
| Diluted EPS (each projection year) | Model Tab | `='Model Tab'!L29`, `='Model Tab'!M29`, etc. |
| M&A Value Per Share (each projection year) | Capital Allocation Build, M&A Value Per Share row | `='Capital Allocation Build'!L30`, etc. |
| Annual DPS (each projection year) | Capital Allocation Build, DPS row | `='Capital Allocation Build'!L47`, etc. |

### What Returns WRITES to Other Tabs

**Nothing.** The Returns tab is a pure output/analysis tab. It reads from Model Tab and Cap Alloc Build but drives nothing else.

### Dependency Chain

```
IS Tab (Net Income, EPS)
    |
    v
Model Tab (consolidates EPS)
    |
    v
Capital Allocation Build (M&A Value Per Share, DPS)
    |
    v
Returns Tab (reads EPS, M&A Value, DPS -> computes IRR)
```

---

## Sign Conventions

- **Entry Price / initial cash flow**: NEGATIVE (cash out to buy shares). Formula: `=-Entry Price`
- **Dividend receipts**: POSITIVE (cash in from dividends)
- **Exit value**: POSITIVE (cash in from selling shares at exit price)
- All values are on a **PER-SHARE** basis (this is an equity returns tab, not enterprise)

---

## Key Assumptions Summary

Only THREE assumptions on this entire tab. Everything else is derived from these three inputs plus data pulled from Model Tab and Cap Alloc Build. This is by design — the Returns tab is a pure output, not an input driver.

| Assumption | Location | Default | Format | Source Comment Guidance |
|-----------|----------|---------|--------|----------------------|
| Entry Price ($/share) | Entry Assumptions | Current share price or `=CIQ()` | $#,##0.00, blue/yellow | "Source: market data as of [date]" |
| Entry Date | Entry Assumptions | `=TODAY()` | mm/dd/yyyy | Dynamic; updates daily |
| Exit P/E | Valuation Grid, Exit column | Company-specific (e.g. 26.0x) | 0.0"x", blue/yellow | "Source: Analyst assumption -- [rationale, e.g. 5yr avg NTM P/E]" |

---

## Formatting Notes (defer to firm-formatting)

- **EPS-Implied Price**, **Total Price**, **Net Cash Flow**, **IRR**: Major Totals (F2F2F2 fill, bold)
- **DPS Received**: bold (sub-total level)
- **M&A Value Per Share**: italic with green font (cross-tab pull that is also a key output)
- **Entry P/E**: bold (implied/derived, not an assumption)
- **Exit P/E**: bold, blue/yellow (the key exit assumption)
- All decomposition rows (EPS CAGR, M&A Value, Dividend Yield, Multiple Change): italic
- **Core Cash Flow (ex-M&A)** row: gray font (#7C7F88) — helper row, not a primary output
- **YEARFRAC Weight** row: gray font (#7C7F88) — helper/intermediate
- Column headers: bold, center-aligned
- Date row: italic, center-aligned, mm/dd/yy format
- Per-share values: $#,##0.00 format
- Multiples: 0.0"x" format
- Percentages: 0.0% format

---

## Key Rules

- **Exit-multiple preferred.** Hierarchy: FCF > P/E > EBIT > EBITDA.
- **Only 3 hardcoded assumptions**: entry price, entry date, and exit multiple. Everything else is formula-driven.
- **Entry P/E is implied** (= Entry Price / calendarized Entry EPS), never assumed.
- **NTM EPS uses YEARFRAC blending** at entry and exit.
- **Total Price = EPS-Implied Price + M&A Value Per Share** — separates organic from M&A value.
- **Dividend counting is YEARFRAC-weighted**: partial at entry, full in middle, partial at exit.
- **Dual cash flow rows**: Net (total) and Core (ex-M&A) enable M&A IRR attribution.
- **IRR decomposes into 4 components**: EPS CAGR, M&A Value, Dividend Yield, Multiple Change.
- **Pulls from Model Tab** for EPS and from **Cap Alloc Build** for M&A Value Per Share and DPS.
- **No freeze panes. All formatting defers to `firm-formatting`.**

**STOP. Update Task Tracker. Report XIRR and results. Wait for "continue."**
