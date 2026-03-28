# Phase 11 — Hardcode Sources

**Reference:** `references/phase-11-hardcode.md`
**Prerequisite:** Phase 10 complete (or skipped if no consensus data). All model content finalized.
**This is the final phase.**

---

## What Happens in This Phase

Convert all source-tab references across the model into hardcoded values. This makes the model self-contained and portable — it no longer depends on BAMSEC, Tegus, broker model, or Historical Data source tabs to function.

1. **Scan** every build tab and statement tab for formulas referencing source data tabs
2. **Evaluate** each formula to get the current value
3. **Replace** the formula with the hardcoded value
4. **Format** the hardcoded cell: blue font (#0000FF) per firm-formatting color coding for hardcodes
5. **Preserve** cross-tab references between model tabs (these stay as live formulas)
6. **Verify** all integrity checks still pass after hardcoding

---

## What Gets Hardcoded

### Target Tabs (scan all of these)

- IS (Reported View historical cells)
- BS (Reported View historical cells)
- CFS (Reported View historical cells)
- Revenue Build (historical cells)
- Costs Build (historical cells)
- PP&E Build (historical cells)
- Debt Build (historical cells, including lease schedule historicals)
- Working Capital Build (historical cells)
- Tax Schedule (historical cells)
- Capital Allocation Build (historical cells)
- Historical Data tab (if it exists — this tab IS already hardcoded by design, but verify)

### Target Formulas (replace these)

Any formula that references a **source data tab**, including:

- `='Tegus'!C15` → hardcode the value
- `='BAMSEC'!D20` → hardcode the value
- `='BAMSEC - 10-K'!E30` → hardcode the value
- `='GS'!F12` or `='GS Model'!G8` → hardcode the value
- `='Historical Data'!H25` → hardcode the value
- `='Old Model'!B10` → hardcode the value
- Any other reference to a tab identified as a source/reference tab (gray #7C7F88 tab color)

### What Does NOT Get Hardcoded

- **Cross-tab model references**: `='Revenue Build'!D10`, `='Debt Build'!E15`, `=IS!C20`, `='Model Tab'!F30` — these are live model linkages and must stay as formulas
- **Projection formulas**: anything in projection columns (these are driver-based, not source-linked)
- **Derived calculations**: `=SUM()`, `=IF()`, margin calculations, growth rates, etc.
- **Data Pull and Data Pull (Values) tabs**: these are reference tabs with their own update process; leave them as-is
- **Consensus data source tab**: leave as-is (the Consensus tab needs it)

### How to Identify Source Tabs

Source tabs are identified by:
1. **Tab color**: Gray (#7C7F88) — all source/reference tabs use this color
2. **Tab name**: Contains "BAMSEC", "Tegus", "GS", "Old Model", or is named "Historical Data"
3. **Position**: Typically rightmost tabs in the workbook

When in doubt, read the tab — if it contains raw imported data (not model formulas), it's a source tab.

---

## Execution Steps

### Step 1: Identify All Source Tabs

Read the workbook tab list. Flag every tab that matches the source tab criteria above. Report the list to the user for confirmation before proceeding.

### Step 2: Scan for Source References

For each target tab (IS, BS, CFS, all build tabs):
1. Read every cell with a formula
2. Check if the formula references any identified source tab
3. Build a list: `[Tab, Cell, Current Formula, Current Value]`

Report the count: "Found X source references across Y tabs."

### Step 3: Replace Formulas with Values

For each identified cell:
1. Record the current computed value
2. Replace the formula with the hardcoded value
3. Change the font color to **blue (#0000FF)** — this marks it as a hardcoded input per firm-formatting color coding
4. **Do NOT add yellow background** — yellow bg is reserved for assumption cells (projection drivers). Historical hardcodes get blue text only.

### Step 4: Verify Integrity

After all replacements:
1. **BS Check = 0** across all historical periods
2. **CF Check = 0** across all historical periods
3. **Reconciliation checks = 0** (Model View vs Reported View on IS/BS/CFS)
4. **All projection formulas still work** — projections should be unaffected since they reference build tabs, not source tabs

If any check fails, a replacement introduced an error. Undo and investigate.

### Step 5: Report

Report to the user:
- Total cells hardcoded
- Tabs affected
- Integrity check results (all should pass)
- Recommendation: source tabs can now be archived or removed at the user's discretion (do NOT delete them automatically)

---

## What Happens to Source Tabs After Hardcoding

**Nothing — this phase does not delete or modify source tabs.** The source tabs remain in the workbook as an audit trail. The user can choose to:

1. **Keep them** — for reference and future re-verification
2. **Archive them** — move to an "Archive" tab group
3. **Delete them** — if the model needs to be portable/lightweight

This is the user's decision, not the skill's.

---

## Edge Cases

### Formulas That Reference Multiple Tabs

Some formulas may reference both a source tab and a model tab:
```
=IF('Tegus'!D10 > 0, 'Tegus'!D10, 'BAMSEC'!E10)
```

For these, evaluate the entire formula to get the final value, then hardcode it. Do not try to partially hardcode.

### SUM Formulas Over Source Ranges

Some historical totals may be `=SUM('Tegus'!D10:D15)`. Replace with the computed sum value, not the individual cells.

### Formulas with Mixed References

If a cell references both source data and model data:
```
='Revenue Build'!D10 + 'Tegus'!D15
```

This is unusual and likely a modeling error. Flag it for the user rather than hardcoding silently.

---

## Formatting After Hardcode

| Cell Type | Font Color | Background | Notes |
|-----------|-----------|------------|-------|
| Hardcoded historical value (was source reference) | Blue (#0000FF) | None (white) | Standard hardcode color per firm-formatting |
| Hardcoded assumption (projection driver) | Blue (#0000FF) | Yellow (#FFFF00) | Unchanged — these were already blue/yellow |
| Live formula | Black (#212529) | None | Unchanged |
| Cross-sheet model reference | Green (#008000) | None | Unchanged |

The key distinction: historical hardcodes get **blue text only** (no yellow background). Yellow background is reserved for assumption cells that the analyst actively sets and updates.

---

## Key Rules

- **Only hardcode source-tab references** — never touch cross-tab model formulas.
- **Blue font (#0000FF)** on every hardcoded cell — marks it as a hardcoded input.
- **No yellow background** on historical hardcodes — yellow is for projection assumptions only.
- **Verify all integrity checks** after hardcoding — BS Check, CF Check, reconciliation.
- **Do not delete source tabs** — that's the user's decision.
- **Report before and after** — list what will be hardcoded, then confirm results.

**STOP. Update Task Tracker. Report hardcode results. Model build is complete.**
