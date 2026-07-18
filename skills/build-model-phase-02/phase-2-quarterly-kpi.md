# Phase 2 -- Quarterly Historicals & KPI Tracker

## Purpose

Two deliverables, in order:

1. **Quarterly Historicals tab** -- capture everything the company reports each quarter,
   going back at least 16 quarters (4 full fiscal years) plus every quarter of the current
   partial year. Together with Annual Historicals (Phase 1), this tab is one of exactly two
   places in the model where hardcoded historical data is allowed. It is the surface that
   gets updated after every earnings release and filing.
2. **KPI Tracker tab** -- the quarterly KPI scorecard built FROM Quarterly Historicals,
   per the `kpi-tracker` skill's construction spec.

Doing this BEFORE Phase 3 (Drivers) is deliberate: 16+ quarters of KPI history in front of
you materially improves driver assumptions.

## Prerequisites

- Phase 1 complete: Annual Historicals populated, IS/BS/CFS Model View historicals built,
  BS Check = 0 and CF Check = 0 every historical period, statement recon checks = 0.
- `kpi-tracker` skill loaded (required for Step 2).
- Source documents available: 10-Qs for the capture window, Q4/FY earnings releases (Q4 has
  no 10-Q; capture Q4 from the FY release + 10-K or derive it -- see Q4 rule below),
  supplemental disclosures / investor datasheets where the company publishes them.

---

## Step 1 -- Quarterly Historicals Capture

### Tab structure

- Standard Tab Header rows 1-6 per firm-formatting (navy title `Company (TICKER) -- Quarterly
  Historicals`, units row, year row, dates row, spacer, first section header).
- Columns: one per fiscal quarter, oldest -> newest, left -> right. Labels follow the Year
  Label Convention: `FQ1 2022A`, `FQ2 2022A`, ... text format `@`, locally hardcoded. Row 4
  holds fiscal-quarter-end dates (`MM/DD/YY`).
- **Horizon: >= 16 quarters + all quarters of the current partial fiscal year.** Each new
  quarter is APPENDED as a new column post-earnings -- never overwrite an old column.
- No FY columns on this tab. Annual figures live on Annual Historicals; quarterly tie-out
  rows reference them cross-tab.
- Tab color teal `#436E71`, positioned immediately right of Annual Historicals. Freeze panes
  at `B5` (Freeze Pane Standard), gridlines off, Arial 10pt, uniform data column widths.

### Capture rules (identical to Annual Historicals -- see `meth-historicals.md`)

- **Capture everything the company reports.** Every line of every reported statement and
  supplemental schedule, at full reported granularity. Never summarize at capture --
  summarization happens downstream (statements, Model Tab, KPI Tracker). If the company
  reports it quarterly, it goes on this tab.
- Every captured value is a **hardcode: blue text #0000FF + source comment**
  (`Source: [doc], [date], [reference]`). No yellow backgrounds (yellow is reserved for
  projection assumptions on build tabs).
- Derived cells (discrete CFS quarters, derived Q4, tie-outs) are **formulas, black text** --
  never hardcoded, even though their inputs include cross-tab references.
- Sections the company does not report are **omitted entirely** -- never an empty header.
- Units declared once in row 2 and applied consistently.

### Section order (canonical -- same 13-section hierarchy as Annual Historicals)

Press-release data first, filing data second, provenance last:

| # | Section | Quarterly notes |
|---|---------|-----------------|
| 1 | Income Statement | Full reported face, every line, every quarter |
| 2 | Segment Data | Revenue + segment profit as reported; versioned blocks on recast (see meth-historicals) |
| 3 | Geographic Data | As disclosed (many companies report geo quarterly in the 10-Q) |
| 4 | Operational / KPI Data | The sector KPI taxonomy selected in Phase 0 Sector Discovery; capture every operating metric the company discloses quarterly |
| 5 | Share Count & Per-Share | Weighted basic/diluted, EOP shares, DPS declared, buyback $ and shares, SBC |
| 6 | Guidance | Guidance AS GIVEN with each quarter's release (see Guidance sub-spec below) |
| 7 | Balance Sheet | **Full reported face every quarter** (point-in-time -- no tie-out to annual needed beyond FQ4 = FY, which is checked) |
| 8 | Cash Flow Statement | As-reported YTD + derived discrete quarters (see CFS sub-spec below) |
| 9 | Debt Detail | Tranches, rates, maturities, revolver availability as disclosed in 10-Qs |
| 10 | Lease Detail | ASC 842 quarterly disclosures where given |
| 11 | Non-GAAP Reconciliations | As reported each quarter (adjusted EBITDA/EPS bridges) |
| 12 | Miscellaneous | D&A by segment, headcount, restructuring, FX impact, other recurring disclosures |
| 13 | Change Log | Bottom of tab; append-only (shared protocol with Annual Historicals -- see meth-historicals) |

### CFS sub-spec (Section 8)

