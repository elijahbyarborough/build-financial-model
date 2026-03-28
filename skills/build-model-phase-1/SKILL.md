---
name: build-model-phase-1
description: "Phase 1 -- Historical Statements. Build historical IS, BS, CFS with model view and reported view. Includes lease schedule setup and WC Build disaggregation. Load after build-model and firm-formatting."
---

# Phase 1 — Historical Statements

## Preamble

Before starting Phase 1 work, load the following skills first:
- **build-model** — core model architecture, tab structure, and conventions
- **firm-formatting** — formatting standards for all model tabs

Then read the **Task Tracker** to understand the current model state, completed phases, and any flags set during Phase 0 (especially lease flags: `HAS_OPERATING_LEASES`, `HAS_FINANCE_LEASES`, `LEASE_MATERIALITY`, `FL_IN_PPE`).

## Instructions

Follow the detailed steps in `phase-1-historical-statements.md` to build historical IS, BS, and CFS statements with both Reported View and Model View. Ensure all integrity checks pass before completing this phase.

### Methodology References (included in this skill)

- **meth-lease-full.md** — Full lease accounting methodology (ASC 842): detection & classification, Lease BS Map, operating lease schedule, finance lease schedule, FL_IN_PPE placement logic, cash flow rules, Working Capital Build disaggregation, and build order.
- **meth-working-capital.md** — Working capital 4-step discovery protocol, driver types table, common WC items, and projection methodology.
- **meth-ppe-signs.md** — PP&E Build sign conventions (asset perspective for capex, D&A, acquisitions, disposals, and the CFS tab sign flip).

## After Completing Phase 1

1. **Update the Task Tracker** — mark Phase 1 as complete, record BS→CF mapping, record all integrity check results, and note any historical reconciliation differences.
2. **Clear context** — Phase 1 context can be unloaded after completion.
3. **Next phase** — Phase 2 (Drivers). Load `build-model-phase-2` skill to continue.
