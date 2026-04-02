# Build Financial Model

A modular skill system for building institutional-quality Excel financial models with Claude for Excel. The system encodes Frist Capital's modeling methodology as a set of interconnected Claude skills that guide a 12-phase model build from raw source data to a finished, QC'd investment summary.

## What This Is

This repo contains 15 Claude skills (47 markdown files) organized into two foundation skills and 12 phase-specific skills. Together they define:

- **Modeling methodology** — how to structure a 3-statement model, build driver-based revenue, handle lease accounting, compute ROIC/ROTIC, and construct a 5-year forward returns framework
- **Formatting standards** — the firm's complete visual hierarchy for Excel: fonts, colors, borders, number formats, spacer rows, and tab organization
- **Quality gates** — structural integrity checks, assumption audits, and consistency validations that every model must pass

The system is designed for **Claude for Excel** (the Excel add-in), where Claude operates at the cell level — reading ranges, writing formulas, applying formatting, and building tabs. It is not a Python script or a template generator. The skills teach Claude *how to think about* building a model, and Claude executes directly in the workbook.

## Skill Architecture

### Foundation Skills (loaded every session)

| Skill | Purpose |
|-------|---------|
| `build-model` | Core rules, phase router, and universal conventions (projection length, formula construction, balance sheet integrity, lease awareness) |
| `firm-formatting` | Single source of truth for all Excel formatting: 5-tier visual hierarchy, number formats with `_)` padding, color coding, borders, tab colors, display settings |

### Phase Skills (loaded one at a time)

| Phase | Skill | What It Does |
|-------|-------|-------------|
| 0 | `build-model-phase-0` | Setup — ingest source data, create all tabs, build Task Tracker, detect lease exposure, set column structure |
| 1 | `build-model-phase-1` | Historical Statements — populate IS, BS, CFS from source data with Model View + Reported View |
| 2 | `build-model-phase-2` | Drivers — build all driver tabs (Revenue, Costs, PP&E, WC, Tax, Debt) with historical data and assumptions |
| 3 | `build-model-phase-3` | Forward Statements — project IS, BS, CFS using driver tab outputs |
| 4 | `build-model-phase-4` | Capital Allocation — cash waterfall, buybacks, dividends, M&A program, share count (enables circular references) |
| 5 | `build-model-phase-5` | Model Tab — consolidate all outputs: Platinum List Indicators, Summary IS, KPIs, Capital Allocation, ROIC/ROTIC, Summary BS, Summary CFS |
| 6 | `build-model-phase-6` | Returns — XIRR-based 5-year forward returns with EPS CAGR, dividend yield, M&A value, and multiple change decomposition |
| 7 | `build-model-phase-7` | Formatting — apply firm-standard formatting to every tab, reorder tabs, apply tab colors |
| 8 | `build-model-phase-8` | QC — run 4 quality gates (structural integrity, assumption review, Model Tab consistency, returns sanity), WC denominator check, tab organization check |
| 9 | `build-model-phase-9` | Output Tab — one-page investment summary with Returns Timeline, Summary IS, Key Drivers, Capital Allocation, and ROIC |
| 10 | `build-model-phase-10` | Consensus — model-vs-street comparison using stacked triplets (Model / Consensus / Delta) with conditional formatting |
| 11 | `build-model-phase-11` | Hardcode Sources — replace live external formulas with static values and document provenance |

## How to Use

### Prerequisites

- **Claude for Excel** add-in installed and active
- A workbook with source data (BAMSEC filing data, Tegus models, broker models, or raw financial statements)
- Skills loaded into Claude's context via `read_skill()` commands

### Starting a New Model

1. Open the workbook in Excel with Claude for Excel active.

2. Load the two foundation skills:
   ```
   read_skill("build-model")
   read_skill("firm-formatting")
   ```

3. Load Phase 0:
   ```
   read_skill("build-model-phase-0")
   ```

