---
name: build-model-phase-11
description: |
  Phase 11 -- Hardcode Sources. Replaces all live data-feed formulas with hardcoded values for portability.
  Load after `build-model` and `firm-formatting` are already in context.
---

# Phase 11 -- Hardcode Sources

## Preflight

**PREFLIGHT -- Do not skip. Output this block to the user before any other work.**

```
Phase: 11 (Hardcode Sources)
Foundation skills loaded: build-model [check/x], firm-formatting [check/x]
Task Tracker read: [check/x] -- current phase per tracker: [phase name]
Firm-formatting color check:
  Assumption bg = #FFFF00 [check/x]
  Blue text     = #0000FF [check/x]
  Navy header   = #1C3553 [check/x]
  Subheader     = #C2D5EB [check/x]
  Major total   = #F2F2F2 [check/x]
Consensus tab complete: [check/x]
Delta formulas verified (% for $, bps for margins, turns for multiples): [check/x]
```

Do not proceed until every field is filled. If any x or blank, stop and load/read.

## Phase Instructions

See `phase-11-hardcode.md` for the complete phase reference.

## After Completing This Phase

1. Verify Definition of Done (see phase instructions)
2. This is the final phase -- model build is complete