Companies report quarterly cash flows **year-to-date**, not discretely. Capture both:

- **Sub-block A -- As-Reported YTD** (hardcode layer): every CFS line exactly as reported in
  each 10-Q (3mo/6mo/9mo YTD) and the 10-K (12mo). Blue hardcodes + source comments.
- **Sub-block B -- Derived Discrete Quarters** (formula layer): same line structure;
  `FQ1 = YTD Q1`, `FQn = YTD n − YTD n−1` (black formulas referencing sub-block A).
  FQ4 = FY (from Annual Historicals) − YTD Q3.

Downstream consumers (KPI Tracker, analysis) reference **sub-block B** (discrete).

### Q4 derivation rule (applies to IS and any flow section)

Where the company does not separately report Q4 (standard: Q4 has no 10-Q), derive it:
`FQ4 = FY − (FQ1 + FQ2 + FQ3)` -- **black formula**, FY referencing the Annual Historicals
cell. Derived Q4 cells are formulas, never hardcodes; label the column normally (the formula
itself is the documentation). Where the company DOES publish discrete Q4 figures (Q4 release
detail), capture those as hardcodes instead and let the tie-out row verify them.

### Tie-out rows (integrity)

At the bottom of each **flow** section (IS, discrete CFS, segment revenue/profit, and any
flow-type operational data): a tie-out row per fiscal year:

```
[Section] Tie-Out FYxxxx = SUM(FQ1:FQ4 of key line) − 'Annual Historicals'![FY value]
```

- Run the tie-out on the section's primary line(s) at minimum (Revenue, Net Income, CFO,
  segment revenue totals).
- Must = 0. A non-zero tie-out means a capture error, a restatement you haven't ingested, or
  a reported-vs-derived conflict -- resolve before proceeding. Where Q4 is derived, the
  tie-out is 0 by construction; note "derived" in the row label.
- Balance sheet: verify FQ4 EOP values = Annual Historicals FY values (point-in-time check).

### Guidance sub-spec (Section 6)

One block per guided metric. Columns align to the quarter **in which guidance was given**:

- Row per guided item: value captured as the **range and mid-point as given** (mid-point in
  the cell, range in the source comment), blue hardcode.
- When the actual later prints, it lands in Section 1/4 as normal capture -- the KPI Tracker's
  guide-vs-actual rows compare the two. Never overwrite guidance with actuals; guidance
  history is the point.
- Withdrawn/updated guidance: new value in the quarter it was updated; prior cells untouched;
  Change Log entry if the definition of the guided metric changed.

### Edge cases

All protocols in `meth-historicals.md` apply unchanged on the quarterly tab: segment recasts
(versioned blocks, freeze old presentation), ASC adoptions (note row, never self-restate),
FYE changes (transition stub columns, re-keyed quarter labels), restatements (overwrite +
comment + Change Log), discontinued ops (company-recast treatment), 52/53-week years
(Weeks in Period row).

### Subtask checklist (write COMPLETE + note to Task Tracker after each -- do not batch)

- P2.1 Quarterly column scaffold (>=16 quarters + partial year, labels, dates, header)
- P2.2 Income Statement capture (all quarters)
- P2.3 Segment / Geographic / Operational KPI capture
- P2.4 Share Count & Per-Share + Guidance capture
- P2.5 Balance Sheet capture (full face, all quarters)
- P2.6 CFS capture: as-reported YTD + derived discrete block
- P2.7 Debt / Lease / Non-GAAP / Miscellaneous capture
- P2.8 Tie-out rows built and all = 0; Change Log section present
- P2.9 KPI Tracker tab built (Step 2)
- P2.10 Verification (Tab Completion Verification + DoD read-back)

---

## Step 2 -- KPI Tracker Build

Load the **`kpi-tracker`** skill and execute its **Track A (build new tab)** methodology --
KPI selection lenses (management's page-1 metrics, bull/bear pivot, model build emphasis,
industry archetypes), proposed-KPI-list checkpoint with the user, 4-year quarterly + FY
column layout (5-column groups), forward columns through current FY + next full FY with
guidance mid-points, FY Derivation Matrix, and its full formatting spec.

**Phase-2 adaptations (these override the kpi-tracker skill where they conflict):**

1. **Skip Track selection (Step 0)** -- inside a model build this is always a new tab.
2. **The reference tab is Quarterly Historicals** -- do not ask; do not re-request source
   documents already ingested during capture. The proposed-KPI-list checkpoint (A4) is
   still REQUIRED -- present the list and wait for user approval before building.