4. Tell Claude to begin: *"Build a new model for [TICKER]. FYE is [month]. Source data is on the [tab name] tab."*

5. Claude will create all tabs, set up the Task Tracker, detect lease exposure, and prepare the workbook structure.

### Working Through Phases

Each phase is a separate conversation to keep context clean:

1. **Start a new conversation** (or clear context)
2. **Load foundation skills**: `read_skill("build-model")` + `read_skill("firm-formatting")`
3. **Load the current phase skill**: `read_skill("build-model-phase-N")`
4. **Tell Claude to continue**: *"Continue the model build."* Claude reads the Task Tracker to pick up where it left off.
5. **Claude completes the phase**, updates the Task Tracker, and reports status.
6. **Repeat** for the next phase.

### Resuming an In-Progress Model

If a Task Tracker tab exists in the workbook, Claude will read it to determine the current phase, completed work, and any flags (lease exposure, sector classification, etc.). Load the foundation skills + the current phase skill, and Claude picks up where it left off.

### Phase Sequencing

Phases must execute in order: 0 → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9 → 10 → 11. Each phase has explicit prerequisites and produces outputs that downstream phases depend on. Skipping phases will produce a broken model.

## Key Modeling Conventions

- **Exit-multiple preferred** over DCF (FCF > P/E > EBIT > EBITDA)
- **7 projection years** (5-year hold + NTM exit window)
- **Driver-based revenue** — never growth rates without decomposition
- **ROIC/ROTIC standard** in every model
- **Mandatory dual view** — IS, BS, CFS each have Model View + Reported View
- **No hardcoded historicals** — every value links to a source tab
- **Assumptions in-row** — yellow bg + blue text in projection columns of the same row as historicals
- **YEARFRAC calendarization** for entry/exit EPS blending
- **ASC 842 lease-aware** — operating and finance lease schedules on the Debt Build
- **Iterative calculation required** after Phase 4 (circular Capital Allocation waterfall)
- **Balance sheet must balance** — BS Check = 0, CF Check = 0, no plugs ever

## Formatting Standards

The `firm-formatting` skill defines the complete visual specification:

- **Font**: Arial 10pt everywhere, no exceptions
- **Visual hierarchy**: 5 tiers (Section Header, Subheader, Year Row, Subtotal, Major Total)
- **Number formats**: All include `_)` right-padding; Section Boundary Rule controls `$` placement
- **Color coding**: Blue = hardcoded assumptions, Black = formulas, Green = cross-sheet refs, Red = external data (SPG())
- **Tab colors**: Navy (primary output), Sage green (financial statements), Steel blue (builds), Amber (Task Tracker), Gray (reference)
- **No freeze panes, no gridlines, no merged cells**

## Repository Structure

```
skills/
  build-model/              # Core rules + phase router
    SKILL.md
    data-quality.md
  firm-formatting/          # Firm-wide formatting standards
    SKILL.md
    firm-formatting.md
  build-model-phase-0/      # Setup
  build-model-phase-1/      # Historical Statements
    meth-lease.md
    meth-ppe-sign.md
    meth-working-capital.md
  build-model-phase-2/      # Drivers
    meth-revenue.md
    meth-cost.md
    meth-ppe-build.md
    meth-working-capital.md
    meth-tax.md
    meth-debt-build.md
  build-model-phase-3/      # Forward Statements
  build-model-phase-4/      # Capital Allocation
  build-model-phase-5/      # Model Tab
    meth-roic.md
  build-model-phase-6/      # Returns
  build-model-phase-7/      # Formatting
  build-model-phase-8/      # QC
  build-model-phase-9/      # Output Tab
  build-model-phase-10/     # Consensus
  build-model-phase-11/     # Hardcode Sources
```

Each phase directory contains a `SKILL.md` (entry point) and one or more detailed reference files (`phase-N-*.md`, `meth-*.md`).
