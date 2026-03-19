# Phase 8 — QC (Quality Control)

**Reference:** `references/phase-8-qc.md`
**Prerequisite:** Phase 7 complete (formatting applied).
**Also load:** `references/data-quality.md` for the 6 quality gates and formula rules.
**This is the final phase.**

---

## What Happens in This Phase

Run structural and formula QC across the entire model. The audit reads — it does not modify. All findings go to the Task Tracker.

---

## 6 Quality Gates

| Gate | What It Checks | Pass Criteria |
|------|---------------|---------------|
| 1. Structural integrity | BS Check = 0, CF Check = 0, NI linkage, RE roll-forward, BS→CF mapping holds | Zero errors, all periods |
| 2. Assumption review | All blue-text cells sourced, no stale assumptions (>90 days) | Every assumption sourced and current |
| 3. Comps validation | SPG() formulas resolving, peer set valid | Variances within +/-10% or documented |
| 4. Thesis consistency | Key Decisions Log aligns with model assumptions | No contradictions |
| 5. Model tab consistency | Summary numbers match underlying tabs | Zero mismatches |
| 6. Returns sanity | XIRR/MOIC consistent, sensitivity reasonable | IRR/MOIC plausible |

### Gate Failure Protocol
- **Gate 1**: Fix structural errors first. No exceptions.
- **Gates 2-4**: Flag, propose fix, get user confirmation.
- **Gate 5**: Refresh Model tab.
- **Gate 6**: Trace upstream to the offending assumption.

---

## Key Rules

- **Does NOT fix issues** — tags each finding with the responsible skill and adds to Task Tracker.
- **Severity levels**: CRITICAL (wrong), WARNING (could mislead), INFO (worth noting).
- **No plugs.**
- **Validates against Core Rules** in build-model SKILL.md.
- **Validates formatting against `firm-formatting`.**
- **No freeze panes anywhere.**

**STOP. Update Task Tracker with all findings. Model is complete when all gates pass.**
