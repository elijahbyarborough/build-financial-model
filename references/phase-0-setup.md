# Phase 0 — Setup

**Owner:** `build-model` (this phase is executed directly).
**Next phase:** Phase 1 (Historical Statements) → load `references/phase-1-historical-statements.md`

---

## First Contact Protocol

**If resuming (Task Tracker exists):** Read the Task Tracker → identify current phase → load that phase's reference file per the Phase Router. Skip everything below.

**If new build:**
1. **HARD REQUIREMENT:** Load `references/firm-formatting.md` and internalize the **Excel Formatting Standards** section BEFORE creating any tab. Specifically, you must apply on every tab you create: Standard Tab Header (rows 1-6), Year Label Convention, Visual Hierarchy tiers, Display Settings (gridlines off, no freeze panes), Column Structure (uniform widths, no blank columns), Tab Colors, and Spacer Row rules. Do NOT defer formatting to Phase 7 — tabs must be correctly formatted from the moment they are created.
2. Read the workbook: scan all tabs, list what exists.
3. Identify starting scenario (see Starting Scenarios below).
4. Run sector discovery (see Sector Discovery Framework below).
5. Confirm with user: ticker/exchange, FYE month, focus areas, tab tier (Tier 1 default; Tier 2 if segments with >500bps margin differential).
6. Create Task Tracker (see Task Tracker section below).
7. Create ALL tabs with correct setup (see Tab Creation Requirements below).

---

## Tab Creation Requirements

**Every tab must be fully set up before Phase 0 is complete.** All formatting rules come from `references/firm-formatting.md` (loaded in step 1 above). This means:

- **Correct tab names** per the Tab Set table below
- **Correct tab colors** per the Tab Set table below
- **Correct tab order** (left to right per the Tab Set table)
- **Standard Tab Header (rows 1-6)** on every tab except Returns and Task Tracker: navy title row, italic unit label, year row with medium bottom border, FYE dates, blank spacer, then first section header
- **Year columns**: historical years (`FY YYYYA`) + 7 projection years (`FY YYYYE`), text format (`@`), locally hardcoded on every tab (never cross-sheet references)
- **Column structure**: Column A = labels, then data columns immediately adjacent. **No blank columns anywhere.** Uniform data column widths (~13 chars / ~95px).
- **No freeze panes** on any tab. Ever.
- **Gridlines off** on every tab
- **No merged cells** in data area
- **Display settings applied** before moving to Phase 1

**Verify every tab is correctly set up before proceeding.** If any tab has wrong naming, missing year columns, incorrect colors, freeze panes, or structural issues, fix it now. Do not carry setup debt into Phase 1.

---

## Tab Set — Tier 1 (Default)

Tab order in the workbook (left to right):

