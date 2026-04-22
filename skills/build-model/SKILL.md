---
name: build-model
description: |
  FC modeling methodology — core rules and phase router. Load this skill at the start of every session. It provides the universal rules that apply across all phases and tells you which phase skill to load next.

  Triggers on: building a financial model, creating projections, 3-statement model, revenue build, debt schedule, PP&E build, returns analysis, valuation, XIRR, ingesting BAMSEC/Tegus/GS data, rebuilding an existing model, lease accounting, ASC 842 leases, operating leases, finance leases, or any model architecture decision.

  Core rules: exit-multiple preferred over DCF, 7 projection years, driver-based revenue, XIRR returns, YEARFRAC calendarization, ROIC/ROTIC standard in every model. Lease-aware: detects and models operating/finance leases with proper BS/IS/CFS linkage.
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
| **Phase 1 -- Historical Statements** | `read_skill("build-model-phase-1")` |
| **Phase 2 -- Drivers** | `read_skill("build-model-phase-2")` |
| **Phase 2.5 -- Driver Review** | `read_skill("build-model-phase-2")` (checkpoint section) |
| **Phase 3 -- Forward Statements** | `read_skill("build-model-phase-3")` |
| **Phase 4 -- Capital Allocation** | `read_skill("build-model-phase-4")` |
| **Phase 5 -- Model Tab** | `read_skill("build-model-phase-5")` |
| **Phase 6 -- Returns** | `read_skill("build-model-phase-6")` |
| **Phase 7 -- Formatting** | `read_skill("build-model-phase-7")` |
| **Phase 8 -- QC** | `read_skill("build-model-phase-8")` |
| **Phase 9 -- Output** | `read_skill("build-model-phase-9")` |
| **Phase 10 -- Consensus** | `read_skill("build-model-phase-10")` |
| **Phase 11 -- Hardcode Sources** | `read_skill("build-model-phase-11")` |

**Resume protocol:** If a Task Tracker tab exists, you are resuming. Read the tracker. Identify the current phase from the Model State Block. Load ONLY that phase's skill. Do NOT reload Phase 0 content.

### Skill Drift Audit (Resume Sessions Only)

If you are resuming a session and the Task Tracker shows phases already complete, perform this audit BEFORE starting new work:

