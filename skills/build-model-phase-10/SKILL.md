---
name: build-model-phase-9
description: |
  Phase 9 -- Output Tab. Builds the Output tab summarizing key metrics, returns, and valuation for review.
  Load after `build-model` and `firm-formatting` are already in context.
---

# Phase 9 -- Output Tab

## Preflight

**PREFLIGHT -- Do not skip. Output this block to the user before any other work.**

```
Phase: 9 (Output Tab)
Foundation skills loaded: build-model [check/x], firm-formatting [check/x]
Task Tracker read: [check/x] -- current phase per tracker: [phase name]
Firm-formatting color check:
  Assumption bg = #FFFF00 [check/x]
  Blue text     = #0000FF [check/x]
  Navy header   = #1C3553 [check/x]
  Subheader     = #C2D5EB [check/x]
  Major total   = #F2F2F2 [check/x]
QC gates: Gate 1 = [PASS/FAIL], Gate 2 = [PASS/FAIL], Gate 3 = [PASS/FAIL], Gate 4 = [PASS/FAIL]
Open CRITICAL findings: [count]
```

Do not proceed until every field is filled. If any x or blank or FAIL, stop and resolve.

## Phase Instructions

See `phase-9-output.md` for the complete phase reference.

## After Completing This Phase

1. Run Tab Completion Verification (see phase instructions)
2. Verify Definition of Done (see phase instructions)
3. Clear context (start new conversation)
4. Next phase: load `build-model` + `firm-formatting` + `build-model-phase-10`
