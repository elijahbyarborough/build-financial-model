        1 # Build Financial Model
        2
        3 A modular skill system for building institutional-quality Excel financial models with Claude for Excel. The system encodes Frist Capital's modeling methodology as a set of interconnected Claude skills that guide a 12-phase model build from raw source data to a fini
          shed, QC'd investment summary.
        4
        5 ## What This Is
        6
        7 This repo contains 15 Claude skills (47 markdown files) organized into two foundation skills and 12 phase-specific skills. Together they define:
        8
        9 - **Modeling methodology** -- how to structure a 3-statement model, build driver-based revenue, handle lease accounting, compute ROIC/ROTIC, and construct a 5-year forward returns framework
       10 - **Formatting standards** -- the firm's complete visual hierarchy for Excel: fonts, colors, borders, number formats, spacer rows, and tab organization
       11 - **Quality gates** -- structural integrity checks, assumption audits, and consistency validations that every model must pass
       12
       13 The system is designed for **Claude for Excel** (the Excel add-in), where Claude operates at the cell level -- reading ranges, writing formulas, applying formatting, and building tabs. It is not a Python script or a template generator. The skills teach Claude *how
          to think about* building a model, and Claude executes directly in the workbook.
       14
       15 ## Skill Architecture
       16
       17 ### Foundation Skills (loaded every session)
       18
       19 | Skill | Purpose |
       20 |-------|---------|
       21 | `build-model` | Core rules, phase router, and universal conventions (projection length, formula construction, balance sheet integrity, lease awareness) |
       22 | `firm-formatting` | Single source of truth for all Excel formatting: 5-tier visual hierarchy, number formats with `_)` padding, color coding, borders, tab colors, display settings |
       23
       24 ### Phase Skills (loaded one at a time)
       25
       26 | Phase | Skill | What It Does |
       27 |-------|-------|-------------|
       28 | 0 | `build-model-phase-0` | Setup -- ingest source data, create all tabs, build Task Tracker, detect lease exposure, set column structure |
       29 | 1 | `build-model-phase-1` | Historical Statements -- populate IS, BS, CFS from source data with Model View + Reported View |
       30 | 2 | `build-model-phase-2` | Drivers -- build all driver tabs (Revenue, Costs, PP&E, WC, Tax, Debt) with historical data and assumptions |
       31 | 3 | `build-model-phase-3` | Forward Statements -- project IS, BS, CFS using driver tab outputs |
       32 | 4 | `build-model-phase-4` | Capital Allocation -- cash waterfall, buybacks, dividends, M&A program, share count (enables circular references) |
       33 | 5 | `build-model-phase-5` | Model Tab -- consolidate all outputs: Platinum List Indicators, Summary IS, KPIs, Capital Allocation, ROIC/ROTIC, Summary BS, Summary CFS |
       34 | 6 | `build-model-phase-6` | Returns -- XIRR-based 5-year forward returns with EPS CAGR, dividend yield, M&A value, and multiple change decomposition |
       35 | 7 | `build-model-phase-7` | Formatting -- apply firm-standard formatting to every tab, reorder tabs, apply tab colors |
       36 | 8 | `build-model-phase-8` | QC -- run 4 quality gates (structural integrity, assumption review, Model Tab consistency, returns sanity), WC denominator check, tab organization check |
       37 | 9 | `build-model-phase-9` | Output Tab -- one-page investment summary with Returns Timeline, Summary IS, Key Drivers, Capital Allocation, and ROIC |
       38 | 10 | `build-model-phase-10` | Consensus -- model-vs-street comparison using stacked triplets (Model / Consensus / Delta) with conditional formatting |
       39 | 11 | `build-model-phase-11` | Hardcode Sources -- replace live external formulas with static values and document provenance |
       40
       41 ## How to Use
       42
       43 ### Prerequisites
       44
       45 - **Claude for Excel** add-in installed and active
       46 - A workbook with source data (BAMSEC filing data, Tegus models, broker models, or raw financial statements)
       47 - Skills loaded into Claude's context via `read_skill()` commands
       48
       49 ### Starting a New Model
       50
       51 1. Open the workbook in Excel with Claude for Excel active.
       52
       53 2. Load the two foundation skills:
       54    ```
       55    read_skill("build-model")
       56    read_skill("firm-formatting")
       57    ```
       58
       59 3. Load Phase 0:
       60    ```
       61    read_skill("build-model-phase-0")
       62    ```
       63
       64 4. Tell Claude to begin: *"Build a new model for [TICKER]. FYE is [month]. Source data is on the [tab name] tab."*
       65
       66 5. Claude will create all tabs, set up the Task Tracker, detect lease exposure, and prepare the workbook structure.
       67
       68 ### Working Through Phases
       69
       70 Each phase is a separate conversation to keep context clean:
       71
       72 1. **Start a new conversation** (or clear context)
       73 2. **Load foundation skills**: `read_skill("build-model")` + `read_skill("firm-formatting")`
       74 3. **Load the current phase skill**: `read_skill("build-model-phase-N")`
       75 4. **Tell Claude to continue**: *"Continue the model build."* Claude reads the Task Tracker to pick up where it left off.
       76 5. **Claude completes the phase**, updates the Task Tracker, and reports status.
       77 6. **Repeat** for the next phase.
       78
       79 ### Resuming an In-Progress Model
       80
       81 If a Task Tracker tab exists in the workbook, Claude will read it to determine the current phase, completed work, and any flags (lease exposure, sector classification, etc.). Load the foundation skills + the current phase skill, and Claude picks up where it left of
          f.
       82
       83 ### Phase Sequencing
       84
       85 Phases must execute in order: 0 -> 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7 -> 8 -> 9 -> 10 -> 11. Each phase has explicit prerequisites and produces outputs that downstream phases depend on. Skipping phases will produce a broken model.
       86
       87 ## Key Modeling Conventions
       88
       89 - **Exit-multiple preferred** over DCF (FCF > P/E > EBIT > EBITDA)
       90 - **7 projection years** (5-year hold + NTM exit window)
       91 - **Driver-based revenue** -- never growth rates without decomposition
       92 - **ROIC/ROTIC standard** in every model
       93 - **Mandatory dual view** -- IS, BS, CFS each have Model View + Reported View
       94 - **No hardcoded historicals** -- every value links to a source tab
       95 - **Assumptions in-row** -- yellow bg + blue text in projection columns of the same row as historicals
       96 - **YEARFRAC calendarization** for entry/exit EPS blending
       97 - **ASC 842 lease-aware** -- operating and finance lease schedules on the Debt Build
       98 - **Iterative calculation required** after Phase 4 (circular Capital Allocation waterfall)
       99 - **Balance sheet must balance** -- BS Check = 0, CF Check = 0, no plugs ever
      100
      101 ## Formatting Standards
      102
      103 The `firm-formatting` skill defines the complete visual specification:
      104
      105 - **Font**: Arial 10pt everywhere, no exceptions
      106 - **Visual hierarchy**: 5 tiers (Section Header, Subheader, Year Row, Subtotal, Major Total)
      107 - **Number formats**: All include `_)` right-padding; Section Boundary Rule controls `$` placement
      108 - **Color coding**: Blue = hardcoded assumptions, Black = formulas, Green = cross-sheet refs, Red = external data (SPG())
      109 - **Tab colors**: Navy (primary output), Sage green (financial statements), Steel blue (builds), Amber (Task Tracker), Gray (reference)
      110 - **No freeze panes, no gridlines, no merged cells**
      111
      112 ## Repository Structure
      113
      114 ```
      115 skills/
      116   build-model/              # Core rules + phase router
      117     SKILL.md
      118     data-quality.md
      119   firm-formatting/          # Firm-wide formatting standards
      120     SKILL.md
      121     firm-formatting.md
      122   build-model-phase-0/      # Setup
      123   build-model-phase-1/      # Historical Statements
      124     meth-lease.md
      125     meth-ppe-sign.md
      126     meth-working-capital.md
      127   build-model-phase-2/      # Drivers
      128     meth-revenue.md
      129     meth-cost.md
      130     meth-ppe-build.md
      131     meth-working-capital.md
      132     meth-tax.md
      133     meth-debt-build.md
      134   build-model-phase-3/      # Forward Statements
      135   build-model-phase-4/      # Capital Allocation
      136   build-model-phase-5/      # Model Tab
      137     meth-roic.md
      138   build-model-phase-6/      # Returns
      139   build-model-phase-7/      # Formatting
      140   build-model-phase-8/      # QC
      141   build-model-phase-9/      # Output Tab
      142   build-model-phase-10/     # Consensus
      143   build-model-phase-11/     # Hardcode Sources
      144 ```
      145
      146 Each phase directory contains a `SKILL.md` (entry point) and one or more detailed reference files (`phase-N-*.md`, `meth-*.md`).
