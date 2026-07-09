# Phase 0 — Setup

---

## First Contact Protocol

**If resuming (Task Tracker exists):** Read the Task Tracker → identify current phase → load that phase's skill per the Phase Router. Skip everything below.

**If new build:**
1. **HARD REQUIREMENT:** The `firm-formatting` skill must be loaded and internalized BEFORE creating any tab. Specifically, you must apply on every tab you create: Standard Tab Header (rows 1-6), Year Label Convention, Visual Hierarchy tiers, Display Settings (gridlines off, no freeze panes), Column Structure (uniform widths, no blank columns), Tab Colors, and Spacer Row rules. Do NOT defer formatting to Phase 8 — tabs must be correctly formatted from the moment they are created.
2. Read the workbook: scan all tabs, list what exists.
3. Identify starting scenario (see Starting Scenarios below).
4. Run sector discovery (see Sector Discovery Framework below).
5. Confirm with user: ticker/exchange, FYE month, focus areas, segment structure for the Profit Build.
6. Create Task Tracker (see Task Tracker section below).
7. Create ALL tabs with correct setup (see Tab Creation Requirements below).

---

## Tab Creation Requirements

**Every tab must be fully set up before Phase 0 is complete.** All formatting rules come from the `firm-formatting` skill (loaded before this phase). This means:

