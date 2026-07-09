---
name: build-model-phase-04
description: "Phase 4 -- Forward Statements. Assemble the projected IS/BS/CFS (Model View) from the completed build tabs -- verify, don't rebuild (Phase 3 owns all build-tab projections). Covers statement wiring, lease CF rules, equity roll, placeholders, and the EBT wiring check. Load after build-model and firm-formatting."
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
- `meth-interest-debt.md` -- Interest expense & debt: aggregate default vs tranche escalation, interest income, cash target + BS cash wiring
- `meth-goodwill.md` (canonical in build-model-phase-03) -- Goodwill & intangibles: carry-forward, amortization, M&A Assets

## After Completing This Phase

1. Run Tab Completion Verification (see phase instructions)
2. Verify Definition of Done (see phase instructions)
3. Clear context (start new conversation)
4. Next phase: load `build-model` + `firm-formatting` + `build-model-phase-05`
