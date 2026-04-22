---
name: build-model-phase-6
description: |
  Phase 6 -- Returns. Builds the Returns tab with entry/exit pricing, XIRR, and multiple-based returns.
  Load after `build-model` and `firm-formatting` are already in context.
---

# Phase 6 -- Returns

## Preflight

**PREFLIGHT -- Do not skip. Output this block to the user before any other work.**

```
Phase: 6 (Returns)
Foundation skills loaded: build-model [check/x], firm-formatting [check/x]
Task Tracker read: [check/x] -- current phase per tracker: [phase name]
Firm-formatting color check:
  Assumption bg = #FFFF00 [check/x]
  Blue text     = #0000FF [check/x]
  Navy header   = #1C3553 [check/x]
  Subheader     = #C2D5EB [check/x]
  Major total   = #F2F2F2 [check/x]
Model Tab sections complete: [count]/8
ROIC/ROTIC populated: [check/x]
```

Do not proceed until every field is filled. If any x or blank, stop and load/read.

## Phase Instructions

See `phase-6-returns.md` for the complete phase reference.

## Methodology References

- `meth-projection-length.md` -- Projection length calculation and historical period guidance

## After Completing This Phase

1. Run Tab Completion Verification (see phase instructions)
2. Verify Definition of Done (see phase instructions)
3. Clear context (start new conversation)
4. Next phase: load `build-model` + `firm-formatting` + `build-model-phase-7`
