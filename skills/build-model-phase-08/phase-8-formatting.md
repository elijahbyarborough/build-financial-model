# Phase 8 — Formatting

**Prerequisite:** Phase 7 complete (Returns tab built, all model content finalized).

---

## What Happens in This Phase

Apply FC visual hierarchy, number formats, and color coding across every tab.

The `firm-formatting` skill is the single source of truth for all formatting. It must be loaded for the complete spec.

---

## Key Reminders

- **5-tier visual hierarchy**: Section Headers (navy), Subheaders (light blue), Year Rows, Subtotals (thin top border), Major Totals (gray fill + thin top border).
- **Assumption cells**: yellow bg (#FFFF00) + blue text (#0000FF).
- **SPG() cells**: red text (#FF0000).
- **Cross-sheet references**: green text (#008000).
- **Number format with _) padding**.
- **Freeze panes per the Freeze Pane Standard** — grid tabs at `B5`, Model Tab and KPI Tracker at `B6`, summary tabs (Task Tracker, Output, Consensus, Returns) unfrozen. Gridlines off. No merged cells.
- **Uniform column widths** across year columns.

Format every tab. No tab is exempt. Two tabs keep their own layout specs while still meeting firm standards: the Returns tab (its own grid layout) and the KPI Tracker tab (laid out per the `kpi-tracker` skill) — for these, verify firm compliance (font, colors, gridlines off, and freeze panes per the Freeze Pane Standard — Returns is unfrozen, KPI Tracker freezes at `B6`) without restructuring their layout.

### Tab Organization

Reorder all tabs to match the firm-standard tab order: Task Tracker → Output → Consensus → Returns → Model Tab → KPI Tracker → IS → BS → CFS → Profit Build → Quarterly Build (optional, when present) → BS & CFS Build → Capital Allocation Build → Annual Historicals → Quarterly Historicals → Data Pull tabs → source tabs. Apply tab colors per firm-formatting Tab Colors table: primary output tabs (incl. KPI Tracker) navy #1C3553; IS/BS/CFS sage #6B9E6F; the build tabs (Profit Build, Quarterly Build when present, BS & CFS Build, Capital Allocation Build) steel #5B8FA8; Annual/Quarterly Historicals teal #436E71. Move any source/reference tabs to the far right with Gray (#7C7F88) coloring.

---

## Tab Completion Verification

After formatting EVERY tab, read and paste for each:

```
TAB VERIFICATION -- [Tab Name]:
  freezePanes: [expected value per the firm-formatting Freeze Pane Standard -- STOP and fix if wrong]
  gridlines: [off -- if on, STOP and fix]
  font: [Arial 10pt -- if not, STOP and fix]
  column widths uniform: [yes/no]
  tab color: [hex] -- expected: [hex from Tab Colors table]
  tab position: [correct/wrong]
  Standard Tab Header (rows 1-6): [correct/missing/wrong -- or "exempt (own layout)" for
    Returns, Task Tracker, and KPI Tracker, which are exempt per firm-formatting]
  year labels follow convention (FY YYYYA/E): [yes/no]
  number formats include _) padding: [spot-checked yes/no]
  Section Boundary Rule applied: [spot-checked yes/no]
```

If you do not paste this output, the user cannot verify compliance. No output = not verified.

---

## Definition of Done (Phase 8)

A phase is complete if and only if ALL of the following are true. Report completion by reading these values back to the user -- not by summarizing in prose.

1. **Task Tracker**: Every subtask row for Phase 8 shows Status = "COMPLETE". Cite the actual cell addresses you checked.
2. **Self-check checklist** from firm-formatting run on every tab -- all items checked.
3. **Tab order** matches firm standard (Task Tracker, Output, Consensus, Returns, Model Tab, KPI Tracker, IS, BS, CFS, Profit Build, BS & CFS Build, Capital Allocation Build, Annual Historicals, Quarterly Historicals, data tabs).
4. **Tab colors** match the Tab Colors table for every tab.
5. **Tab Completion Verification** output pasted for EVERY tab in the workbook.
6. **Task Tracker Model State Block**: "Last Skill Run" updated, "Next Skill" = "build-model-phase-09".

If you write "Phase 8 complete" in chat before reading and reporting these values, you have made an error. Re-verify and correct.

**STOP. Report status. Wait for "continue."**