| Tab | Purpose | Color |
|-----|---------|-------|
| Task Tracker | Session state, task list | Amber (#D4963A) |
| Output | Single-page investment summary. **Blank until Phase 9.** | Navy (#1C3553) |
| Returns | XIRR, IRR decomposition | Navy (#1C3553) |
| Model | Summary IS, key drivers, KPIs, ROIC/ROTIC. **Blank until Phase 5.** | Navy (#1C3553) |
| IS | Detailed Income Statement (Model View + Reported View) | Sage green (#6B9E6F) |
| BS | Detailed Balance Sheet (Model View + Reported View) | Sage green (#6B9E6F) |
| CF | Detailed Cash Flow Statement (Model View + Reported View) | Sage green (#6B9E6F) |
| Revenue Build | Driver-based revenue projections | Steel blue (#5B8FA8) |
| Costs Build | OpEx detail, margin analysis | Steel blue (#5B8FA8) |
| PP&E Build | Capex, depreciation schedule | Steel blue (#5B8FA8) |
| Debt Build | Tranche-by-tranche debt, interest | Steel blue (#5B8FA8) |
| Working Capital Build | All operating BS items with drivers | Steel blue (#5B8FA8) |
| Capital Allocation Build | Cash waterfall, buybacks, dividends | Steel blue (#5B8FA8) |
| Tax Schedule | Effective tax rate, DTAs/DTLs | Steel blue (#5B8FA8) |
| Historical Data | Quarterly/annual actuals capture | Dark teal (#436E71) |
| Consensus | Model vs. street comparison. **Blank until Phase 10.** | Navy (#1C3553) |
| Data Pull | Live SPG() formulas | Gray (#7C7F88) |
| Data Pull (Values) | Pasted-as-values snapshot | Gray (#7C7F88) |

Tabs can be added or removed based on company/sector. This is the baseline — not a straitjacket.

### Tier 2 (Segment Reporting)

**Decision rule:** If the company has 2+ reportable segments with >500bps margin differential or fundamentally different revenue models, use Tier 2.

Same as Tier 1 except Revenue Build and Costs Build are replaced with per-segment tabs plus a Consolidation tab:

| Replaces | With | Purpose |
|----------|------|---------|
| Revenue Build + Costs Build | **[Segment Name] Build** (one per segment) | Revenue AND cost drivers combined per segment |
| (new) | **Consolidation** | Aggregates segments, corporate/unallocated costs, inter-segment eliminations |

**Segment tab naming:** `[Segment Name] Build` (e.g., `Azure Build`, `M365 Build`).

---

## Starting Scenarios

Every model build starts from one of three scenarios. Identify which one applies before doing anything else.

### Scenario 1: Fresh Build from BAMSEC + Tegus

**Most common.** You receive:
- **BAMSEC Excel export**: raw financial tables (IS, BS, CF) pulled from SEC filings. This is the primary source of truth for reported historicals.
- **Tegus model**: Excel file with detailed, accurate historicals and often richer line-item granularity than BAMSEC. **Ignore Tegus projections entirely** — they are underdeveloped and unreliable. Use Tegus for historicals and operating metrics only.

**Workflow:**
1. Keep BAMSEC and Tegus sheets in the workbook as source tabs (do not delete or hide them — they serve as the audit trail)
2. Map BAMSEC line items to the standard IS/BS/CF structure on the detailed statement tabs
3. Auto-detect segment breakouts from BAMSEC and/or Tegus for the Revenue Build
4. Pull historical KPIs and operating metrics from Tegus where available
5. Flag any line items that don't map cleanly to the standard structure and ask the user before proceeding
6. All model cells that use historical data should **link directly to the source BAMSEC/Tegus tabs** — never copy-paste values. This preserves the audit trail: anyone can trace a number back to the filing.

### Scenario 2: Fresh Build from BAMSEC + Tegus + GS Model

Same as Scenario 1, plus:
- **GS (Goldman Sachs) model**: varies in quality and completeness. Treat case by case.
  - If the GS model has strong historicals that complement Tegus, use both for cross-referencing
  - If the GS model has detailed projections, use them as a **reference point / sanity check** — not as the basis for your projections
  - If the GS model has useful segment-level detail that Tegus lacks, pull it in
  - Never blindly adopt GS assumptions. The user will tell you which elements to use.
- Keep the GS sheet as a source tab in the workbook, same as BAMSEC and Tegus

### Scenario 3: Rebuild from an Existing Model

You receive an existing model (yours or someone else's) and need to bring it up to standard.

**Default approach: rebuild to the standard tab set, pulling data from the existing model.**
- Do NOT try to reformat the existing model in place. Create fresh tabs using the standard structure.
- Link from new tabs back to the existing model's tabs for historical data, then verify the numbers against filings
- Once the new model is built and verified, the old tabs can be moved to an "Archive" group or deleted (user's call)
- If the existing model is already close to the standard structure, the user may override and say "just layer on top" — but the default is always a clean rebuild

---

## Source Exhaustiveness Rule

When multiple sources of historical financial data exist, **always default to the most exhaustive source** — the one with the most complete, fully-fledged GAAP-format financials with the most line-item granularity.

### Raw Source Selection (which source to ingest from)

1. **Tegus model** — typically the most complete: full GAAP line items, segment detail, operating metrics, quarterly granularity. Use for historicals and operating metrics. **Ignore Tegus projections entirely.**
2. **Broker model (GS, MS, etc.)** — often has strong historicals with detailed line items. Use for historicals; treat projections as reference only.
3. **BAMSEC export** — raw SEC filing data. Complete but may lack the organizing structure of a Tegus/broker model.
4. **User-built model** — may be simplified or incomplete. Never prefer a user-built model's historicals over a more complete external source.

### Linking Hierarchy (what build tabs and statement tabs reference)

Once data is in the model, build tabs and IS/BS/CF tabs need to link to it via formulas:

1. **Historical Data tab (preferred when it exists)** — if the Historical Data tab has curated raw sources into a clean capture layer, this is the preferred intermediary. Build tabs and statement tabs link here via `='Historical Data'!cell`. The Historical Data tab itself is the one place where hardcoded actuals from filings are acceptable.
2. **Raw source tabs (when no Historical Data tab exists)** — link directly to BAMSEC/Tegus/broker source tabs via `='Tegus'!cell` or `='BAMSEC'!cell`.

The data flow is: **Raw sources → Historical Data tab (capture layer) → Build tabs / IS / BS / CF link via formulas**. When no Historical Data tab exists, build tabs link directly to raw source tabs.

### How to Apply

- **At the start of every model build**, scan ALL available source tabs and identify which has the most complete IS, BS, and CF historicals.
- The most exhaustive source becomes the **default source for the Reported View** on IS/BS/CF tabs — its line items, ordering, and granularity define the GAAP presentation.
- The Model View pulls from build tabs as always — the source hierarchy affects the Reported View and the historical data foundation.
- If the user has an existing model AND a Tegus/broker model, the Tegus/broker model's historicals take priority unless the user explicitly overrides.
- When in doubt, ask: "I see both [source A] and [source B]. [Source A] has more complete GAAP financials — should I use that as the foundation for historicals?"

---

## Source Tab Conventions

### Naming
| Source | Tab Name |
|--------|----------|
| BAMSEC financial export | `BAMSEC` or `BAMSEC - [Filing Type]` (e.g., `BAMSEC - 10-K`) |
| Tegus model | `Tegus` or `Tegus - [Analyst Name]` |
| GS model | `GS` or `GS Model` |
| Existing model being rebuilt | `Old Model` or original tab names with `(old)` suffix |

### Tab Color
All source/reference tabs: Gray (#7C7F88), same as Data Pull.

### Rules
- **Never delete source tabs** during the build process. They are the audit trail.
- **Never modify source tabs.** If you need to clean or reshape source data, create a staging tab.
- All links from model tabs to source tabs should use direct cell references (e.g., `='BAMSEC'!C15`), not VLOOKUP or INDEX/MATCH, so the audit trail is one click away.

---

## Data Ingestion Steps (Automatic)

When the user provides source files, automatically:

1. **Map BAMSEC line items to the standard IS/BS/CF structure**
   - Revenue, COGS, SG&A, R&D, D&A, Interest, Tax, etc.
   - Subtotals: Gross Profit, Operating Income, EBITDA, EBT, Net Income
   - BS: Current Assets, PP&E, Intangibles, Goodwill, Current Liabilities, LT Debt, Equity
   - CF: CFO, CFI, CFF, beginning/ending cash

2. **Auto-detect segment breakouts for the Revenue Build**
   - Look for segment revenue tables in BAMSEC
   - Look for product/geography/customer breakouts in Tegus
   - Propose a segment structure for the Revenue Build and confirm with the user

3. **Pull historical KPIs / operating metrics from Tegus if available**
   - Margins, growth rates, unit economics, subscriber counts, fleet metrics, etc.
   - Place in the KPI section on the Model tab

4. **Flag line items that don't map cleanly**
   - Unusual items, one-time charges, reclassifications, items that exist in Tegus but not BAMSEC (or vice versa)
   - Present a list to the user and ask how to handle each one before proceeding

---

### Lease Detection

When scanning source financials, identify lease presence and classification:

1. **Scan BS** for: "Operating Lease ROU", "Right-of-use", "Finance Lease", "Capital Lease", "ROU Asset"
2. **Scan IS** for: "Operating lease cost", "Finance lease depreciation", "Finance lease interest", "Lease expense"
3. **Scan CF** for: "Finance lease payments", "Operating lease payments", "ROU asset amortization"
4. **Determine FL_IN_PPE**: Check whether the company's reported Net PP&E includes or excludes finance lease ROU assets. Inspect the PP&E footnote or BS detail. If finance lease assets are inside the PP&E roll-forward, `FL_IN_PPE = Y`. If reported as a separate BS line, `FL_IN_PPE = N`.
5. **Assess materiality**: If total lease ROU assets (operating + finance) are > 5% of total assets, `LEASE_MATERIALITY = HIGH`. Otherwise `LOW`.

**Add to Task Tracker Model State Block**:

HAS_OPERATING_LEASES: [Y/N]
HAS_FINANCE_LEASES: [Y/N]
FL_IN_PPE: [Y/N]
LEASE_MATERIALITY: [HIGH/LOW]

These flags control whether the Debt Build includes lease schedules and whether the PP&E Build needs FL-related adjustments.

---

## Data Pull Tabs

### Data Pull (Live SPG Formulas)
- Contains all `SPG()` formulas pulling live data from S&P Capital IQ
- These formulas refresh automatically when CIQ updates, which can cause model instability if other tabs reference them directly
- **No model tab should reference the Data Pull tab directly** — the model references Data Pull (Values) instead

### Data Pull (Values)
- **Paste-special-as-values snapshot** of the live Data Pull tab
- Purpose: creates a stable, non-volatile layer between live CIQ data and the model's calculations. The model references this tab, not the live formulas tab.
- Date-stamped in a header cell: "Values as of MM/DD/YYYY"
- **Update process**: refresh Data Pull (live), review for errors, then paste-as-values into Data Pull (Values) and update the date stamp

### Full Linking Hierarchy
```
SPG() formulas → Data Pull (live) → paste-as-values → Data Pull (Values) → Model tabs reference here
BAMSEC / Tegus / Broker source tabs → Historical Data tab (if built) → Build tabs / IS / BS / CF link via formulas
BAMSEC / Tegus / Broker source tabs → Build tabs / IS / BS / CF link directly (if no Historical Data tab)
```

**No Hardcoded Historicals**: Every historical value on build tabs and statement tabs must be a formula linking to a source tab (or to the Historical Data tab as an intermediary). Never copy-paste values from source tabs — always use cell references so the audit trail is one click away. The only tab where hardcoded historical values are acceptable is the Historical Data tab itself (it IS the capture layer).

---

## Sector Discovery Framework

### Revenue Model Identification

| Revenue Type | Key Signals | Revenue Cascade |
|-------------|------------|-----------------|
| Unit Economics | Volume x price disclosed | Units Sold x ASP = Revenue |
| Subscription/Recurring | ARR/MRR, retention, net adds | Beginning Subs + Net Adds x ARPU x 12 |
| Transaction-Based | GMV/TPV, take rate | GMV x Take Rate |
| Asset-Based | Fleet size, utilization, yield | Fleet x Utilization x Rate x Period |
| Project-Based | Backlog, book-to-bill | Beginning Backlog + New Orders - Revenue = Ending |
| Fee-Based | AUM, fee rates | AUM x Fee Rate + Performance Fees |
| Ad-Supported | MAU/DAU, impressions, CPM | Impressions x CPM / 1000 |

### Segment Structure Detection
1. Count segments. If >1 with >500bps margin differential, propose segment-level builds.
2. Different business models → separate revenue cascades.
3. Different capital structures → flag for SOTP.
4. Document segment structure in Task Tracker Notes.

### Non-Standard BS Patterns

| Pattern | Industries | Adaptation |
|---------|-----------|------------|
| Negative working capital | SaaS, subscription | Deferred revenue is the key driver |
| Heavy inventory cycles | Retail, distribution | Model quarterly if material |
| Contract assets/liabilities | Construction, defense | Model backlog conversion |
| Financial assets | Banks, insurance | Replace WC with financial asset/liability modeling |
| Rental fleet | Equipment rental | Fleet = PP&E equivalent |

### Discovery Outputs (document in Task Tracker Notes)
1. Revenue model type and cascade
2. Segment structure
3. Non-standard BS patterns
4. Exit multiple type (FCF > P/E > EBIT > EBITDA) with rationale
5. Sector-specific KPIs for Model tab

---

## Assumptions Organization

**All assumptions live on build tabs.** The Model tab and IS/BS/CF are output layers.

- **Revenue Build**: pricing, volume, growth, mix
- **Costs Build**: margin targets, OpEx growth
- **PP&E Build**: capex rates, useful lives, depreciation
- **Debt Build**: tranche terms, interest rates, SOFR
- **Working Capital Build**: days metrics, % of revenue, or other drivers
- **Capital Allocation Build**: buybacks, dividend policy, acquisition spend
- **Returns tab**: entry price, exit multiple, discount rate

**Every assumption cell** gets: yellow bg (#FFFF00) + blue text (#0000FF) + source comment. No exceptions.

---

## Task Tracker

**Every model build must maintain a task tracker.** First tab, leftmost. Amber/gold (#D4963A).

### Model State Block (top of tab)

| Field | Content |
|-------|---------|
| Company | Full company name |
| Ticker | Exchange:Ticker |
| FYE | Fiscal year-end month |
| Historical Years | Range |
| Projection Years | Range |
| Tabs Created | Comma-separated list |
| Tabs Pending | Comma-separated list |
| BS Check Status | PASS / FAIL |
| CF Check Status | PASS / FAIL |
| Last Skill Run | Skill name + date |
| Next Skill | Recommended next |

### Task Table

| Column | Content |
|--------|---------|
| Phase | 0-11 |
| Task | ID + name |
| Description | What needs doing |
| Status | DONE / IN PROGRESS / NOT STARTED / BLOCKED |
| Skill | Owner |
| Dependencies | Required task IDs |
| Notes | Decisions, methodology |

### Blockers Section
Blocker ID, Description, Blocking Tasks, Owner, Status, Resolution.

### Rules
- Update after every work session
- Read tracker first at start of every session
- Log significant decisions in the Notes column
- Respect dependencies
- Uses firm-formatting styling

### Phased Execution (Universal Rule)
**No skill attempts to complete its work in one shot.** Read tracker → plan phases → execute one at a time → update tracker → add tasks for other skills to tracker.
