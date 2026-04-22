---
name: build-model-phase-1
description: "Phase 1 -- Historical Statements. Build historical IS, BS, CFS with model view and reported view. Includes lease schedule setup and WC Build disaggregation. Load after build-model and firm-formatting."
---

# Phase 1 -- Historical Statements

## Preflight

**PREFLIGHT -- Do not skip. Output this block to the user before any other work.**

```
Phase: 1 (Historical Statements)
Foundation skills loaded: build-model [check/x], firm-formatting [check/x]
Task Tracker read: [check/x] -- current phase per tracker: [phase name]
Firm-formatting color check:
  Assumption bg = #FFFF00 [check/x]
  Blue text     = #0000FF [check/x]
  Navy header   = #1C3553 [check/x]
  Subheader     = #C2D5EB [check/x]
  Major total   = #F2F2F2 [check/x]
Lease flags from Phase 0:
  HAS_OPERATING_LEASES = [Y/N]
  HAS_FINANCE_LEASES   = [Y/N]
  FL_IN_PPE            = [Y/N]
  LEASE_MATERIALITY    = [HIGH/LOW]
```

Do not proceed until every field is filled. If any x or blank, stop and load/read.

## Phase Instructions

See `phase-1-historical-statements.md` for the complete phase reference.

## Methodology References

- `meth-lease-full.md` -- Full lease accounting (ASC 842): detection, classification, Lease BS Map, schedules, CF rules, WC disaggregation
- `meth-working-capital.md` -- Working capital 4-step discovery protocol, driver types, common WC items
- `meth-ppe-signs.md` -- PP&E Build sign conventions (asset perspective, CFS sign flip)

## After Completing This Phase

1. Run Tab Completion Verification (see phase instructions)
2. Verify Definition of Done (see phase instructions)
3. Clear context (start new conversation)
4. Next phase: load `build-model` + `firm-formatting` + `build-model-phase-2`
