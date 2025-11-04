---
name: Subagent-Driven Development
description: Use when executing implementation plans with independent tasks in the current session - dispatches fresh subagent for each task with code review between tasks, enabling fast iteration with quality gates
---

# Subagent-Driven Development

Execute plan by dispatching fresh subagent per task, with code review after each.

**Core principle:** Fresh subagent per task + review between tasks = high quality, fast iteration

## Overview

**vs. Executing Plans (parallel session):**

- Same session (no context switch)
- Fresh subagent per task (no context pollution)
- Code review after each task (catch issues early)
- Faster iteration (no human-in-loop between tasks)

**When to use:**

- Staying in this session
- Tasks are mostly independent
- Want continuous progress with quality gates

**When NOT to use:**

- Need to review plan first (use executing-plans)
- Tasks are tightly coupled (manual execution better)
- Plan needs revision (brainstorm first)

## The Process

### 1. Load Plan

1. Read plan file
2. **Check for design document**: If plan header references a design doc, read it
3. Create TodoWrite with all tasks
4. Note: Design doc (if exists) will be passed to implementation subagents for architectural context

### 2. Execute Task with Subagent

For each task:

**Dispatch fresh subagent:**

```
Task tool (general-purpose):
  description: "Implement Task N: [task name]"
  prompt: |
    You are implementing Task N from [plan-file].

    **Design Context:** [If design doc exists: Read docs/designs/YYYY-MM-DD-feature-design.md for architectural intent before starting]

    Read the task carefully. Your job is to:
    1. Implement exactly what the task specifies
    2. Follow architecture from design doc (if provided)
    3. Write tests (following TDD if task says to)
    4. Verify implementation works
    5. Commit your work
    6. Report back

    Work from: [directory]

    Report: What you implemented, what you tested, test results, files changed, any issues
```

**Subagent reports back** with summary of work.

### 3. Review Subagent's Work

**Dispatch code-reviewer subagent:**

```
Task tool (superpowers:code-reviewer):
  Use template at requesting-code-review/code-reviewer.md

  WHAT_WAS_IMPLEMENTED: [from subagent's report]
  PLAN_OR_REQUIREMENTS: Task N from [plan-file]
  BASE_SHA: [commit before task]
  HEAD_SHA: [current commit]
  DESCRIPTION: [task summary]
```

**If design doc exists:** Include design doc path in code review context:

```
DESIGN_DOC: docs/designs/YYYY-MM-DD-feature-design.md
```

Code reviewer checks alignment with both plan and design.

**Code reviewer returns:** Strengths, Issues (Critical/Important/Minor), Assessment

### 4. Apply Review Feedback

**If issues found:**

- Fix Critical issues immediately
- Fix Important issues before next task
- Note Minor issues

**Dispatch follow-up subagent if needed:**

```
"Fix issues from code review: [list issues]"
```

### 5. Mark Complete, Next Task

- Mark task as completed in TodoWrite
- Move to next task
- Repeat steps 2-5

### 6. Final Review

After all tasks complete, dispatch final code-reviewer:

- Reviews entire implementation
- Checks all plan requirements met
- Validates overall architecture

### 7. Cleanup and Complete Development

After final review passes:

**Cleanup background processes - MANDATORY:**

YOU MUST clean up background processes before completing. No exceptions.

1. List background shells: `/bashes`
2. Kill all shells created by subagents
3. Verify: `/bashes` shows no orphaned processes

Each subagent dispatch = background shells. Every time. Multiple tasks = accumulated shells = resource waste.

**Skipping cleanup = poor process hygiene. Always.**

**Complete development:**

- Announce: "I'm using the finishing-a-development-branch skill to complete this work."
- **REQUIRED SUB-SKILL:** Use superpowers:finishing-a-development-branch
- Follow that skill to verify tests, present options, execute choice

## Example Workflow

```
You: I'm using Subagent-Driven Development to execute this plan.

[Load plan, create TodoWrite]

Task 1: Hook installation script

[Dispatch implementation subagent]
Subagent: Implemented install-hook with tests, 5/5 passing

[Get git SHAs, dispatch code-reviewer]
Reviewer: Strengths: Good test coverage. Issues: None. Ready.

[Mark Task 1 complete]

Task 2: Recovery modes

[Dispatch implementation subagent]
Subagent: Added verify/repair, 8/8 tests passing

[Dispatch code-reviewer]
Reviewer: Strengths: Solid. Issues (Important): Missing progress reporting

[Dispatch fix subagent]
Fix subagent: Added progress every 100 conversations

[Verify fix, mark Task 2 complete]

...

[After all tasks]
[Dispatch final code-reviewer]
Final reviewer: All requirements met, ready to merge

Done!
```

## Advantages

**vs. Manual execution:**

- Subagents follow TDD naturally
- Fresh context per task (no confusion)
- Parallel-safe (subagents don't interfere)

**vs. Executing Plans:**

- Same session (no handoff)
- Continuous progress (no waiting)
- Review checkpoints automatic

**Cost:**

- More subagent invocations
- But catches issues early (cheaper than debugging later)

## Red Flags

**Never:**

- Skip code review between tasks
- Proceed with unfixed Critical issues
- Dispatch multiple implementation subagents in parallel (conflicts)
- Implement without reading plan task
- Leave background processes running after subagents complete

**Design doc rationalizations:**

If you're thinking:

- "Subagent doesn't need design doc for this simple task"
- "Design doc slows down iteration"
- "I read it, that's enough"

**All of these mean: Pass design doc to subagent. Step 1 requires it.**

| Excuse                     | Reality                                                                                     |
| -------------------------- | ------------------------------------------------------------------------------------------- |
| "Task is too simple"       | Simple tasks still need architectural alignment. 2 min reading prevents integration issues. |
| "Slows iteration"          | Fast implementation of wrong architecture is slower overall. Context prevents rework.       |
| "I read it, that's enough" | Subagent has fresh eyes. They'll catch what you missed. Design doc provides shared context. |

**Cleanup rationalizations:**

| Excuse                              | Reality                                                        |
| ----------------------------------- | -------------------------------------------------------------- |
| "I'll clean up after all tasks"     | Multiple subagents = accumulated shells. Clean up per task.    |
| "Background processes auto-cleanup" | They don't. You must kill them explicitly.                     |
| "One more subagent won't matter"    | Each adds more. Clean up immediately after subagent completes. |

**If subagent fails task:**

- Dispatch fix subagent with specific instructions
- Don't try to fix manually (context pollution)

## Integration

**Required workflow skills:**

- **writing-plans** - REQUIRED: Creates the plan that this skill executes
- **requesting-code-review** - REQUIRED: Review after each task (see Step 3)
- **finishing-a-development-branch** - REQUIRED: Complete development after all tasks (see Step 7)

**Subagents must use:**

- **test-driven-development** - Subagents follow TDD for each task

**Alternative workflow:**

- **executing-plans** - Use for parallel session instead of same-session execution

See code-reviewer template: requesting-code-review/code-reviewer.md
