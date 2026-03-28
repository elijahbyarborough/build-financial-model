---
name: build-model-phase-0
description: |
  Phase 0 -- Setup. Ingests source data, creates tabs, builds the Task Tracker, and detects lease exposure.
  Load after `build-model` and `firm-formatting` are already in context.
---

# Phase 0 -- Setup

## Before Starting This Phase
1. Ensure `build-model` and `firm-formatting` skills are loaded
2. This is the first phase -- no prior state to restore

## After Completing This Phase
1. Update Task Tracker: mark Phase 0 complete, record all outputs and state
2. Clear context (start new conversation)
3. Next phase: load `build-model` + `firm-formatting` + `build-model-phase-1`

## Phase Instructions
See `phase-0-setup.md` for the complete phase reference.
