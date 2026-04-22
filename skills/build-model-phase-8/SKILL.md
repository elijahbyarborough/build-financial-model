---
name: build-model-phase-8
description: |
  Phase 8 -- QC (Quality Control). Runs quality gates, checks BS balance, CFS reconciliation, and lease integrity.
  Load after `build-model` and `firm-formatting` are already in context.
---

# Phase 8 -- QC (Quality Control)

## Preflight

**PREFLIGHT -- Do not skip. Output this block to the user before any other work.**

```
Phase: 8 (QC)
Foundation skills loaded: build-model [check/x], firm-formatting [check/x]
Task Tracker read: [check/x] -- current phase per tracker: [phase name]
Firm-formatting color check:
  Assumption bg = #FFFF00 [check/x]
  Blue text     = #0000FF [check/x]
  Navy header   = #1C3553 [check/x]
  Subheader     = #C2D5EB [check/x]
  Major total   = #F2F2F2 [check/x]
Formatting applied to all tabs (Phase 7 complete): [check/x]
Tab order matches firm standard: [check/x]
```

Do not proceed until every field is filled. If any x or blank, stop and load/read.

## Phase Instructions

See `phase-8-qc.md` for the complete phase reference.

## After Completing This Phase

1. Verify Definition of Done (see phase instructions)
2. Clear context (start new conversation)
3. Next phase: load `build-model` + `firm-formatting` + `build-model-phase-9`