- **Correct tab names** per the Tab Set table below
- **Correct tab colors** per the Tab Set table below
- **Correct tab order** (left to right per the Tab Set table)
- **Standard Tab Header (rows 1-6)** on every tab except Returns, Task Tracker, and KPI Tracker (the `kpi-tracker` skill owns that tab's header and quarterly column layout): navy title row, italic unit label, year row with medium bottom border, FYE dates, blank spacer, then first section header
- **Year columns**: historical years (`FY YYYYA`) + 7 projection years (`FY YYYYE`), text format (`@`), locally hardcoded on every tab (never cross-sheet references). The **Quarterly Historicals** tab uses fiscal-quarter labels (`FQN YYYYA`) per the Year Label Convention instead of FY labels — at least 16 historical quarters.
- **Column structure**: Column A = labels, then data columns immediately adjacent. **No blank columns anywhere.** Uniform data column widths (~13 chars / ~95px).
- **No freeze panes** on any tab. Ever.
- **Gridlines off** on every tab
- **No merged cells** in data area
- **Display settings applied** before moving to Phase 1

**Verify every tab is correctly set up before proceeding.** If any tab has wrong naming, missing year columns, incorrect colors, freeze panes, or structural issues, fix it now. Do not carry setup debt into Phase 1.

---

## Tab Set

Tab order in the workbook (left to right):

| Tab | Purpose | Color |
|-----|---------|-------|
| Task Tracker | Session state, task list | Amber (#D4963A) |
| Output | Single-page investment summary. **Blank until Phase 10.** | Navy (#1C3553) |
| Consensus | Model vs. street comparison. **Blank until Phase 11.** | Navy (#1C3553) |
| Returns | XIRR, IRR decomposition. **Blank until Phase 7.** | Navy (#1C3553) |
| Model Tab | Summary IS, key drivers, KPIs, ROIC/ROTIC. **Blank until Phase 6.** | Navy (#1C3553) |
| KPI Tracker | Quarterly KPI dashboard, 4-year lookback + forward. **Blank until Phase 2.** | Navy (#1C3553) |
| IS | Income Statement — Model View (historicals link to Annual Historicals) | Sage green (#6B9E6F) |
| BS | Balance Sheet — Model View | Sage green (#6B9E6F) |
| CFS | Cash Flow Statement — Model View | Sage green (#6B9E6F) |
| Profit Build | Segment revenue & cost drivers through EBIT, plus tax | Steel blue (#5B8FA8) |
| BS & CFS Build | PP&E & capex, working capital, debt & cash, lease schedules | Steel blue (#5B8FA8) |
| Capital Allocation Build | Cash waterfall, buybacks, dividends | Steel blue (#5B8FA8) |
| Annual Historicals | As-reported annual data capture layer | Dark teal (#436E71) |
| Quarterly Historicals | As-reported quarterly data capture layer (≥16 quarters) | Dark teal (#436E71) |
| Data Pull | Live SPG() formulas | Gray (#7C7F88) |
| Data Pull (Values) | Pasted-as-values snapshot | Gray (#7C7F88) |

Tabs can be added or removed based on company/sector. This is the baseline — not a straitjacket.

### Segments Live Inside the Profit Build

**Segments never get their own tabs.** When the company has multiple reportable segments, each segment becomes a **Tier 2 section within the Profit Build** (volume/price drivers → segment revenue → segment costs → segment profit per section), followed by a consolidated P&L bridge section. Single-segment companies use consolidated Revenue and Costs sections instead. See the Phase 3 (Drivers) skill for the full Profit Build structure. Document the segment structure in the Task Tracker Notes during Sector Discovery.

---

## Starting Scenarios

Every model build starts from one of three scenarios. Identify which one applies before doing anything else.

### Scenario 1: Fresh Build from BAMSEC + Tegus

**Most common.** You receive:
- **BAMSEC Excel export**: raw financial tables (IS, BS, CFS) pulled from SEC filings. This is the primary source of truth for reported historicals.
- **Tegus model**: Excel file with detailed, accurate historicals and often richer line-item granularity than BAMSEC. **Ignore Tegus projections entirely** — they are underdeveloped and unreliable. Use Tegus for historicals and operating metrics only.

**Workflow:**
1. Keep BAMSEC and Tegus sheets in the workbook as source tabs during the build (working material for capture)
2. In Phase 1, capture reported data from these sources onto the **Annual Historicals** tab (and in Phase 2, the **Quarterly Historicals** tab) — hardcoded entries with source comments, per the capture rules below
3. Auto-detect segment breakouts from BAMSEC and/or Tegus for the Profit Build's segment sections
4. Identify historical KPIs and operating metrics from Tegus for the Operational/KPI Data section of the Historicals tabs
5. Flag any line items that don't map cleanly to the capture hierarchy and ask the user before proceeding

### Scenario 2: Fresh Build from BAMSEC + Tegus + GS Model

Same as Scenario 1, plus:
- **GS (Goldman Sachs) model**: varies in quality and completeness. Treat case by case.
  - If the GS model has strong historicals that complement Tegus, use both for cross-referencing during capture
  - If the GS model has detailed projections, use them as a **reference point / sanity check** — not as the basis for your projections
  - If the GS model has useful segment-level detail that Tegus lacks, capture it
  - Never blindly adopt GS assumptions. The user will tell you which elements to use.
- Keep the GS sheet as a source tab in the workbook, same as BAMSEC and Tegus

### Scenario 3: Rebuild from an Existing Model

You receive an existing model (yours or someone else's) and need to bring it up to standard.

**Default approach: rebuild to the standard tab set, capturing data from the existing model.**
- Do NOT try to reformat the existing model in place. Create fresh tabs using the standard structure.
- Capture the old model's reported actuals onto the Annual/Quarterly Historicals tabs (verified against filings), then build the new model from there
- Once the new model is built and verified, the old tabs can be moved to an "Archive" group or deleted (user's call)
- If the existing model is already close to the standard structure, the user may override and say "just layer on top" — but the default is always a clean rebuild

---

## Source Exhaustiveness Rule

When multiple sources of historical financial data exist, **always default to the most exhaustive source** — the one with the most complete, fully-fledged GAAP-format financials with the most line-item granularity.

### Raw Source Selection (which source to capture from)

1. **Tegus model** — typically the most complete: full GAAP line items, segment detail, operating metrics, quarterly granularity. Use for historicals and operating metrics. **Ignore Tegus projections entirely.**
2. **Broker model (GS, MS, etc.)** — often has strong historicals with detailed line items. Use for historicals; treat projections as reference only.
3. **BAMSEC export** — raw SEC filing data. Complete but may lack the organizing structure of a Tegus/broker model.
4. **User-built model** — may be simplified or incomplete. Never prefer a user-built model's historicals over a more complete external source.

### The Capture Layer (what the model references)

Reported data flows through exactly one intermediary — the Historicals tabs:

```
Raw sources (BAMSEC / Tegus / Broker / filings)
    → CAPTURE: hardcoded entry + source comment on Annual Historicals / Quarterly Historicals
        → IS / BS / CFS historicals, build tabs, and KPI Tracker link here via formulas
```

- The **Annual Historicals** and **Quarterly Historicals** tabs are the **only place in the model where hardcoded historical values are allowed**. Every captured cell: blue text (#0000FF) + source comment (`Source: [Document Type], [Date], [Specific Reference]`).
- **No model tab ever links to a raw source tab.** The audit trail lives in the source comment on each captured cell — one click away, and the workbook stays portable when source tabs are archived.
- Capture is governed by the **capture-everything principle** and the canonical section hierarchy — see `meth-historicals.md` in the Phase 1 skill.

### How to Apply

- **At the start of every model build**, scan ALL available source tabs and identify which has the most complete IS, BS, and CFS historicals.
- The most exhaustive source becomes the **default capture source for the Annual Historicals tab** — its line items, ordering, and granularity define the as-reported presentation captured there.
- If the user has an existing model AND a Tegus/broker model, the Tegus/broker model's historicals take priority unless the user explicitly overrides.
- When in doubt, ask: "I see both [source A] and [source B]. [Source A] has more complete GAAP financials — should I use that as the capture source for historicals?"

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
- **Never delete source tabs** during the build process. They remain working material and a secondary audit trail until the build is complete (Phase 12 confirms they are archivable at the user's discretion).
- **Never modify source tabs.** If you need to clean or reshape source data, create a staging tab.
- **Never link model tabs to source tabs.** Data leaves a source tab only by being captured onto the Historicals tabs with a source comment.

---

## Data Ingestion Steps (Automatic)

When the user provides source files, automatically:

1. **Map source line items to the Historicals capture hierarchy** (see `meth-historicals.md`, Phase 1 skill)
   - IS/BS/CFS faces: every reported line, full granularity
   - Supplemental sections: segments, geography, operational KPIs, share counts, guidance, debt detail, lease detail, non-GAAP reconciliations

2. **Auto-detect segment breakouts for the Profit Build**
   - Look for segment revenue tables in BAMSEC
   - Look for product/geography/customer breakouts in Tegus
   - Propose a segment-section structure for the Profit Build and confirm with the user

3. **Identify historical KPIs / operating metrics from Tegus if available**
   - Margins, growth rates, unit economics, subscriber counts, fleet metrics, etc.
   - These seed the Operational/KPI Data section of the Historicals tabs and the KPI Tracker's KPI list

4. **Flag line items that don't map cleanly**
   - Unusual items, one-time charges, reclassifications, items that exist in Tegus but not BAMSEC (or vice versa)
   - Present a list to the user and ask how to handle each one before proceeding

---

### Lease Detection

When scanning source financials, identify lease presence and classification:

1. **Scan BS** for: "Operating Lease ROU", "Right-of-use", "Finance Lease", "Capital Lease", "ROU Asset"
2. **Scan IS** for: "Operating lease cost", "Finance lease depreciation", "Finance lease interest", "Lease expense"
3. **Scan CFS** for: "Finance lease payments", "Operating lease payments", "ROU asset amortization"
4. **Build the Lease BS Map**: Read the lease accounting policy footnote for explicit disclosure of
   where lease items are presented on the BS (e.g., "Finance leases are included in property and
   equipment, net, accrued expenses and other current liabilities, and other liabilities"). Cross-
   reference against the face of the BS (look for dedicated lease lines) and the PP&E footnote
   (look for FL ROU in the gross detail). Populate every field of the Lease BS Map on the Task
   Tracker. Then derive FL_IN_PPE from the map (Y if FL_ROU_ASSET_NC maps to a PP&E line).
5. **Assess materiality**: If total lease ROU assets (operating + finance) are > 5% of total assets, `LEASE_MATERIALITY = HIGH`. Otherwise `LOW`.

**Add to Task Tracker Model State Block**:

HAS_OPERATING_LEASES: [Y/N]
HAS_FINANCE_LEASES: [Y/N]
LEASE_MATERIALITY: [HIGH/LOW]
LEASE BS MAP:
  OL_ROU_ASSET_NC:     [line name or "own line"]
  OL_ROU_ASSET_C:      [line name or "N/A"]
  OL_LIAB_C:           [line name or "own line"]
  OL_LIAB_NC:          [line name or "own line"]
  FL_ROU_ASSET_NC:     [line name or "own line"]
  FL_ROU_ASSET_C:      [line name or "N/A"]
  FL_LIAB_C:           [line name or "own line"]
  FL_LIAB_NC:          [line name or "own line"]
FL_IN_PPE: [Y/N] (derived — Y if FL_ROU_ASSET_NC is a PP&E line)

These flags and the Lease BS Map control whether the BS & CFS Build includes lease schedule sections, how its Working Capital section disaggregates lease components, and whether its PP&E & Capex section needs FL-related adjustments. The Lease Detail section of the Historicals tabs captures the footnote data these schedules are built from.

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
Raw sources (BAMSEC/Tegus/Broker) → CAPTURE → Annual/Quarterly Historicals → IS/BS/CFS, build tabs, KPI Tracker link via formulas
```

**Single Hardcode Layer**: Every historical value on build tabs and statement tabs must be a formula linking to the Annual Historicals tab (or Quarterly Historicals for quarterly-sourced items). Hardcoded historical values are allowed ONLY on the two Historicals tabs — they ARE the capture layer, and every captured cell carries blue text + a source comment.

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
1. Count segments. If >1 with >500bps margin differential or fundamentally different revenue models, propose one Profit Build section per segment.
2. Different business models → separate revenue cascades (one per segment section).
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

### Operational / KPI Taxonomy Selection

Sector discovery also determines **which operational metrics the company reports and which matter** — this seeds two downstream artifacts:

1. **Historicals Section 4 (Operational / KPI Data)**: the list of company-reported operating metrics to capture every period (volumes, ASP, subscribers, ARR/NRR, stores, same-store sales, backlog/RPO, bookings, DAU/MAU, AUM & flows, NIM, combined ratio, RevPAR, headcount, utilization — whatever the company actually discloses)
2. **KPI Tracker KPI list (Phase 2)**: the curated subset that drives the quarterly tracker, selected per the `kpi-tracker` skill's lenses (what management leads with, the bull/bear pivot, the model's driver structure, industry archetype)

Scan the most recent earnings release and investor deck to enumerate what the company discloses. Capture-everything applies: if the company reports it every quarter, it belongs in Section 4.

### Discovery Outputs (document in Task Tracker Notes)
1. Revenue model type and cascade
2. Segment structure (Profit Build sections)
3. Non-standard BS patterns
4. Exit multiple type (FCF > P/E > EBIT > EBITDA) with rationale
5. Operational/KPI taxonomy: metrics list for Historicals Section 4 and KPI Tracker candidates

---

## Assumptions Organization

**All assumptions live on build tabs.** The Model tab and IS/BS/CFS are output layers.

- **Profit Build**: pricing, volume, growth, mix, margin targets, OpEx growth, effective tax rate, NOL utilization
- **BS & CFS Build**: capex rates, useful lives, depreciation rates, debt tranche terms, interest rates, SOFR, target cash balance, WC days metrics / % of revenue drivers, lease drivers
- **Capital Allocation Build**: buybacks, dividend policy, acquisition spend
- **Returns tab**: entry price, exit multiple

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
| Historical Quarters | Range (Quarterly Historicals coverage, ≥16) |
| Projection Years | Range |
| Tabs Created | Comma-separated list |
| Tabs Pending | Comma-separated list |
| BS Check Status | PASS / FAIL |
| CF Check Status | PASS / FAIL |
| SEGMENT_PRESENTATION | Current segment presentation version (e.g., "FY2024 presentation") |
| Last Skill Run | Skill name + date |
| Next Skill | Recommended next |

### Task Table

| Column | Content |
|--------|---------|
| Phase | 0-12 |
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

---

## Tab Completion Verification

Before reporting Phase 0 complete, read and paste the following for EVERY tab created:

```
TAB VERIFICATION:
[Tab Name]:
  tab exists: [yes/no]
  tab color: [hex] -- expected: [hex from Tab Set table]
  tab position: [position] -- expected: [position from Tab Set table]
  Standard Tab Header (rows 1-6): [present/missing/N-A for Returns, Task Tracker, KPI Tracker]
  Year columns: [count] historical + [count] projection (Quarterly Historicals: [count] FQ columns)
  Column widths uniform: [yes/no]
  freezePanes: [null -- if not null, STOP and fix]
  gridlines: [off -- if on, STOP and fix]
```

If you do not paste this output, the user cannot verify compliance. No output = not verified.

---

## Definition of Done (Phase 0)

A phase is complete if and only if ALL of the following are true. Report completion by reading these values back to the user -- not by summarizing in prose.

1. **Task Tracker**: Every subtask row for Phase 0 shows Status = "COMPLETE". Cite the actual cell addresses you checked (e.g., "D5:D12 all = COMPLETE").
2. **Task Tracker Model State Block**: All fields populated (Company, Ticker, FYE, Historical Years, Historical Quarters, Projection Years, Tabs Created, BS/CF Check Status, SEGMENT_PRESENTATION, Last Skill Run, Next Skill).
3. **Lease flags populated**: HAS_OPERATING_LEASES, HAS_FINANCE_LEASES, FL_IN_PPE, LEASE_MATERIALITY, and Lease BS Map all filled.
4. **Sector discovery documented**: revenue model, segment structure, exit multiple, and the Operational/KPI taxonomy list all in Task Tracker Notes.
5. **All tabs created** per the Tab Set table with correct names, colors, order, and Standard Tab Headers.
6. **Tab Completion Verification output** pasted above with all tabs passing.
7. **Task Tracker Model State Block**: "Last Skill Run" = "build-model-phase-0" + today's date, "Next Skill" = "build-model-phase-1".

If you write "Phase 0 complete" in chat before reading and reporting these values, you have made an error. Re-verify and correct.
