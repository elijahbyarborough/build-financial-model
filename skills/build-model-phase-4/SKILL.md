---
name: build-model-phase-4
description: "Phase 4 -- Forward Statements. Build all forward projections (IS/BS/CFS) off the Profit Build and BS & CFS Build: lease schedules, PP&E, WC, debt, tax. Includes full methodology for leases, PP&E, WC, tax, interest/debt, and goodwill. Load after build-model and firm-formatting."
---

# Phase 4 -- Forward Statements

## Preflight

**PREFLIGHT -- Do not skip. Output this block to the user before any other work.**

```
Phase: 4 (Forward Statements)
Foundation skills loaded: build-model [check/x], firm-formatting [check/x]
Task Tracker read: [check/x] -- current phase per tracker: [phase name]
Firm-formatting color check:
  Assumption bg = #FFFF00 [check/x]
  Blue text     = #0000FF [check/x]
  Navy header   = #1C3553 [check/x]
  Subheader     = #C2D5EB [check/x]
  Major total   = #F2F2F2 [check/x]
Phase 3 driver review complete: [check/x]
  LOW/MEDIUM confidence drivers resolved: [check/x]
Both build tabs populated with historicals + drivers: Profit Build [check/x], BS & CFS Build [check/x]
```

Do not proceed until every field is filled. If any x or blank, stop and load/read.

## Phase Instructions

See `phase-4-forward-statements.md` for the complete phase reference.

## Methodology References

- `meth-lease-full.md` -- Full lease accounting (ASC 842): detection, BS Map, OL/FL schedules, CF rules, WC disaggregation
- `meth-ppe-build.md` -- PP&E & Capex section: 3-part structure, roll-forward, FL adjustments
- `meth-working-capital.md` -- Working Capital section: 4-step discovery, driver types, methodology
- `meth-tax.md` -- Tax section: effective tax rate, NOL companies, tax outputs
- `meth-interest-debt.md` -- Interest expense & debt: tranche-by-tranche build, cash & equivalents
- `meth-goodwill.md` -- Goodwill & intangibles: carry-forward, amortization, M&A Assets

## After Completing This Phase

1. Run Tab Completion Verification (see phase instructions)
2. Verify Definition of Done (see phase instructions)
3. Clear context (start new conversation)
4. Next phase: load `build-model` + `firm-formatting` + `build-model-phase-5`
