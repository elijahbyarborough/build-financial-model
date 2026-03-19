# Data Standards & Quality Gates

Rules for data sourcing, SPG() formulas, comps construction, formula discipline, and the quality gate checklist. Load this reference when working with external data, building comps, or running final QC.

---

## Data Source Hierarchy

Use the highest-ranked available source. Never skip a tier unless the data genuinely doesn't exist there.

1. **Company filings** (10-K, 10-Q, proxy, earnings releases) — primary, always preferred
2. **Tegus models & management guidance** — secondary, especially for forward-looking assumptions
3. **Equity research & Capital IQ consensus** — tertiary, for triangulation and sanity-checking only
4. **Qualitative sources** (transcripts, memos, industry reports) — context and color, not hard numbers

---

## SPG() Formula Patterns

Three parameter patterns for S&P Capital IQ formulas:

| Pattern | # Params | Example |
|---------|----------|---------|
| Spot market data | 3 (entity, field, date) | `=SPG("NYSE:URI","SP_LASTSALEPRICE",$B$2)` |
| Period-based market | 4 (entity, field, period, date) | `=SPG("NYSE:URI","SP_MARKETCAP_OUT","LTM",$B$2)` |
| Fundamental/estimate | 4 (entity, field, period, date) | `=SPG("NYSE:URI","IQ_EBITDA","FY2025",$B$2)` |

