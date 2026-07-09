---
name: build-model-phase-3
description: "Phase 3 -- Drivers. Populate the two driver tabs (Profit Build, BS & CFS Build) with historicals linked to Annual Historicals, driver assumptions, and build-tab projections. Includes revenue, cost, tax, PP&E, WC, and debt/lease methodology. Load after build-model and firm-formatting."
---

# Phase 3 -- Drivers

## Preflight

**PREFLIGHT -- Do not skip. Output this block to the user before any other work.**

```
Phase: 3 (Drivers)
Foundation skills loaded: build-model [check/x], firm-formatting [check/x]
Task Tracker read: [check/x] -- current phase per tracker: [phase name]
Firm-formatting color check:
  Assumption bg = #FFFF00 [check/x]
  Blue text     = #0000FF [check/x]
  Navy header   = #1C3553 [check/x]
  Subheader     = #C2D5EB [check/x]
  Major total   = #F2F2F2 [check/x]
Phase 1 integrity checks:
  BS Check = [0 or value]
  CF Check = [0 or value]
Phase 2 checks:
  Quarterly tie-outs (sum Q1-Q4 = FY) = [pass/fail]
  KPI Tracker built = [check/x]
BS-to-CF mapping location: [tab!range]
```

Do not proceed until every field is filled. If any x or blank, stop and load/read.

## Phase Instructions

See `phase-3-drivers.md` for the complete phase reference, including the mandatory cross-tab build order (Profit Build revenue/costs → BS & CFS Build all sections → Profit Build tax).

## Methodology References

- `meth-revenue.md` -- Profit Build revenue sections: approach hierarchy, driver-based rules
- `meth-cost.md` -- Profit Build cost sections: operating expenses methodology
- `meth-tax.md` -- Profit Build tax section: effective tax rate, NOL companies, DTA/DTL, memo EBT
- `meth-ppe-build.md` -- BS & CFS Build PP&E & Capex section: 3-block structure, sign conventions, roll-forward
- `meth-working-capital.md` -- BS & CFS Build Working Capital section: 4-step discovery, driver types, denominator consistency, ex-lease disaggregation
- `meth-debt-build.md` -- BS & CFS Build Debt & Cash section + lease schedules: canonical structure, cash target balance

## After Completing This Phase

1. Run Tab Completion Verification (see phase instructions)
2. Verify Definition of Done (see phase instructions)
3. Clear context (start new conversation)
4. Next phase: load `build-model` + `firm-formatting` + `build-model-phase-4`
