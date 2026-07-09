# Phase 12 — Source Hygiene

**Prerequisite:** Phase 11 complete (or skipped if no consensus data). All model content finalized. This is the final phase.

---

## What Happens in This Phase

In the v2 architecture, reported data is hardcoded AT CAPTURE on the Annual/Quarterly Historicals tabs — so the model is self-contained by construction, and there is no mass hardcoding job. This phase is a final hygiene pass:

1. **Scan** every model tab for stray live references to source/reference tabs
2. **Freeze** any found — replace the formula with its value, with provenance preserved
3. **Verify** the single-hardcode-layer rule holds across the whole model
4. **Verify** the Data Pull snapshot discipline
5. **Re-verify** all integrity checks

In a clean v2 build, Step 1 should find ZERO stray references — every hit is a capture-discipline miss worth logging to the Task Tracker with the responsible phase.

---

## What Gets Scanned

### Target Tabs (scan all of these)

- IS, BS, CFS (historical columns)
- Profit Build (historical cells)
- BS & CFS Build (historical cells, including lease schedule historicals)
- Capital Allocation Build (historical cells)
- Model, Returns, Output, KPI Tracker
- Annual Historicals and Quarterly Historicals (should contain hardcodes + intra-tab formulas ONLY — any live source-tab reference here is a capture-discipline miss)

### Target Formulas (freeze these)

Any formula that references a **source data tab**, including:

