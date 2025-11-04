---
name: Executing Plans
description: Use when partner provides a complete implementation plan to execute in controlled batches with review checkpoints - loads plan, reviews critically, executes tasks in batches, reports for review between batches
---

# Executing Plans

## Overview

Load plan, review critically, execute tasks in batches, report for review between batches.

**Core principle:** Batch execution with checkpoints for architect review.

**Announce at start:** "I'm using the executing-plans skill to implement this plan."

## The Process

### Step 1: Load and Review Plan

1. Read plan file
2. **Check for design document**: If plan header references a design doc, read it
3. Review critically - identify questions or concerns about plan or design alignment
4. If concerns: Raise them with your human partner before starting
5. If no concerns: Create TodoWrite and proceed

### Step 2: Execute Batch

**Default: First 3 tasks**

**Design context:** If plan references design doc, keep architectural intent in mind during implementation. Check design doc if implementation questions arise.

For each task:

1. Mark as in_progress
2. Follow each step exactly (plan has bite-sized steps)
3. Run verifications as specified
4. **Quick validation** (after completing task, before marking complete):
   - **Type check**: If you changed signatures, run type checker (mypy/tsc/etc.)
   - **Import validation**: If you changed function/class names, grep for call sites
   - **Task-specific test**: If plan specifies a test for this task, run it
   - **Fix forward**: If validation fails, fix before next task
5. Mark as completed

**Exception**: Multi-file refactors that intentionally break across tasks - document the plan and validate at batch boundary instead.

### Step 3: Report

When batch complete:

- Show what was implemented
- Show verification output
- Note any deviations from design/plan (if applicable)
- Say: "Ready for feedback."

### Step 4: Continue

Based on feedback:

- Apply changes if needed
- Execute next batch
- Repeat until complete

### Step 5: Cleanup and Complete Development

After all tasks complete and verified:

**Cleanup background processes - MANDATORY:**

YOU MUST clean up background processes before completing. No exceptions.

1. List background shells: `/bashes`
2. Kill all shells created during plan execution
3. Verify: `/bashes` shows no orphaned processes

Plan execution leaves background shells running. Every time. Long plans = accumulated resource waste.

**Complete development:**

- Announce: "I'm using the finishing-a-development-branch skill to complete this work."
- **REQUIRED SUB-SKILL:** Use superpowers:finishing-a-development-branch
- Follow that skill to verify tests, present options, execute choice

## Red Flags - Design Doc Rationalizations

**If you're thinking:**

- "Design doc seems redundant with plan"
- "Too urgent to read design doc"
- "I'll read it if I have questions"
- "Following spirit not letter"

**All of these mean: Read the design doc. Step 1 explicitly requires it.**

| Excuse                         | Reality                                                                   |
| ------------------------------ | ------------------------------------------------------------------------- |
| "Design doc is redundant"      | Redundancy is validation. 5 min confirms alignment or catches divergence. |
| "Too urgent to skip reading"   | Emergencies need MORE context. 5 min prevents 30 min debugging wrong fix. |
| "I'll read if questions arise" | Questions arise from gaps you don't know exist. Read first.               |

## When to Stop and Ask for Help

**STOP executing immediately when:**

- Hit a blocker mid-batch (missing dependency, test fails, instruction unclear)
- Plan has critical gaps preventing starting
- You don't understand an instruction
- Verification fails repeatedly

**Ask for clarification rather than guessing.**

## When to Revisit Earlier Steps

**Return to Review (Step 1) when:**

- Partner updates the plan based on your feedback
- Fundamental approach needs rethinking

**Don't force through blockers** - stop and ask.

## Cleanup Rationalizations

| Excuse                            | Reality                                                 |
| --------------------------------- | ------------------------------------------------------- |
| "I'll clean up when plan done"    | Shells accumulate during execution. Clean up at Step 5. |
| "Long plan = cleanup can wait"    | Long plan = more shells. Clean up before completion.    |
| "Background processes don't hurt" | They consume resources. Always clean up.                |

## Remember

- Review plan critically first
- Follow plan steps exactly
- Don't skip verifications
- Reference skills when plan says to
- Between batches: just report and wait
- Stop when blocked, don't guess
- Clean up background processes at Step 5
