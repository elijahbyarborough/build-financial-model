---
name: build-model-phase-3
description: "Phase 3 -- Forward Statements. Build all forward projections (IS/BS/CFS), lease schedules, PP&E, WC, debt, tax. Includes full methodology for leases, PP&E, WC, tax, interest/debt, and goodwill. Load after build-model and firm-formatting."
---

# Phase 3 -- Forward Statements

## Preamble

Load `build-model` + `firm-formatting` first, then read the Task Tracker before starting work.

## Methodology Files Included

This skill bundles the following methodology excerpts (copied verbatim from `references/methodology.md`):

- `meth-lease-full.md` -- Full lease accounting (ASC 842): Detection, BS Map, OL Schedule, FL Schedule, FL_IN_PPE, CF Rules, WC Disaggregation, Build Order
- `meth-ppe-build.md` -- PP&E Build: sign convention, roll-forward, capex, depreciation, acquisitions, disposals, reference metrics
- `meth-working-capital.md` -- Working Capital: 4-step discovery protocol, driver types, common items, methodology
- `meth-tax.md` -- Tax: effective tax rate, NOL companies, tax schedule outputs
- `meth-interest-debt.md` -- Interest Expense & Debt: tranche-by-tranche debt build, interest rate assumptions, cash & equivalents
- `meth-goodwill.md` -- Goodwill & Intangibles: goodwill carry-forward, intangible amortization schedule, M&A Assets BS row

Also includes the full phase instructions in `phase-3-forward-statements.md`.

## Instructions

Read all methodology files and the phase instructions before building. Follow the build order specified in the phase instructions (adjusted for lease flags on the Task Tracker).

## After Completing

1. Update the Task Tracker with phase completion status and all check results.
2. Clear context -- this phase is complete.
3. Next phase is Phase 4 (Capital Allocation).
