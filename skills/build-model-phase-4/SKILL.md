---
name: build-model-phase-4
description: |
  Phase 4 -- Capital Allocation. Builds the capital allocation waterfall, debt build, share count, and buyback plug.
  Load after `build-model` and `firm-formatting` are already in context.
---

# Phase 4 -- Capital Allocation

## Preflight

**PREFLIGHT -- Do not skip. Output this block to the user before any other work.**

```
Phase: 4 (Capital Allocation)
Foundation skills loaded: build-model [check/x], firm-formatting [check/x]
Task Tracker read: [check/x] -- current phase per tracker: [phase name]
Firm-formatting color check:
  Assumption bg = #FFFF00 [check/x]
  Blue text     = #0000FF [check/x]
  Navy header   = #1C3553 [check/x]
  Subheader     = #C2D5EB [check/x]
  Major total   = #F2F2F2 [check/x]
Iterative calculation enabled in Excel: [check/x]
Phase 3 integrity checks:
  BS Check = [0 or value]
  CF Check = [0 or value]
Capital allocation placeholders in place on CFS: [check/x]
```

Do not proceed until every field is filled. If any x or blank, stop and load/read.

## Phase Instructions

See `phase-4-capital-allocation.md` for the complete phase reference.

## Methodology References

- `meth-interest-debt.md` -- Interest expense, debt build, cash & equivalents
- `meth-share-count.md` -- Share count logic, diluted shares via treasury stock method

## After Completing This Phase

1. Run Tab Completion Verification (see phase instructions)
2. Verify Definition of Done (see phase instructions)
3. Clear context (start new conversation)
4. Next phase: load `build-model` + `firm-formatting` + `build-model-phase-5`