### SPG() Rules
- **Entity format**: Always `Exchange:Ticker` (e.g., NYSE:URI, NASDAQGS:WSC). Never just the ticker.
- **Date parameter**: Always an anchored cell reference `$B$2` pointing to a central "As-Of Date" cell. Never hardcode dates in formulas.
- **Ticker reference**: Use an anchored cell reference `$B$5` so rows copy easily across peers.
- **Font color**: Red text (#FF0000) per firm-formatting color coding.
- For the full SPG() mnemonic reference, see the CIQ formula documentation.

---

## Source Comments

**Every hardcoded input needs a source comment.** No exceptions.

### Format
```
Source: [Document Type], [Date], [Specific Reference]
```

### Examples
- `Source: URI 10-K (FY2025), p.47, Segment Revenue Table`
- `Source: Management guidance, Q4 2025 earnings call`
- `Source: Capital IQ consensus, retrieved 2026-03-03`
- `Source: Tegus model (analyst: [Name]), received 2026-02-15`
- `Source: Analyst assumption — based on 5yr historical average DSO of 42 days`

### Rules
- Only ASCII characters in comments (no em dashes, smart quotes, special characters)
- If an assumption is your own judgment (not sourced from a document), say so explicitly: "Source: Analyst assumption — [rationale]"
- Update the source comment whenever the value changes

---

## Comps Construction

### Peer Selection
- **3-5 publicly-traded peers** with US exchange:ticker identifiers (NYSE, NASDAQGS)
- Select peers based on: business model similarity, end-market overlap, size proximity, margin/growth profile
- Mark acquired or private companies as **inactive** — no SPG() formulas for inactive peers
- CIQ is the primary data source for comps

### Comps Tab Layout
1. **Company header block**: Ticker, name, market cap, EV, last price, shares outstanding
2. **Trading multiples**: EV/Revenue, EV/EBITDA, EV/EBIT, P/E, P/FCF — LTM and NTM
3. **Growth metrics**: Revenue growth, EBITDA growth — LTM and NTM
4. **Margin metrics**: Gross margin, EBITDA margin, net margin — LTM
5. **Summary statistics**: Mean, median, 25th percentile, 75th percentile for each metric
6. **Subject company row**: Show where the subject company falls vs. peers

### Multiple Calibration (feeds into Returns)
From the comps tab, extract:
1. Historical 5yr trading range of the subject company's multiple (median, 25th, 75th)
2. Current peer median and range
3. Premium/discount assessment based on growth and quality differential

These three data points calibrate the exit multiple assumption on the Returns tab.

---

## Formula Construction Rules

### Always
- Use Excel formulas — never hardcode calculations in Python and paste static values
- Historical totals must be formulas (`=SUM()`, `=subtotal1 + subtotal2`, etc.), not hardcoded even if they match the filing
- All assumptions live in separate cells with cell references — yellow background (#FFFF00) + blue text (#0000FF) + source comment on every assumption cell
- Comment every assumption with rationale and source

### Never
- Never hardcode a calculation result as a static value
- Never embed assumptions inside formulas — pull them out into labeled cells
- Never use non-ASCII characters in comments (no em dashes `—`, smart quotes `""`, etc.)
- Never paste values over live formulas without creating a separate "Values" snapshot tab

---

## Balance Sheet Integrity Checks

### Required Check Rows

**BS Check** (on the BS tab):
```
BS Check = Total Assets - Total Liabilities & Equity
```
Must equal zero in every period. If non-zero, the model has a structural error — stop and debug.

**CF Check** (on the CF tab):
```
CF Check = Beginning Cash + Total Cash Flow from All Activities - Ending Cash
```
Must equal zero in every period. If non-zero, there's a missing cash flow item or a BS/CF linkage error.

### Check Row Formatting
- Show the check value in every period column
- If zero: display as `"-"` (the standard zero format handles this)
- If non-zero: the value will display as a number, making the error immediately visible

---

## 6 Quality Gates

Every model must pass all 6 gates before presentation. Run them in order.

| Gate | Method | Pass Criteria |
|------|--------|--------------|
| 1. Structural integrity | BS Check = 0, CF Check = 0, NI linkage, RE roll-forward (see Phase 1 / Phase 3 integrity checks) | Zero errors across all periods |
| 2. Assumption review | Review all blue-text cells: source comments present, no stale assumptions (>90 days), no "Aggressive" tags without documented rationale | Every assumption sourced and current |
| 3. Comps validation | Re-check Comps tab: all SPG() formulas resolving, peer set still valid, summary stats current | All variances within +/-10% of consensus or documented explanation |
| 4. Thesis consistency | Review Key Decisions Log on Task Tracker: assumptions align with stated thesis, no internal contradictions | No contradictions between model assumptions and investment narrative |
| 5. Model tab consistency | Verify Model tab summary numbers match underlying IS/BS/CF/build tabs: summary revenue = IS revenue, summary EPS = IS EPS, ROIC inputs match BS/IS, FCF yields match CF/market data, key drivers match build tab assumptions (see Phase 5) | Zero mismatches between Model tab and source tabs |
| 6. Returns sanity | Re-check Returns tab: XIRR/MOIC internally consistent, Returns tab pulls from Model tab correctly, sensitivity table reasonable, entry price current (see Phase 6) | IRR/MOIC plausible, sensitivity grid spans realistic range |

### Gate Failure Protocol
- **Gate 1 failure**: Fix the structural error before anything else. Use the BS→CF mapping from Phase 1 to diagnose. No exceptions.
- **Gate 2-4 failure**: Flag the specific issue, propose a fix, and get user confirmation before proceeding.
- **Gate 5 failure**: Model tab formula is broken or stale — refresh per Phase 5.
- **Gate 6 failure**: Usually indicates an assumption issue upstream — trace back to the offending input.

---

## Data Pull Tabs

### Data Pull (Live)
- Contains all SPG() formulas pulling live data from CIQ
- Organized by company (subject + each peer) with clear section headers
- Red font for all SPG() cells per firm-formatting
- Central "As-Of Date" cell (`$B$2`) that all formulas reference
- **No model tab should reference the Data Pull (live) tab directly** — live formulas are volatile and can break when CIQ refreshes or loses connectivity

### Data Pull (Values)
- **Paste-special-as-values snapshot** of the live Data Pull tab
- This is the stable layer between live CIQ data and the model. All model tabs reference Data Pull (Values), not the live formulas tab.
- Date-stamped in a header cell: "Values as of MM/DD/YYYY"
- **Update process**: refresh Data Pull (live), review for errors, then paste-as-values into Data Pull (Values) and update the date stamp
- This architecture ensures the model never breaks due to CIQ connectivity issues or data refreshes

### Linking Hierarchy
```
SPG() formulas → Data Pull (live) → paste-as-values → Data Pull (Values) → Model tabs reference here
BAMSEC / Tegus source tabs → Model tabs link directly (for historicals)
```
