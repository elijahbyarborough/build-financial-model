---
name: build-model
description: |
  FC modeling methodology — core rules and phase router. Load this skill at the start of every session. It provides the universal rules that apply across all phases and tells you which phase skill to load next.

  Triggers on: building a financial model, creating projections, 3-statement model, profit build, debt schedule, historicals capture, quarterly KPI tracker, returns analysis, valuation, XIRR, ingesting BAMSEC/Tegus/GS data, rebuilding an existing model, updating a model post-earnings, lease accounting, ASC 842 leases, operating leases, finance leases, or any model architecture decision.

  Core rules: exit-multiple preferred over DCF, 7 projection years, driver-based revenue, XIRR returns, YEARFRAC calendarization. All reported data captured on Annual/Quarterly Historicals tabs (the single hardcode layer). Credit-adjusted EBITDA for leverage ratios on acquisitive companies. Lease-aware: detects and models operating/finance leases with proper BS/IS/CFS linkage.
---

# FC Modeling Conventions

## Phase Router — Read This First

Identify the current phase, then load ONLY that phase's skill. Do not load all phase skills at once.

**At the start of every session, load these two foundation skills:**
1. `read_skill("build-model")` (this skill — core rules + phase router)
2. `read_skill("firm-formatting")` (formatting standards — always needed)

**Then load the current phase skill:**

| Current Phase | Load This Skill |
|---------------|----------------|
| **New build** | `read_skill("build-model-phase-0")` |
| **Resuming** (Task Tracker exists) | Read Task Tracker -> identify current phase -> load that phase's skill below |
| **Phase 1 -- Annual Historicals & Statements** | `read_skill("build-model-phase-1")` |
| **Phase 2 -- Quarterly Historicals & KPI Tracker** | `read_skill("build-model-phase-2")` (also loads `kpi-tracker`) |
| **Phase 3 -- Drivers** | `read_skill("build-model-phase-3")` |
| **Phase 3.5 -- Driver Review** | `read_skill("build-model-phase-3")` (checkpoint section) |
| **Phase 4 -- Forward Statements** | `read_skill("build-model-phase-4")` |
| **Phase 5 -- Capital Allocation** | `read_skill("build-model-phase-5")` |
| **Phase 6 -- Model Tab** | `read_skill("build-model-phase-6")` |
| **Phase 7 -- Returns** | `read_skill("build-model-phase-7")` |
| **Phase 8 -- Formatting** | `read_skill("build-model-phase-8")` |
| **Phase 9 -- QC** | `read_skill("build-model-phase-9")` |
| **Phase 10 -- Output** | `read_skill("build-model-phase-10")` |
| **Phase 11 -- Consensus** | `read_skill("build-model-phase-11")` |
| **Phase 12 -- Source Hygiene** | `read_skill("build-model-phase-12")` |
| **Post-build: company reported earnings / new filing** | `read_skill("update-model")` (+ `kpi-tracker`) |

**Resume protocol:** If a Task Tracker tab exists, you are resuming. Read the tracker. Identify the current phase from the Model State Block. Load ONLY that phase's skill. Do NOT reload Phase 0 content.

### Skill Drift Audit (Resume Sessions Only)

If you are resuming a session and the Task Tracker shows phases already complete, perform this audit BEFORE starting new work:

