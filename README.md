# Build Financial Model

A modular skill system for building institutional-quality Excel financial models with Claude for Excel. The system encodes Frist Capital's modeling methodology as a set of interconnected Claude skills that guide a 13-phase model build (Phases 0–12) from raw source data to a finished, QC'd investment summary — packaged as a single installable Claude plugin (`fc-model-builder`).

## What This Is

This repo contains 17 Claude skills organized into four foundation/utility skills and 13 phase-specific skills. Together they define:

- **Historicals capture** — Annual Historicals and Quarterly Historicals tabs that record *everything the company reports* (the model's single hardcode layer, updated post-earnings and post-filing), with protocols for segment recasts, ASC adoptions, FYE changes, and restatements
- **Modeling methodology** — how to structure a 3-statement model, build driver-based revenue, handle lease accounting, compute ROIC/ROTIC, and construct a 5-year forward returns framework
- **Formatting standards** — the firm's complete visual hierarchy for Excel: fonts, colors, borders, number formats, spacer rows, and tab organization
- **Quality gates** — structural integrity checks, assumption audits, historicals-integrity checks, and consistency validations that every model must pass
- **KPI tracking** — a quarterly KPI tracker tab (4-year lookback + forward columns) built into every model
- **Post-earnings maintenance** — a dedicated update workflow that brings a finished model current when the company reports, without touching assumptions

The system is designed for **Claude for Excel** (the Excel add-in), where Claude operates at the cell level. It is not a Python script or a template generator. The skills teach Claude *how to think about* building a model, and Claude executes directly in the workbook.

## Skill Architecture

### Foundation Skills (loaded every session)

| Skill | Purpose |
|-------|---------|
| `build-model` | Core rules, phase router, and universal conventions (single hardcode layer, capture-everything, projection length, formula construction, balance sheet integrity, lease awareness) |
| `firm-formatting` | Single source of truth for all Excel formatting: 5-tier visual hierarchy, number formats with `_)` padding, color coding, borders, tab colors, display settings |

### Utility Skills

| Skill | Purpose |
|-------|---------|
| `kpi-tracker` | Build or update a quarterly KPI tracker tab. Two modes: plugin-model (pulls live from Quarterly Historicals) and standalone (portable, hardcoded). Loaded by Phase 2; also usable on its own. |
| `update-model` | Post-earnings / post-filing update for a finished model: capture the new quarter (or FY) onto the Historicals tabs, flip the KPI Tracker column E→A, verify integrity. Never touches drivers, projections, or Returns — those stay deliberate. |

### Phase Skills (loaded one at a time)

| Phase | Skill | What It Does |
|-------|-------|-------------|
| 0 | `build-model-phase-0` | Setup — create all tabs, build Task Tracker, detect lease exposure, run Sector Discovery (incl. KPI taxonomy selection), set column structure |
| 1 | `build-model-phase-1` | Annual Historicals & Statements — capture everything the company reports annually, then build Model-View-only IS/BS/CFS linking to it |
| 2 | `build-model-phase-2` | Quarterly Historicals & KPI Tracker — capture ≥16 quarters of reported data, build the KPI Tracker tab (loads `kpi-tracker`) |
| 3 | `build-model-phase-3` | Drivers — populate Profit Build (segments → EBITDA → tax) and BS & CFS Build (PP&E, WC, debt & cash, leases) with historicals, drivers, and forward projections |
| 4 | `build-model-phase-4` | Forward Statements — assemble projected IS, BS, CFS from the build tabs |
| 5 | `build-model-phase-5` | Capital Allocation — cash waterfall, buybacks, dividends, M&A program, share count (enables circular references) |
| 6 | `build-model-phase-6` | Model Tab — consolidate all outputs: Platinum List Indicators, Summary IS, KPIs, Capital Allocation, ROIC/ROTIC, Summary BS, Summary CFS |
| 7 | `build-model-phase-7` | Returns — XIRR-based 5-year forward returns with EPS CAGR, dividend yield, M&A value, and multiple change decomposition |
| 8 | `build-model-phase-8` | Formatting — apply firm-standard formatting to every tab, reorder tabs, apply tab colors |
| 9 | `build-model-phase-9` | QC — run 5 quality gates (structural integrity, assumption review, Model Tab consistency, returns sanity, historicals integrity) |
| 10 | `build-model-phase-10` | Output Tab — one-page investment summary with Returns Timeline, Summary IS, Key Drivers, Capital Allocation, and ROIC |
| 11 | `build-model-phase-11` | Consensus — model-vs-street comparison using stacked triplets (Model / Consensus / Delta) with conditional formatting |
| 12 | `build-model-phase-12` | Source Hygiene — verify the single hardcode layer holds, freeze any stray source references, confirm portability |

### Conductor Command (Claude Code / Cowork)

`commands/build-model.md` defines `/build-model` — an orchestrator that runs the phases in sequence, each in a fresh subagent (clean context per phase), checkpointing to the Task Tracker between stages. Default: pause after each phase for review. `/build-model auto`: roll through, stopping only on failed checks. Requires a harness with subagents (Claude Code or Cowork); in the Excel sidebar, run phases manually instead.

## The Tab Architecture

```
Task Tracker | Output | Consensus | Returns | Model | KPI Tracker |
IS | BS | CFS |
Profit Build | BS & CFS Build | Capital Allocation Build |
Annual Historicals | Quarterly Historicals |
Data Pull | Data Pull (Values) | [source tabs]
```

- **Annual / Quarterly Historicals** (teal): the "we captured everything the company reports" record — the ONLY tabs with hardcoded historicals (blue text + source comments). Updated post-earnings/filings. 13-section hierarchy ordered by update workflow: IS, segments, geographic, operational KPIs, share count, guidance, then BS, CFS, debt detail, lease detail, non-GAAP recons, miscellaneous, change log.
- **IS / BS / CFS** (sage): Model View only — historicals link to Annual Historicals, projections pull from the build tabs. Slim reconciliation checks prove nothing was lost.
- **Profit Build** (steel): segment sections (volume × price → revenue → costs → segment profit) → consolidated P&L bridge → tax.
- **BS & CFS Build** (steel): PP&E & capex → working capital → debt & cash → operating/finance lease schedules.
- **Capital Allocation Build** (steel): cash waterfall, buyback plug, dividend policy, M&A value build, share count.
- **KPI Tracker** (navy): quarterly scorecard, 4 years back + forward columns, pulling live from Quarterly Historicals.

## How to Use

### Install as a plugin

The repo is a single-plugin Claude marketplace. In Claude Code:

```
/plugin marketplace add elijahbyarborough/build-financial-model
/plugin install fc-model-builder@frist-capital
```

In claude.ai: Settings → Customize → Plugins → Personal plugins → + → Add marketplace → point at this repo.

### Starting a New Model

1. Open the workbook in Excel with Claude for Excel active.
2. Load the two foundation skills: `read_skill("build-model")` + `read_skill("firm-formatting")`
3. Load Phase 0: `read_skill("build-model-phase-0")`
4. Tell Claude to begin: *"Build a new model for [TICKER]. FYE is [month]. Source data is on the [tab name] tab."*

### Working Through Phases

Each phase is a separate conversation to keep context clean:

1. **Start a new conversation** (or clear context)
2. **Load foundation skills**: `read_skill("build-model")` + `read_skill("firm-formatting")`
3. **Load the current phase skill**: `read_skill("build-model-phase-N")` (Phase 2 also loads `kpi-tracker`)
4. **Tell Claude to continue**: *"Continue the model build."* Claude reads the Task Tracker to pick up where it left off.
5. **Claude completes the phase**, updates the Task Tracker, and reports status.
6. **Repeat** for the next phase — or run `/build-model` from Claude Code/Cowork to automate the loop.

### Resuming / Updating Post-Earnings

If a Task Tracker tab exists, Claude reads it to determine the current phase and flags. For a finished model, load **`update-model`** when the company reports: it captures the new quarter onto the Historicals tabs (release sections same-day, filing sections when the 10-Q/10-K drops), flips the KPI Tracker column via `kpi-tracker` Track B, and verifies integrity — leaving driver assumptions, horizon, and Returns anchoring as flagged manual follow-ups.

### Phase Sequencing

Phases must execute in order: 0 → 1 → 2 → … → 12. Each phase has explicit prerequisites and produces outputs that downstream phases depend on. Skipping phases will produce a broken model.

## Key Modeling Conventions

- **Single hardcode layer** — hardcoded historicals live ONLY on the Historicals tabs (blue text + source comment); everything else links
- **Capture everything** — the Historicals tabs record every line the company reports; summarize downstream, never at capture
- **Exit-multiple preferred** over DCF (FCF > P/E > EBIT > EBITDA)
- **7 projection years** (5-year hold + NTM exit window)
- **Driver-based revenue** — never growth rates without decomposition
- **ROIC/ROTIC standard** in every model
- **Single Model View** — IS/BS/CFS each carry one view; the Historicals tabs are the as-reported record
- **Assumptions in-row** — yellow bg + blue text in projection columns of the same row as historicals
- **YEARFRAC calendarization** for entry/exit EPS blending
- **ASC 842 lease-aware** — operating and finance lease schedules on the BS & CFS Build
- **Iterative calculation required** after Phase 5 (circular Capital Allocation waterfall)
- **Balance sheet must balance** — BS Check = 0, CF Check = 0, no plugs ever

## Repository Structure

```
.claude-plugin/
  plugin.json               # fc-model-builder plugin manifest
  marketplace.json          # single-plugin marketplace (root as plugin)
commands/
  build-model.md            # /build-model conductor (Claude Code / Cowork)
skills/
  build-model/              # Core rules + phase router
  firm-formatting/          # Firm-wide formatting standards
  kpi-tracker/              # Quarterly KPI tracker (plugin-model + standalone modes)
  update-model/             # Post-earnings update (capture -> flip tracker -> verify)
  build-model-phase-0/      # Setup
  build-model-phase-1/      # Annual Historicals & Statements
    meth-historicals.md     #   (canonical) capture hierarchy + edge-case protocols
    meth-lease-full.md      #   (canonical) ASC 842 methodology
    meth-ppe-signs.md
  build-model-phase-2/      # Quarterly Historicals & KPI Tracker
  build-model-phase-3/      # Drivers
    meth-revenue.md  meth-cost.md  meth-tax.md          # (canonical)
    meth-ppe-build.md  meth-working-capital.md  meth-debt-build.md
  build-model-phase-4/      # Forward Statements
    meth-goodwill.md  meth-interest-debt.md             # (canonical)
  build-model-phase-5/      # Capital Allocation
    meth-share-count.md
  build-model-phase-6/      # Model Tab (meth-roic.md)
  build-model-phase-7/      # Returns (meth-projection-length.md)
  build-model-phase-8/      # Formatting
  build-model-phase-9/      # QC
  build-model-phase-10/     # Output Tab
  build-model-phase-11/     # Consensus
  build-model-phase-12/     # Source Hygiene
```

Each phase directory contains a `SKILL.md` (entry point) and one or more detailed reference files (`phase-N-*.md`, `meth-*.md`). Files marked canonical are the edit points; copies elsewhere carry sync banners.