- `='Tegus'!C15` → freeze to value
- `='BAMSEC'!D20` → freeze to value
- `='BAMSEC - 10-K'!E30` → freeze to value
- `='GS'!F12` or `='GS Model'!G8` → freeze to value
- `='Old Model'!B10` → freeze to value
- Any other reference to a tab identified as a source/reference tab (gray #7C7F88 tab color)

### What Does NOT Get Touched

- **Cross-tab model references**: `='Profit Build'!D10`, `='BS & CFS Build'!E15`, `=IS!C20`, `='Model Tab'!F30`, `='Annual Historicals'!G12`, `='Quarterly Historicals'!H40` — these are live model linkages and must stay as formulas
- **The Historicals tabs' hardcodes**: Annual/Quarterly Historicals ARE the capture layer — their blue hardcoded cells are correct by design
- **Projection formulas**: anything in projection columns (driver-based, not source-linked)
- **Derived calculations**: `=SUM()`, `=IF()`, margin calculations, growth rates, tie-out rows, etc.
- **Data Pull and Data Pull (Values) tabs**: these have their own update process; leave them as-is
- **Consensus data source tab**: leave as-is (the Consensus tab needs it)

### How to Identify Source Tabs

Source tabs are identified by:
1. **Tab color**: Gray (#7C7F88) — all source/reference tabs use this color
2. **Tab name**: Contains "BAMSEC", "Tegus", "GS", or "Old Model"
3. **Position**: Typically rightmost tabs in the workbook

When in doubt, read the tab — if it contains raw imported data (not model formulas), it's a source tab.

---

## Execution Steps

### Step 1: Identify All Source Tabs

Read the workbook tab list. Flag every tab that matches the source tab criteria above. Report the list to the user for confirmation before proceeding.

### Step 2: Scan for Stray Source References

For each target tab:
1. Read every cell with a formula
2. Check if the formula references any identified source tab
3. Build a list: `[Tab, Cell, Current Formula, Current Value]`

Report the count: "Found X stray source references across Y tabs." (Expected: zero in a clean build.) Log every hit to the Task Tracker with the phase responsible for that tab.

### Step 3: Freeze Stray References

For each identified cell:
1. Record the current computed value
2. Replace the formula with the hardcoded value
3. Change the font color to **blue (#0000FF)** — marks it as a hardcoded input per firm-formatting color coding
4. Add the registry tag comment: `Frozen: Phase 12 [date], from [source tab]![cell]` — this registers the cell in the Hardcode Registry so Gate 5 re-runs and future audits never flag it
5. **Do NOT add yellow background** — yellow bg is reserved for assumption cells (projection drivers). Historical hardcodes get blue text only.

**Better fix, when practical:** if the value already exists on the Annual/Quarterly Historicals tabs, re-point the formula there (green cross-sheet reference) instead of freezing — that restores the single-hardcode-layer architecture rather than adding a second hardcode site.

### Step 4: Verify the Single-Hardcode-Layer Rule

Scan the historical columns of IS, BS, CFS, Profit Build, BS & CFS Build, and Capital Allocation Build for blue-font (#0000FF) hardcoded cells. Outside the Annual/Quarterly Historicals tabs, hardcoded historicals must number ZERO (excluding any Step 3 freezes just made and reported). Report the scan scope and result. This repeats the Phase 9 Gate 5 audit as a final backstop.

### Step 5: Verify Data Pull Discipline

1. The model references only **Data Pull (Values)** — never the live Data Pull tab
2. The Data Pull (Values) snapshot is date-stamped and current
3. No live `SPG()` formulas exist outside the Data Pull tab

### Step 6: Verify Integrity

After any freezes:
1. **BS Check = 0** across all periods
2. **CF Check = 0** across all periods
3. **Reconciliation checks = 0** — Model NI / Total Assets / End Cash each match the Annual Historicals reported values
4. **All projection formulas still work** — projections should be unaffected since they reference build tabs, not source tabs

If any check fails, a freeze introduced an error. Undo and investigate.

### Step 7: Report

Report to the user:
- Stray references found, frozen, re-pointed, and flagged
- Single-hardcode-layer audit result
- Data Pull discipline result
- Integrity check results (all should pass)
- Recommendation: source tabs can now be archived or removed at the user's discretion (do NOT delete them automatically)

---

## What Happens to Source Tabs After This Phase

**Nothing — this phase does not delete or modify source tabs.** The source tabs remain in the workbook as an audit trail. The user can choose to:

1. **Keep them** — for reference and future re-verification
2. **Archive them** — move to an "Archive" tab group
3. **Delete them** — if the model needs to be portable/lightweight

This is the user's decision, not the skill's.

---

## Edge Cases

### Formulas That Reference Multiple Source Tabs

```
=IF('Tegus'!D10 > 0, 'Tegus'!D10, 'BAMSEC'!E10)
```

Evaluate the entire formula to get the final value, then freeze it. Do not try to partially hardcode.

### SUM Formulas Over Source Ranges

`=SUM('Tegus'!D10:D15)` — replace with the computed sum value, not the individual cells.

### Formulas with Mixed References

If a cell references both source data and model data:
```
='Profit Build'!D10 + 'Tegus'!D15
```

This is unusual and likely a modeling error. Flag it for the user rather than freezing silently.

---

## Formatting After Freezing

| Cell Type | Font Color | Background | Notes |
|-----------|-----------|------------|-------|
| Frozen historical value (was source reference) | Blue (#0000FF) | None (white) | Standard hardcode color per firm-formatting; comment MUST carry the tag `Frozen: Phase 12 [date], from [source tab]![cell]` — this registers the cell in the Hardcode Registry so Gate 5 re-runs never flag it |
| Hardcoded assumption (projection driver) | Blue (#0000FF) | Yellow (#FFFF00) | Unchanged — these were already blue/yellow |
| Live formula | Black (#212529) | None | Unchanged |
| Cross-sheet model reference | Green (#008000) | None | Unchanged |

The key distinction: historical hardcodes get **blue text only** (no yellow background). Yellow background is reserved for assumption cells that the analyst actively sets and updates.

---

## Key Rules

- **Only freeze source-tab references** — never touch cross-tab model formulas or the Historicals tabs' capture hardcodes.
- **Prefer re-pointing to the Historicals tabs** over freezing, when the value exists there.
- **Blue font (#0000FF)** on every frozen cell, with a source comment.
- **No yellow background** on historical hardcodes — yellow is for projection assumptions only.
- **Verify all integrity checks** after freezing — BS Check, CF Check, reconciliation.
- **Do not delete source tabs** — that's the user's decision.
- **Report before and after** — list what will be frozen, then confirm results.

---

## Definition of Done (Phase 12)

A phase is complete if and only if ALL of the following are true. Report completion by reading these values back to the user -- not by summarizing in prose.

1. **Task Tracker**: Every subtask row for Phase 12 shows Status = "COMPLETE". Cite the actual cell addresses you checked.
2. **Stray source references resolved**: report counts found / frozen / re-pointed / flagged. Zero live source-tab references remain outside the Data Pull tabs and the consensus data source.
3. **Single-hardcode-layer audit passed**: zero hardcoded historicals outside the Annual/Quarterly Historicals tabs other than Hardcode Registry-sanctioned cells (including this phase's `Frozen: Phase 12`-tagged freezes). Report the scan scope, count, and the number of tagged freezes.
4. **Data Pull discipline verified**: model references only Data Pull (Values); snapshot date-stamped; no stray live SPG() formulas.
5. **BS Check = 0** across all periods. Read and report.
6. **CF Check = 0** across all periods. Read and report.
7. **Reconciliation checks = 0** (Model NI / Total Assets / End Cash vs Annual Historicals reported).
8. **All projection formulas still work** (projections unaffected).
9. **Source tabs preserved** (not deleted -- user's decision).
10. **Task Tracker Model State Block**: "Last Skill Run" = "build-model-phase-12" + today's date.

If you write "Phase 12 complete" or "Model build complete" in chat before reading and reporting these values, you have made an error. Re-verify and correct.

**STOP. Report results. Model build is complete.**
