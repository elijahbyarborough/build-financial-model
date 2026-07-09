---
name: build-model-phase-2
description: "Phase 2 -- Drivers. Populate all build tabs with historical data and driver assumptions. Includes PP&E, WC, Tax, Revenue, and Cost methodology. Load after build-model and firm-formatting."
---

# Phase 2 -- Drivers

## Preflight

**PREFLIGHT -- Do not skip. Output this block to the user before any other work.**

```
Phase: 2 (Drivers)
Foundation skills loaded: build-model [check/x], firm-formatting [check/x]
Task Tracker read: [check/x] -- current phase per tracker: [phase name]
Firm-formatting color check:
  Assumption bg = #FFFF00 [check/x]
  Blue text     = #0000FF [check/x]
  Navy header   = #1C3553 [check/x]
  Subheader     = #C2D5EB [check/x]
  Major total   = #F2F2F2 [check/x]
Phase 1 integrity checks:
  BS Check = [0 or value]
  CF Check = [0 or value]
BS-to-CF mapping location: [tab!range]
```

Do not proceed until every field is filled. If any x or blank, stop and load/read.

## Phase Instructions

See `phase-2-drivers.md` for the complete phase reference.

## Methodology References

- `meth-ppe-build.md` -- PP&E Build: 3-section structure, sign conventions, roll-forward
- `meth-working-capital.md` -- Working Capital: 4-step discovery, driver types, common items
- `meth-tax.md` -- Tax: effective tax rate, NOL companies, DTA/DTL
- `meth-revenue.md` -- Revenue Build: approach hierarchy, driver-based rules
- `meth-cost.md` -- Cost Build: operating expenses methodology
- `meth-debt-build.md` -- Debt Build: canonical 6-section structure, lease schedules

## After Completing This Phase

1. Run Tab Completion Verification (see phase instructions)
2. Verify Definition of Done (see phase instructions)
3. Clear context (start new conversation)
4. Next phase: load `build-model` + `firm-formatting` + `build-model-phase-3`
