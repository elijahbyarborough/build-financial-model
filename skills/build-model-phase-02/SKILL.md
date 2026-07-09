---
name: build-model-phase-02
description: "Phase 2 -- Quarterly Historicals & KPI Tracker. Capture everything the company reports quarterly (16+ quarters, full IS/BS/CFS faces plus all supplemental sections) on the Quarterly Historicals tab, then build the quarterly KPI Tracker tab from it. Loads the kpi-tracker skill for the tracker build. Load after build-model and firm-formatting."
---

# Phase 2 -- Quarterly Historicals & KPI Tracker

## Preflight

**PREFLIGHT -- Do not skip. Output this block to the user before any other work.**

```
Phase: 2 (Quarterly Historicals & KPI Tracker)
Foundation skills loaded: build-model [check/x], firm-formatting [check/x]
kpi-tracker skill loaded: [check/x]
Task Tracker read: [check/x] -- current phase per tracker: [phase name]
Firm-formatting color check:
  Assumption bg = #FFFF00 [check/x]
  Blue text     = #0000FF [check/x]
  Navy header   = #1C3553 [check/x]
  Subheader     = #C2D5EB [check/x]
  Major total   = #F2F2F2 [check/x]
Phase 1 complete: [check/x]
  Annual Historicals populated (sections present): [list sections]
  IS/BS/CFS Model View historicals built: [check/x]
  BS Check = [value] (must be 0 every historical period)
  CF Check = [value] (must be 0 every historical period)
  Statement recon checks vs Annual Historicals = 0: [check/x]
```

Do not proceed until every field is filled. If any x or blank, stop and load/read.

This phase loads ONE skill beyond the foundations and this skill: the **`kpi-tracker`**
skill (in this plugin), which owns the KPI Tracker tab's construction spec. Load it before
starting Step 2.

## Phase Instructions

See `phase-2-quarterly-kpi.md` for the complete phase reference.

## Methodology References

- `meth-historicals.md` -- Canonical Historicals hierarchy (13 sections), capture rules, and edge-case protocols (segment recasts, ASC adoptions, FYE changes, restatements)
- `kpi-tracker` skill (separate skill in this plugin) -- KPI Tracker tab: KPI selection lenses, column structure, FY Derivation Matrix, formatting spec

## After Completing This Phase

1. Run Tab Completion Verification for Quarterly Historicals + KPI Tracker (see phase instructions)
2. Verify Definition of Done (see phase instructions)
3. Clear context (start new conversation)
4. Next phase: load `build-model` + `firm-formatting` + `build-model-phase-03`
