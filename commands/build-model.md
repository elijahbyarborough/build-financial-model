---
description: Conductor for the FC financial model build. Runs phases 0-12, each in a fresh isolated subagent, checkpointing to the Task Tracker between stages. Pauses after each phase by default; pass "auto" to roll through, pausing only on failures.
argument-hint: "[auto]   (optional — run continuously, stop only on failures)"
---

# Build-Model Conductor

You are the **conductor** for a Frist Capital financial model build. Your ONLY job is
orchestration and checkpointing. You do NOT build any part of the model yourself, and you
do NOT reason about modeling methodology — every phase is executed by a fresh subagent that
loads the relevant skills. Keep your own context lean: hold only short phase summaries,
never the skill bodies or the model contents.

## Precondition

This command needs a harness with (a) subagent support and (b) live access to the target
workbook (Claude for Excel connection or work-across-apps). If you cannot spawn subagents,
or cannot reach the workbook, STOP and tell the user this must run from Claude Code or
Cowork with Excel connected — it will not work in the plain Excel sidebar.

## Mode

- Argument received: `$ARGUMENTS`
- If it contains `auto` → **AUTO mode**: run phases back-to-back, pausing ONLY when a
  subagent reports a failed check (QC gate, integrity check, or unverified Definition of
  Done), or when Phase 0 needs build inputs.
- Otherwise → **CHECKPOINT mode** (default): run one phase, report, then STOP and wait for
  the user to say "continue" before starting the next.

## Step 1 — Determine the starting phase

Check the workbook for a **Task Tracker** tab.
- **Exists** → read the Model State Block; the starting phase = its "Next Skill" value.
  This is a resume — do NOT restart from Phase 0.
- **Does not exist** → new build; starting phase = Phase 0. Before spawning Phase 0,
  confirm with the user: ticker, fiscal year-end month, and which tab holds the source
  data. (Always pause for these inputs regardless of mode.)

## Step 2 — Run each phase (loop from start phase through Phase 12)

For each phase N, spawn ONE fresh subagent with this assignment:

> You are executing Phase N of the FC model build.
> 1. Load these skills, in order: `build-model`, `firm-formatting`, `build-model-phase-N`
>    (N zero-padded to two digits for phases 0-9: `build-model-phase-04`, not `-4`).
>    For Phase 2 only, also load `kpi-tracker` (the phase's KPI Tracker step defers to it).
> 2. Read the Task Tracker tab to establish current state; resume where it indicates.
> 3. Execute Phase N exactly as its skill specifies — every preflight check, every step,
>    all methodology. Obey every Core Rule: assumptions only on build tabs, never a plug,
>    single hardcode layer (Historicals tabs), lease rules, `_)` number padding, firm colors.
> 4. Checkpoint continuously: mark each subtask COMPLETE on the Task Tracker as you finish
>    it, per the Subtask Completion Protocol — do not batch these at the end.
> 5. Verify the phase's Definition of Done by READING the actual cell values back out of
>    the workbook (BS Check, CF Check, gate results, etc.) — never assert completion from
>    memory.
> 6. Update the Model State Block: Last Skill Run = phase-N + today's date,
>    Next Skill = phase-(N+1).
> 7. Return ONLY a concise status report: phase number, PASS/FAIL of each Definition-of-Done
>    check with its read-back value, any flags raised, and any subtask left incomplete.
>    Do NOT return model contents or skill text.

When the subagent returns:
- **Any FAIL, unverified check, or incomplete subtask** → STOP in BOTH modes. Surface the
  failure verbatim and wait for the user. Never advance past a broken phase.
- **CHECKPOINT mode, clean pass** → report the summary, then STOP and wait for "continue."
- **AUTO mode, clean pass** → report a one-line summary and immediately spawn the next phase.

## Step 3 — Completion

After Phase 12 passes, report "Model build complete," list the phases run this session, and
restate the final integrity checks (BS Check = 0, CF Check = 0) as read back by the last
phases. Do not declare completion before those values are confirmed.

## Guardrails

- One phase per subagent. Never run two phases in a single subagent — that defeats the
  context reset that makes this worth doing.
- Never skip a phase. Order is strict: 0 → 1 → 2 → ... → 12. One sanctioned exception:
  Phase 11 (Consensus) may be skipped when no consensus data source exists — record the
  skip on the Task Tracker and proceed to Phase 12 (whose preflight accepts it).
- You (the conductor) never touch Excel cells directly. If tempted to "just fix" something,
  spawn a subagent to do it.
- If a subagent dies or returns nothing, retry it once; if it fails again, STOP and report.
