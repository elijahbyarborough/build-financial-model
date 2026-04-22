# Phase 7 — Formatting

**Prerequisite:** Phase 6 complete (Returns tab built, all model content finalized).

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
- **No freeze panes. Gridlines off. No merged cells.**
- **Uniform column widths** across year columns.

Format every tab. No tab is exempt.

### Tab Organization

Reorder all tabs to match the firm-standard tab order: Task Tracker → Output → Consensus → Returns → Model → IS → BS → CF → builds → data tabs. Apply tab colors per firm-formatting Tab Colors table. Move any source/reference tabs to the far right with Gray (#7C7F88) coloring.

---

## Tab Completion Verification

After formatting EVERY tab, read and paste for each:

```
TAB VERIFICATION -- [Tab Name]:
  freezePanes: [null -- if not null, STOP and fix]
  gridlines: [off -- if on, STOP and fix]
  font: [Arial 10pt -- if not, STOP and fix]
  column widths uniform: [yes/no]
  tab color: [hex] -- expected: [hex from Tab Colors table]
  tab position: [correct/wrong]
  Standard Tab Header (rows 1-6): [correct/missing/wrong]
  year labels follow convention (FY YYYYA/E): [yes/no]
  number formats include _) padding: [spot-checked yes/no]
  Section Boundary Rule applied: [spot-checked yes/no]
```

If you do not paste this output, the user cannot verify compliance. No output = not verified.

---

## Definition of Done (Phase 7)

A phase is complete if and only if ALL of the following are true. Report completion by reading these values back to the user -- not by summarizing in prose.

1. **Task Tracker**: Every subtask row for Phase 7 shows Status = "COMPLETE". Cite the actual cell addresses you checked.
2. **Self-check checklist** from firm-formatting run on every tab -- all items checked.
3. **Tab order** matches firm standard (Task Tracker, Output, Consensus, Returns, Model, IS, BS, CFS, builds, data tabs).
4. **Tab colors** match the Tab Colors table for every tab.
5. **Tab Completion Verification** output pasted for EVERY tab in the workbook.
6. **Task Tracker Model State Block**: "Last Skill Run" updated, "Next Skill" = "build-model-phase-8".

If you write "Phase 7 complete" in chat before reading and reporting these values, you have made an error. Re-verify and correct.

**STOP. Report status. Wait for "continue."**
