---
name: build-model-phase-06
description: |
  Phase 6 -- Model Tab. Consolidates key outputs onto the Model tab including the Platinum List, summary statements, KPIs, and capital allocation metrics.
  Load after `build-model` and `firm-formatting` are already in context.
---

# Phase 6 -- Model Tab

## Preflight

**PREFLIGHT -- Do not skip. Output this block to the user before any other work.**

```
Phase: 6 (Model Tab)
Foundation skills loaded: build-model [check/x], firm-formatting [check/x]
Task Tracker read: [check/x] -- current phase per tracker: [phase name]
Firm-formatting color check:
  Assumption bg = #FFFF00 [check/x]
  Blue text     = #0000FF [check/x]
  Navy header   = #1C3553 [check/x]
  Subheader     = #C2D5EB [check/x]
  Major total   = #F2F2F2 [check/x]
Phase 5 verification gate (6 checks):
  BS Check = [0 or value]
  CF Check = [0 or value]
  NI Check = [0 or value]
  Waterfall Check = [0 or value]
  CFS End Cash = BS & CFS Build Target: [check/x]
  CFS End Cash = BS Cash: [check/x]
Circular refs resolving (iterative calc): [check/x]
```

Do not proceed until every field is filled. If any x or blank, stop and load/read.

## Phase Instructions

See `phase-6-model-tab.md` for the complete phase reference.

## Methodology References


## After Completing This Phase

1. Run Tab Completion Verification (see phase instructions)
2. Verify Definition of Done (see phase instructions)
3. Clear context (start new conversation)
4. Next phase: load `build-model` + `firm-formatting` + `build-model-phase-07`
