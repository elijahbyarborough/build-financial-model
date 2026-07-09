---
name: update-model
description: |
  Post-earnings / post-filing update for a finished FC model. Captures the new quarter (or fiscal year) onto the Quarterly/Annual Historicals tabs, flips the KPI Tracker column from E to A, and verifies integrity. Does NOT touch driver assumptions, projections, or the Returns tab -- those are deliberate manual follow-ups.

  Triggers on: "update the model", "[TICKER] reported", "[TICKER] just printed", "new quarter", "roll in earnings", "the 10-Q dropped", "the 10-K is out", "add the latest quarter to the model", or any request to bring a built model current after a company reports.
---

# Update Model — Post-Earnings / Post-Filing Update

Bring a finished FC model current after the company prints. Three jobs, in order: **capture** the new data onto the Historicals tabs (the model's single hardcode layer), **flip** the KPI Tracker column, **verify** nothing broke. Everything downstream of the Historicals tabs updates through live links.

**This skill updates the columns. It does not roll the model.** Driver assumptions, projection horizon extension, Returns entry re-anchoring, and Consensus refresh are deliberate analyst decisions — flagged at the end, never done automatically.

## Required skill loads

Load alongside this skill: `build-model` + `firm-formatting` (foundations, always) and `kpi-tracker` (Track B is the flip procedure). Capture methodology and edge-case protocols: `meth-historicals.md` (copy in this directory).

## Preflight (output this block before any work; any blank or x = STOP)

```
UPDATE-MODEL PREFLIGHT:
  [ ] Foundation skills loaded: build-model, firm-formatting, kpi-tracker
  [ ] Workbook is a v2 FC model: Task Tracker tab [exists], Annual Historicals [exists],
      Quarterly Historicals [exists], KPI Tracker [exists]
  [ ] Company / ticker: [value]
  [ ] What printed: [earnings release only | 10-Q | 10-K (+release)]
  [ ] Source document(s) in hand: [list -- release PDF, filing, IR deck]
  [ ] Last captured quarter on Quarterly Historicals: [read from tab, e.g. FQ3 2026A]
  [ ] Firm colors verified: assumption bg #FFFF00, blue #0000FF, navy #1C3553,
      subheader #C2D5EB, major total #F2F2F2
```

If the workbook is NOT a v2 model (no Historicals tabs), stop and tell the user this skill requires the build-model v2 architecture — offer `kpi-tracker` standalone mode for the tracker alone.

## Step 1 — Identify the print type

| Printed | Update |
|---|---|
| **Earnings release only** (day-of) | Quarterly Historicals sections 1–6 (IS, Segments, Geographic, Operational/KPI, Share Count, Guidance) |
| **10-Q** (days later) | Same column, sections 7–12 (BS, CFS, Debt Detail, Lease Detail, Non-GAAP Recons, Miscellaneous) |
| **10-K (+Q4/FY release)** | Annual Historicals FIRST (new FY column, all sections), then Quarterly Q4, then full-year tie-outs |

A release-day update that stops at section 6 is complete for that day — note on the Task Tracker that sections 7–12 await the filing.

## Step 2 — Capture

All capture follows `meth-historicals.md`: **capture everything the company reports**, hardcoded blue #0000FF + source comment (`Source: [doc], [date], [ref]`), units per the tab's declared units, sections in the canonical order, never a new hierarchy.

1. **Append the new FQ column** to Quarterly Historicals in position (after the last quarter). Column label per the Year Label Convention (`FQN YYYYA`), text format.
2. **Enter the reported values** for every line the company reports in the applicable sections. New disclosed lines the company added this quarter → add the row to the section (capture-everything); note it in the Change Log.
3. **10-K path**: append the new FY column to Annual Historicals first and capture all sections. Then on Quarterly Historicals: enter Q4 as reported, or derive it where not separately reported (`= FY − ΣQ1–Q3`, black formula referencing Annual Historicals). Quarterly CFS: capture as-reported YTD, derive the discrete quarter (`= YTDn − YTDn−1`, black formula).
4. **Tie-outs**: when a fiscal year completes, populate the ΣQ1–Q4 = FY tie-out cells for every flow section. Any non-zero → resolve before proceeding (usually a units or restatement issue).
5. **Guidance**: enter the new guidance as given (mid-points, blue + comment citing the release) in the Guidance section. Do not overwrite prior-period guidance — it is the record of what was guided when.

**Edge cases — handle BEFORE touching downstream tabs** (full protocols in `meth-historicals.md`):
- Segment recast → versioned block protocol + `SEGMENT_PRESENTATION` flag + Change Log
- Restatement / 10-K/A → overwrite affected cells, comment cites the amending filing, Change Log
- ASC adoption noted in the filing → note row + Change Log, never self-restate
- FYE change → transition stub column protocol + Change Log

## Step 3 — Flip the KPI Tracker

Follow the `kpi-tracker` skill's **Track B**, with the plugin-model sourcing rule:

1. Identify the target column (first column labeled `E` for the printed quarter).
2. **Write green cross-sheet formulas** into the flipped column pulling the new quarter's cells from Quarterly Historicals — forward columns are blank until flipped, so the links are created now, at flip time.
3. Flip the period label `E` → `A`; move the forecast divider one column right.
4. Intra-tab derived rows (Y/Y, margins, FY derivations) update automatically; verify the FY formulas still span the right quarters per the FY Derivation Matrix.
5. New guidance mid-points → next unreported quarter column and/or guided FY column, blue + cell note.
6. If the tracker has run out of forward columns, extend per the kpi-tracker forward-projection matrix.

## Step 4 — Verify (read values back; do not assert from memory)

```
UPDATE VERIFICATION -- [Ticker] [FQ/FY]:
  Quarterly Historicals: column [label] added, [N] values captured across [M] sections
  Annual Historicals: [FY column added + N values | not applicable this update]
  Tie-outs (if year completed): [each = 0, or list non-zero]
  BS Check (all periods): [0 or value]      CF Check (all periods): [0 or value]
  Recon checks vs Annual Historicals (if annual updated): NI [0], TA [0], End Cash [0]
  KPI Tracker: column [label] flipped E->A [yes/no], links live [yes/no], divider moved [yes/no]
  Drift audit (3 random cells, per build-model): [PASS/FAIL each]
  Change Log entries this update: [count + types, or none]
```

Any failed check → stop and fix before reporting the update complete. A BS/CF check that was 0 before the update and is non-zero after means a capture step touched something it shouldn't have — historicals capture must never break the model.

## Step 5 — Report and stop

Report the verification block plus a one-line summary of what printed and what was captured. Then list the **deliberate follow-ups NOT done** (analyst's call, separate sessions):

- Revisit driver assumptions against the new actuals (Phase 3 build tabs)
- Extend the projection horizon if a fiscal year closed (Phase 3/4)
- Re-anchor the Returns tab entry date/price (Phase 7)
- Refresh the Consensus tab source data (Phase 11)

Update the Task Tracker: log the update (date, quarter, sections captured, checks passed) in the Notes/log area. Do not change the Model State Block phase fields — this skill is maintenance, not a build phase.