1. Pick 3 cells at random from completed phases (one from a build tab, one from a statement tab, one from a summary/output tab if available)
2. For each cell, verify:
   - Font color matches firm-formatting rules (blue = hardcode, black = formula, green = cross-sheet)
   - Number format includes `_)` padding
   - If assumption cell: yellow bg (#FFFF00) present, source comment present
3. Output the audit results to the user:
   ```
   DRIFT AUDIT (3 cells from completed phases):
   [Tab]![Cell]: font=[color], format=[format string], bg=[color] — [PASS/FAIL]
   [Tab]![Cell]: font=[color], format=[format string], bg=[color] — [PASS/FAIL]
   [Tab]![Cell]: font=[color], format=[format string], bg=[color] — [PASS/FAIL]
   ```

If any cell fails, flag to user: "Drift detected in [tab]![cell]: [issue]. Recommend re-running Phase 7 formatting on affected tabs before proceeding." Do not skip this audit.

**Phase boundaries:** After completing each phase, STOP. Update the Task Tracker. Report status. Wait for "continue."

**Context management:** After completing a phase, the user should start a new conversation and reload: `build-model` + `firm-formatting` + the next phase's skill. This keeps context clean and ensures full phase instructions are available.

**Sequencing enforcement:** Each phase must complete before the next begins. Phase 0 -> 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7 -> 8 -> 9 -> 10 -> 11. No skipping. Dependencies are specified in each phase's skill.

All skills are designed for Claude for Excel cell-level operations.

---

## Core Rules (Always Apply -- Every Phase)

### No Freeze Panes
**Never freeze panes on any tab, ever.** No exceptions. Gridlines off. No merged cells in data area.

### Projection Length
**7 dynamic projection years** (5yr hold + NTM exit window). Calculate from today's date + FYE. When in doubt, add one more year.

### Historical Periods
As many years of actuals as available. No fixed minimum -- use what the data gives you.

### Revenue Build Priority
1. Unit economics / driver-based (volume x price/rate) -- strongly preferred
2. Segment-level with identified drivers -- acceptable
3. Growth rate decomposition -- last resort

Never slap growth rates on revenue without decomposing into drivers.

### Exit-Multiple Preferred
Exit-multiple over DCF. Only build DCF when explicitly requested. Multiple hierarchy: FCF > P/E > EBIT > EBITDA (closest to cash wins).

### Formula Construction
- Always use Excel formulas -- never hardcode calculations in Python and paste values
- **Never embed assumptions as in-cell hardcodes within output/data formulas.** Every assumption must be a clearly identifiable cell with **yellow background (#FFFF00) + blue text (#0000FF)** and a source comment.
- Historical totals must also be formulas (`=SUM()` or equivalent)
- Comment every assumption with rationale and source
- Only ASCII in comments (no em dashes, smart quotes)

### Assumption Placement: Same Row, Not Separate
Assumption values go directly in the **projection columns of the existing metric row** -- the same row that holds the historical values. Do NOT create a separate "assumption" row underneath. One row carries both: historical values (black text) on the left, assumption values (yellow bg + blue text + source comment) on the right.

### No Hardcoded Historicals
**Every historical value in the model must be a formula linking to a source tab** -- never a hardcoded number. Historical cells reference BAMSEC, Tegus, broker model, or the Historical Data tab via direct cell references. The audit trail must be one click away.

### Mandatory Dual View (IS, BS, CFS)
Every IS, BS, and CFS tab MUST have both a **Model View** and a **Reported View** -- no exceptions. Even if they would look identical, produce both. The Model View can simplify or collapse lines, but both views must always exist. The Reported View is the audit trail; the Model View is the projection engine.

### Build-First Rule (HARD STOP)
**Never populate projection assumptions directly on the IS, BS, or CFS tabs.** Assumptions and driver logic live on build tabs. The IS/BS/CFS tabs contain only:
1. **Pull formulas** -- references to build tabs
2. **Derived computations** -- formulas computed from other cells on the same statement

### Balance Sheet Integrity
Always include:
- **BS Check row**: `Total Assets - Total Liabilities & Equity = 0`
- **CF Check row**: `Beginning Cash + Total Cash Flow = Ending Cash`
- **Capital Allocation Check**: `Unreconciled Residual = 0`

If any check is non-zero, stop and debug. **Never use a plug, balancing item, or "other" line to force a check to zero.**

### Lease Awareness
When source data contains operating or finance leases (ASC 842), the model must include dedicated lease schedules on the Debt Build tab. Lease detection occurs in Phase 0/1. Key flags tracked on the Task Tracker: `HAS_OPERATING_LEASES`, `HAS_FINANCE_LEASES`, `FL_IN_PPE`, `LEASE_MATERIALITY`, and the full Lease BS Map. The critical rule: **CF Financing finance lease payment = principal only (depreciation amount), NEVER total payment (depreciation + interest).**

### Iterative Calculation Required
After Phase 4, the model contains intentional circular references (Capital Allocation waterfall depends on CFS, CFS depends on waterfall). **Enable iterative calculation in Excel** (`File > Options > Formulas > Enable iterative calculation`) before running Phase 4. Without it, the model will show circular reference errors.

### Subtask Completion Protocol

After completing each subtask (e.g., P2.1, P2.2, P2.3) -- before starting the next:
1. Write "COMPLETE" to the corresponding Task Tracker row (Status column)
2. Write a 1-line summary to the Notes column
3. Output to the user: "Subtask [ID] complete. Tracker updated."

Do NOT batch these updates at phase end. Small, frequent writes during work -- not one big write after. If you complete a subtask and move to the next without updating the tracker, you have made an error. Stop and update.

### ROIC & ROTIC
Standard in every model. ROIC and ROTIC on the Model Tab. Full formulas provided in the Phase 5 skill.

---

## Data Standards

### Data Source Hierarchy

Use the highest-ranked available source. Never skip a tier unless the data genuinely doesn't exist there.

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