1. Pick 3 cells at random from completed phases (one from a build tab, one from a statement tab, one from a summary/output tab if available)
2. For each cell, verify:
   - Font color matches firm-formatting rules (blue = hardcode, black = formula, green = cross-sheet)
   - Number format includes `_)` padding (Integer-format cells are exempt -- no parenthetical negatives expected)
   - If assumption cell: yellow bg (#FFFF00) present, source comment present
3. Output the audit results to the user:
   ```
   DRIFT AUDIT (3 cells from completed phases):
   [Tab]![Cell]: font=[color], format=[format string], bg=[color] — [PASS/FAIL]
   [Tab]![Cell]: font=[color], format=[format string], bg=[color] — [PASS/FAIL]
   [Tab]![Cell]: font=[color], format=[format string], bg=[color] — [PASS/FAIL]
   ```

If any cell fails, flag to user: "Drift detected in [tab]![cell]: [issue]. Recommend re-running Phase 8 formatting on affected tabs before proceeding." Do not skip this audit.

**Phase boundaries:** After completing each phase, STOP. Update the Task Tracker. Report status. Wait for "continue."

**Context management:** After completing a phase, the user should start a new conversation and reload: `build-model` + `firm-formatting` + the next phase's skill. This keeps context clean and ensures full phase instructions are available.

**Sequencing enforcement:** Each phase must complete before the next begins. Phase 0 -> 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7 -> 8 -> 9 -> 10 -> 11 -> 12. No skipping. Dependencies are specified in each phase's skill.

All skills are designed for Claude for Excel cell-level operations.

---

## Core Rules (Always Apply -- Every Phase)

### No Freeze Panes
**Never freeze panes on any tab, ever.** No exceptions. Gridlines off. No merged cells in data area.

### Projection Length
**7 dynamic projection years** (5yr hold + NTM exit window). Calculate from today's date + FYE. When in doubt, add one more year.

### Historical Periods
As many years of actuals as available. No fixed minimum -- use what the data gives you.

### Revenue Driver Priority
1. Unit economics / driver-based (volume x price/rate) -- strongly preferred
2. Segment-level with identified drivers -- acceptable
3. Growth rate decomposition -- last resort

Never slap growth rates on revenue without decomposing into drivers.

### Exit-Multiple Preferred
Exit-multiple over DCF. Only build DCF when explicitly requested. Multiple hierarchy: FCF > P/E > EBIT > EBITDA (closest to cash wins).

### Formula Construction
- Always use Excel formulas -- never hardcode calculations in Python and paste values
- **Never embed assumptions as in-cell hardcodes within output/data formulas.** Every model driver assumption must be a clearly identifiable cell with **yellow background (#FFFF00) + blue text (#0000FF)** and a source comment. Yellow is reserved for projection driver assumptions on build tabs and the Returns tab. (Guidance in a plugin-built model is CAPTURED on the Quarterly Historicals Guidance section, blue + comment; the KPI Tracker links to it in green. Only in the kpi-tracker skill's standalone mode do guidance mid-points sit on the tracker as blue-only hardcodes.)
- Historical totals must also be formulas (`=SUM()` or equivalent)
- Comment every assumption with rationale and source
- Only ASCII in comments (no em dashes, smart quotes)

### Assumption Placement: Same Row, Not Separate
Assumption values go directly in the **projection columns of the existing metric row** -- the same row that holds the historical values. Do NOT create a separate "assumption" row underneath. One row carries both: historical values (black text) on the left, assumption values (yellow bg + blue text + source comment) on the right.

### Single Hardcode Layer (Historicals Tabs)
**The Annual Historicals and Quarterly Historicals tabs are the ONLY place hardcoded historical values are allowed** -- entered at capture with blue text (#0000FF) and a source comment citing the filing. Every historical value anywhere else in the model (statements, build tabs, Model Tab, KPI Tracker) must be a formula linking back to a Historicals tab. The audit trail is the source comment, one click away. Raw source tabs (BAMSEC, Tegus, broker) are working material for capture, never live link targets.

### Hardcode Registry (the complete list of legitimate hardcode sites)

Every QC audit (Gates 2 and 5, Phase 12) validates against THIS list. A blue-text data cell anywhere not on it is a finding; a cell on it is never a finding.

| Site | What | Marking |
|------|------|---------|
| Annual / Quarterly Historicals | All captured reported values (historical hardcodes) | Blue + source comment |
| Profit Build, BS & CFS Build, Capital Allocation Build | Projection driver assumptions | Yellow bg + blue + source comment |
| Returns tab | Exactly 3: Entry Price, Entry Date, Exit P/E | Yellow bg + blue + source comment |
| Output tab | Exit P/E mirror -- a FORMULA (`=Returns!Exit P/E`) displayed in blue as an assumption cue; not a true hardcode | Blue font on formula (registered exception) |
| KPI Tracker | Plugin-built models: NONE (fully link-based, incl. guidance -- links to the Quarterly Historicals Guidance section). Standalone mode only: historicals + guidance hardcodes | Blue (standalone only) |
| Phase 12 freezes | Stray source-tab references frozen to values | Blue + comment tagged `Frozen: Phase 12 [date], from [ref]` |
| Structural labels | Year headers, row labels, titles | Black (never counted as hardcodes) |

### Capture Everything
The Historicals tabs are the "we captured everything the company reports" record. Enter every line the company discloses in each section -- full statement faces, all segment/geographic/operational detail, share data, guidance, footnote schedules. Summarization happens downstream (statements, Model Tab, KPI Tracker), never at capture. Section hierarchy and edge-case protocols (segment recasts, ASC adoptions, FYE changes, restatements) are defined in `meth-historicals.md` (Phases 1-2).

### Single Model View (IS, BS, CFS)
Each statement tab carries ONE view -- the Model View. Historical columns link to the Annual Historicals tab; projection columns pull from the build tabs. There is no separate Reported View: the Historicals tabs ARE the as-reported record. Each statement keeps a slim reconciliation check (Model NI / Total Assets / Ending Cash = reported value on Annual Historicals) proving the Model View consolidation lost nothing.

### Build-First Rule (HARD STOP)
**Never populate projection assumptions directly on the IS, BS, or CFS tabs.** Assumptions and driver logic live on the build tabs -- **Profit Build** (revenue, costs, segments, EBITDA, tax), **BS & CFS Build** (PP&E, working capital, debt & cash, leases), and **Capital Allocation Build**. The IS/BS/CFS tabs contain only:
1. **Pull formulas** -- references to build tabs (projections) and Annual Historicals (historicals)
2. **Derived computations** -- formulas computed from other cells on the same statement

### Balance Sheet Integrity
Always include:
- **BS Check row**: `Total Assets - Total Liabilities & Equity = 0`
- **CF Check row**: `Beginning Cash + Total Cash Flow = Ending Cash`
- **Capital Allocation Check**: `Unreconciled Residual = 0`

If any check is non-zero, stop and debug. **Never use a plug, balancing item, or "other" line to force a check to zero.**

### Lease Awareness
When source data contains operating or finance leases (ASC 842), the model must include dedicated lease schedules on the BS & CFS Build tab. Lease detection occurs in Phase 0/1. Key flags tracked on the Task Tracker: `HAS_OPERATING_LEASES`, `HAS_FINANCE_LEASES`, `FL_IN_PPE`, `LEASE_MATERIALITY`, and the full Lease BS Map. The critical rule: **CF Financing finance lease payment = principal only (depreciation amount), NEVER total payment (depreciation + interest).**

### Iterative Calculation Required
After Phase 5, the model contains intentional circular references (Capital Allocation waterfall depends on CFS, CFS depends on waterfall). **Enable iterative calculation in Excel** (`File > Options > Formulas > Enable iterative calculation`) before running Phase 5. Without it, the model will show circular reference errors. Phases 0-4 are a straight-line dependency chain and need no iteration.

### Subtask Completion Protocol

After completing each subtask (e.g., P2.1, P2.2, P2.3) -- before starting the next:
1. Write "COMPLETE" to the corresponding Task Tracker row (Status column)
2. Write a 1-line summary to the Notes column
3. Output to the user: "Subtask [ID] complete. Tracker updated."

Do NOT batch these updates at phase end. Small, frequent writes during work -- not one big write after. If you complete a subtask and move to the next without updating the tracker, you have made an error. Stop and update.

### Credit-Adjusted EBITDA for Leverage Ratios
On companies with a projected M&A program, leverage ratios (Net Debt/EBITDA, Interest Coverage) must use **Credit-Adjusted EBITDA** = Model EBITDA + Cumulative Acquired EBITDA, where Acquired EBITDA is derived from the acquisition spend and an assumed acquisition EV/EBITDA multiple, and the acquired base compounds at an assumed Acquired EBITDA Growth % (both yellow/blue assumptions on the Capital Allocation Build). Acquired EBITDA is a MEMO concept: it exists only for the leverage ratios and never flows into the IS, CFS, EPS, or Returns. Without it, acquisition-funded balance sheets divide by organic-only EBITDA and leverage is overstated. Full spec in the Phase 5 skill.

---

## Data Standards

### Data Source Hierarchy

Use the highest-ranked available source. Never skip a tier unless the data genuinely doesn't exist there. Sources feed CAPTURE onto the Historicals tabs (hardcoded entry + source comment); the model links to the Historicals tabs, never to the sources directly.

1. **Company filings** (10-K, 10-Q, proxy, earnings releases) -- primary, always preferred
2. **Tegus models & management guidance** -- secondary, especially for forward-looking assumptions
3. **Equity research & Capital IQ consensus** -- tertiary, for triangulation and sanity-checking only
4. **Qualitative sources** (transcripts, memos, industry reports) -- context and color, not hard numbers

### SPG() Formula Patterns

Three parameter patterns for S&P Capital IQ formulas:

| Pattern | # Params | Example |
|---------|----------|---------|
| Spot market data | 3 (entity, field, date) | `=SPG("NYSE:URI","SP_LASTSALEPRICE",$B$2)` |
| Period-based market | 4 (entity, field, period, date) | `=SPG("NYSE:URI","SP_MARKETCAP_OUT","LTM",$B$2)` |
| Fundamental/estimate | 4 (entity, field, period, date) | `=SPG("NYSE:URI","IQ_EBITDA","FY2025",$B$2)` |

**SPG() Rules:**
- Entity format: Always `Exchange:Ticker` (e.g., NYSE:URI, NASDAQGS:WSC). Never just the ticker.
- Date parameter: Always an anchored cell reference `$B$2` pointing to a central "As-Of Date" cell. Never hardcode dates in formulas.
- Ticker reference: Use an anchored cell reference `$B$5` so rows copy easily across peers.
- Font color: Red text (#FF0000) per firm-formatting color coding.

### Source Comments

Every hardcoded input needs a source comment. No exceptions.

**Format:** `Source: [Document Type], [Date], [Specific Reference]`

**Examples:**
- `Source: URI 10-K (FY2025), p.47, Segment Revenue Table`
- `Source: Management guidance, Q4 2025 earnings call`
- `Source: Capital IQ consensus, retrieved 2026-03-03`
- `Source: Analyst assumption -- based on 5yr historical average DSO of 42 days`

**Rules:**
- Only ASCII characters in comments (no em dashes, smart quotes, special characters)
- If an assumption is your own judgment, say so explicitly: "Source: Analyst assumption -- [rationale]"
- Update the source comment whenever the value changes
