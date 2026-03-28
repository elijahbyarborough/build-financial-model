---
name: build-model
description: |
  FC modeling methodology — core rules and phase router. Load this skill at the start of every session. It provides the universal rules that apply across all phases and tells you which phase skill to load next.

  Triggers on: building a financial model, creating projections, 3-statement model, revenue build, debt schedule, PP&E build, returns analysis, valuation, XIRR, ingesting BAMSEC/Tegus/GS data, rebuilding an existing model, lease accounting, ASC 842 leases, operating leases, finance leases, or any model architecture decision.

  Core rules: exit-multiple preferred over DCF, 7 projection years, driver-based revenue, XIRR returns, YEARFRAC calendarization, ROIC/ROTIC standard in every model. Lease-aware: detects and models operating/finance leases with proper BS/IS/CF linkage.
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

### Build-First Rule (HARD STOP)
**Never populate projection assumptions directly on the IS, BS, or CF tabs.** Assumptions and driver logic live on build tabs. The IS/BS/CF tabs contain only:
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
After Phase 4, the model contains intentional circular references (Capital Allocation waterfall depends on CF, CF depends on waterfall). **Enable iterative calculation in Excel** (`File > Options > Formulas > Enable iterative calculation`) before running Phase 4. Without it, the model will show circular reference errors.

### ROIC & ROTIC
Standard in every model. ROIC and ROTIC on the Model Tab. Full formulas provided in the Phase 5 skill.
