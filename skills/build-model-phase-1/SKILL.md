---
name: build-model-phase-1
description: "Phase 1 -- Annual Historicals & Statements. Capture everything the company reports annually onto the Annual Historicals tab (the single hardcode layer), then build IS, BS, CFS as Model View only, linking to it. Includes lease schedule setup on the BS & CFS Build. Load after build-model and firm-formatting."
---

# Phase 1 -- Annual Historicals & Statements

## Preflight

**PREFLIGHT -- Do not skip. Output this block to the user before any other work.**

```
Phase: 1 (Annual Historicals & Statements)
Foundation skills loaded: build-model [check/x], firm-formatting [check/x]
Task Tracker read: [check/x] -- current phase per tracker: [phase name]
Phase 0 complete per tracker: [check/x]
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
Capture source selected (most exhaustive): [source tab name]
```

Do not proceed until every field is filled. If any x or blank, stop and load/read.

## Phase Instructions

See `phase-1-annual-historicals.md` for the complete phase reference.

## Methodology References

- `meth-historicals.md` -- CANONICAL. Historicals capture: 13-section hierarchy, single-hardcode-layer and capture-everything rules, quarterly mechanics, edge-case protocols (segment recast, ASC adoption, FYE change, restatement, disc ops, 52/53-week), Change Log
- `meth-lease-full.md` -- CANONICAL. Full lease accounting (ASC 842): detection, classification, Lease BS Map, schedules on the BS & CFS Build, CF rules, working capital disaggregation
- `meth-ppe-signs.md` -- PP&E & Capex sign conventions (asset perspective, CFS sign flip)

## After Completing This Phase

1. Run Tab Completion Verification (see phase instructions)
2. Verify Definition of Done (see phase instructions)
3. Clear context (start new conversation)
4. Next phase: load `build-model` + `firm-formatting` + `build-model-phase-2` (Quarterly Historicals & KPI Tracker; also loads `kpi-tracker`)