3. **Live pulls, not hardcodes.** Historical quarterly values pull LIVE from Quarterly
   Historicals via cross-sheet formulas (green #008000 per firm-formatting). Do NOT run the
   kpi-tracker skill's convert-to-hardcoded-values portability step, and do not apply its
   "no green" color rule -- that rule exists for standalone trackers in foreign workbooks.
   Here, Quarterly Historicals is the single hardcode layer; duplicating hardcodes on the
   tracker would create a second update surface and violate the single-hardcode-layer rule.
   Post-earnings updates flow: new column on Quarterly Historicals -> tracker updates
   automatically.
4. **Intra-tab derived formulas stay live** exactly per the skill (margins, Y/Y growth, FY
   derivations, TTM, blank-input guards).
5. **Guidance rows pull from the Quarterly Historicals Guidance section** (green refs), not
   fresh hardcodes. Guide-vs-actual rows compute on-tab.
6. **Tab placement and color:** position 6 in the firm tab order (immediately right of
   Model), tab color navy `#1C3553`.
7. Forward quarter/FY columns: at this point in the build there are no model projections yet
   (Phases 3-4 build them). Forward cells remain blank except guidance mid-points, with the
   skill's blank-input-guarded formulas in derived rows. Do not fabricate estimates.

Everything else in the kpi-tracker skill applies as written: column widths/heights, spacing
and indentation rhythm, no-bold/no-borders body rules, FY gray-fill rectangles, forecast
divider, number formats with `_)` padding, source citations, final review checklist.

---

## Update Mode (post-earnings, after the model is built)

These two tabs are the model's update surface. **The `update-model` skill packages this workflow end-to-end** (identify print type -> capture -> flip tracker -> verify) — load it for post-earnings sessions instead of this phase skill. The mechanics, for reference:

1. **Append a column** to Quarterly Historicals; capture the release (sections 1-6) same-day
   and the 10-Q sections (7-12) when filed. New tie-out cells for a completed fiscal year.
2. **KPI Tracker**: follow the kpi-tracker skill's **Track B** -- flip the target column's
   label E -> A and move the forecast divider right (one column for FQ1-FQ3; two columns on
   an FQ4 close, which also flips the FY label -- per Track B's fiscal-year-close branch) --
   except values arrive via live links rather than manual writes: WRITE green cross-sheet
   formulas into the flipped column pulling the new quarter's cells from Quarterly
   Historicals (forward columns are blank until flipped, so the links must be created at
   flip time). Intra-tab derived rows (Y/Y, margins, FY formulas) then update automatically.
3. If a 10-K quarter: update Annual Historicals first (Phase 1's tab), then the quarterly
   column, then verify tie-outs.
4. Any recast/restatement/ASC change in the release: apply the meth-historicals protocol and
   log it in the Change Log before touching downstream tabs.

---

## Tab Completion Verification (paste both blocks)

```
TAB COMPLETION VERIFICATION -- Quarterly Historicals
Tab exists, position (right of Annual Historicals): [check/x]
Tab color = #436E71: [check/x]
freezePanes per Freeze Pane Standard: [check/x]   Gridlines off: [check/x]   Font = Arial 10: [check/x]
Quarters captured: [N] (>= 16 + current partial year: [check/x])
Sections present: [list -- omitted sections listed as "not reported"]
Hardcodes blue #0000FF + source comments (spot-check 3 cells): [cell refs + PASS/FAIL]
Derived cells are formulas (spot-check discrete CFS + derived Q4): [cell refs + PASS/FAIL]
Tie-out rows: [count] -- all = 0: [check/x, list any non-zero]
Change Log section present: [check/x]
```

```
TAB COMPLETION VERIFICATION -- KPI Tracker
Tab exists, position (right of Model Tab): [check/x]
Tab color = #1C3553: [check/x]
freezePanes per Freeze Pane Standard: [check/x]   Gridlines off: [check/x]   Font = Arial 10: [check/x]
KPI count: [N] across [N] sections
Column span: [first period] -> [last period] incl. forward columns: [check/x]
Historical cells = live green refs to Quarterly Historicals (spot-check 3): [cells + PASS/FAIL]
Forecast divider at first estimate column: [check/x]
Guidance rows wired from Quarterly Historicals Guidance section: [check/x]
kpi-tracker Final Review Checklist run: [check/x]
```

## Definition of Done

Do not write "Phase 2 complete" before reading these values back from the workbook:

1. Every Phase-2 subtask row (P2.1-P2.10) on the Task Tracker = COMPLETE with cited cell
   addresses in Notes.
2. >= 16 quarters + current partial year captured across every section the company reports
   (read back quarter count and section list).
3. All tie-out rows = 0 (read back each value; resolve any non-zero before proceeding).
4. Q4 / discrete-CFS derivations are formulas, not hardcodes (spot-check read-back).
5. KPI Tracker populated: read back KPI count, column span, and 3 spot-check values matching
   Quarterly Historicals.
6. Guide-vs-actual wired (read back one guidance row + its actual).
7. Both Tab Completion Verification blocks pasted above.
8. Model State Block updated: Last Skill Run = build-model-phase-02 + today's date,
   Next Skill = build-model-phase-03.

Then STOP. Report status. Wait for "continue."
