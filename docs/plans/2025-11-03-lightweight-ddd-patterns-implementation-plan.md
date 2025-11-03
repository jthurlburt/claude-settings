# Lightweight DDD Patterns Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Integrate Document-Driven Development patterns into superpowers by creating design artifact infrastructure and enhancing five existing skills with document-first workflow patterns.

**Design:** [../designs/2025-11-03-lightweight-ddd-patterns-design.md](../designs/2025-11-03-lightweight-ddd-patterns-design.md)

**Architecture:** Create `docs/designs/` directory with README template, then enhance brainstorming, writing-plans, executing-plans, subagent-driven-development, and documentation-management skills to create/reference/validate design artifacts. System works with or without designs (backward compatible).

**Tech Stack:** Markdown documentation, bash for existence checks

---

## Existing Patterns Survey

**Similar features:**

- Tier 1 knowledge management integration (ADR + DISCOVERIES)
- Existing planning workflow (writing-plans → executing-plans)
- Skill composition via explicit calls

**Conventions to follow:**

- Skills check for optional infrastructure: `test -d docs/decisions && ...`
- Graceful degradation when infrastructure missing
- Cross-references use relative paths: `../designs/`, `../decisions/`
- Skill modifications add sections, don't rewrite entirely
- Commit messages: Conventional commits format

---

## Task 1: Create Design Infrastructure

**Files:**

- Create: `docs/designs/README.md`

**Step 1: Create designs directory** (already done in brainstorming Phase 4)

```bash
test -d docs/designs && echo "Already exists" || mkdir -p docs/designs
```

Expected: Directory exists

**Step 2: Write design template README**

Create `docs/designs/README.md` with:

- Overview of design docs purpose
- When to create design docs vs going straight to plan
- Template structure explanation
- Status field guidance (Draft → Approved → Implemented → Superseded)
- Versioning guidance (identified in Phase 4.5 challenge)
- Retcon writing reminder
- Examples

Content:

```markdown
# Feature Designs

This directory contains architectural designs for features before implementation. Designs capture the high-level approach, components, data flow, and key decisions to inform implementation planning.

## When to Create Design Docs

**Create a design doc when:**

- Feature requires significant architectural decisions
- Multiple implementation approaches need evaluation
- Feature complexity warrants upfront design work
- Team needs shared understanding before implementation
- Architecture exploration without immediate implementation

**Skip design docs when:**

- Implementation is straightforward (enhance existing pattern)
- Requirements are clear and approach is obvious
- Quick prototype or experiment
- Bug fixes or small changes

## Design Doc Structure

Each design doc includes:

- **Goal**: One paragraph - problem being solved and value provided
- **Architecture**: High-level approach using present tense (retcon writing)
- **Components**: Major parts and their responsibilities
- **Data Flow**: How data moves through the system
- **Error Handling**: Failure modes and recovery strategies
- **Testing Strategy**: Unit/integration/e2e approach
- **Open Questions**: Items still TBD
- **Design Decisions**: Key choices with rationale and trade-offs
- **Related ADRs**: Links to architecture decision records
- **Source**: Inspiration or prior art references

## Status Field

Track design lifecycle:

- **Draft**: Initial design, not yet reviewed
- **Approved**: Design reviewed and ready for implementation
- **Implemented**: Feature built, design served its purpose
- **Superseded**: Replaced by newer design (link to it)

## Versioning Guidance

**When design evolves after plan created:**

1. Update design doc with changes
2. Add note at top: "Updated YYYY-MM-DD: [what changed]"
3. Consider whether plan needs updating
4. If significant changes, create new design doc and supersede old one

**Preventing staleness:**

- Implementation plans reference designs by filename
- When updating design, grep for references: `grep -r "design-filename" docs/plans/`
- Update referencing plans or add migration notes

## Retcon Writing Technique

Write designs in **present tense** as if the feature already exists:

✅ **Good:**

- "The system validates inputs before processing"
- "Authentication uses JWT tokens stored in HTTP-only cookies"
- "The cache expires after 5 minutes"

❌ **Avoid:**

- "The system will validate inputs"
- "We will use JWT tokens"
- "The cache will expire"

**Why:** Present tense reduces hedging, makes designs more concrete, and easier to review. You're describing the intended reality, not speculating about the future.

## Naming Convention

Files: `YYYY-MM-DD-<feature-name>-design.md`

Examples:

- `2025-11-03-user-authentication-design.md`
- `2025-11-15-caching-strategy-design.md`

Use actual date when design is created, not when feature is implemented.

## Relationship to Other Docs

**Design docs inform:**

- Implementation plans (`docs/plans/`) - plans reference designs for architectural context
- Code - implementation follows design intent
- Documentation - architecture sections reference designs

**Design docs reference:**

- ADRs (`docs/decisions/`) - architectural decisions
- Amplifier analysis or external sources - inspiration
- Prior art - similar features or patterns

## Example Workflow

1. **Brainstorming** (Phases 1-4): Create design doc in `docs/designs/`
2. **Review**: Validate design before proceeding
3. **Writing-plans**: Create implementation plan referencing design
4. **Implementation**: Execute plan following design intent
5. **Update status**: Mark design as Implemented

Design docs can exist independently (exploration without implementation) or as part of full workflow.
```

**Step 3: Verify file created**

```bash
ls -la docs/designs/README.md
wc -l docs/designs/README.md
```

Expected: File exists with ~100+ lines

**Step 4: Commit**

```bash
git add docs/designs/README.md
git commit -m "feat(docs): add design artifact infrastructure and template"
```

---

## Task 2: Enhance brainstorming Skill - Phase 4 Modifications

**Files:**

- Modify: `skills/brainstorming/SKILL.md`

**Step 1: Read current Phase 4 section**

Read `skills/brainstorming/SKILL.md` lines 100-140 (Phase 4: Design Documentation section)

**Step 2: Update Phase 4 output location**

Find this text in Phase 4:

```markdown
- **File location:** `docs/plans/YYYY-MM-DD-<topic>-design.md` (use actual date and descriptive topic)
```

Replace with:

```markdown
- **File location:** `docs/designs/YYYY-MM-DD-<topic>-design.md` (use actual date and descriptive topic)
```

**Step 3: Add retcon writing guidance**

After the "After validating the design, write it to a permanent document:" line, before "- **File location:**", add:

```markdown
**Retcon Writing Technique:**

Write the design in **present tense** as if the feature already exists:

- ✅ "The system validates inputs before processing"
- ❌ "The system will validate inputs before processing"
- ✅ "Authentication uses JWT tokens"
- ❌ "Authentication will use JWT tokens"

This reduces hedging, makes designs more concrete, and easier to review.
```

**Step 4: Update design template reference**

Find the "- **Content:**" bullet in Phase 4.

Replace with:

```markdown
- **Content:** Use design template structure from `docs/designs/README.md`:
  - Goal (one paragraph problem/value statement)
  - Architecture (high-level approach, present tense)
  - Components (major parts and responsibilities)
  - Data Flow (how data moves through system)
  - Error Handling (failure modes and recovery)
  - Testing Strategy (unit/integration/e2e approach)
  - Open Questions (TBD items)
  - Design Decisions (key choices with rationale)
  - Related ADRs (links to architecture decision records)
  - Source (inspiration or prior art references)
```

**Step 5: Add approval gate**

After "Commit the design document to git before proceeding", add:

```markdown
**Approval gate:** Ask user: "Design document created in `docs/designs/`. Review before proceeding to worktree setup?"

Wait for user confirmation before moving to Phase 5.
```

**Step 6: Verify changes**

```bash
grep "docs/designs" skills/brainstorming/SKILL.md
grep -A 5 "Retcon Writing" skills/brainstorming/SKILL.md
grep "Approval gate" skills/brainstorming/SKILL.md
```

Expected: All three patterns appear in Phase 4

**Step 7: Commit**

```bash
git add skills/brainstorming/SKILL.md
git commit -m "feat(skills): enhance brainstorming with design artifacts and retcon writing"
```

---

## Task 3: Enhance writing-plans Skill - Header and Survey

**Files:**

- Modify: `skills/writing-plans/SKILL.md`

**Step 1: Read current header section**

Read `skills/writing-plans/SKILL.md` lines 60-76 (Plan Document Header section)

**Step 2: Update header template**

Find the header template (after "**Every plan MUST start with this header:**").

Current:

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

Replace with:

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Design:** [../designs/YYYY-MM-DD-feature-design.md](../designs/YYYY-MM-DD-feature-design.md) _(if exists)_

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

**Step 3: Add design check to Survey Existing Patterns**

Find "## Survey Existing Patterns (before detailed planning)" section.

After the section intro paragraph, before step 1 "**Find similar features**", add step 0:

````markdown
0. **Check for design document**: If this plan is being created from a design phase, reference it
   ```bash
   test -f docs/designs/*-feature-design.md && fd . docs/designs
   ```
````

Read the design document to understand the intended architecture before planning implementation details. Include design doc link in plan header.

````

Renumber existing steps: current 1→1, 2→3, 3→4, 4→5, 5→6 (step 2 already references decisions/).

**Step 4: Verify changes**

```bash
grep "Design:" skills/writing-plans/SKILL.md
grep -B 2 "Check for design document" skills/writing-plans/SKILL.md
````

Expected: Design field in header template, design check in survey

**Step 5: Commit**

```bash
git add skills/writing-plans/SKILL.md
git commit -m "feat(skills): enhance writing-plans to reference design docs"
```

---

## Task 4: Enhance executing-plans Skill - Load and Report

**Files:**

- Modify: `skills/executing-plans/SKILL.md`

**Step 1: Read current Step 1 section**

Read `skills/executing-plans/SKILL.md` lines 18-24 (Step 1: Load and Review Plan)

**Step 2: Enhance Step 1 with design check**

Find "### Step 1: Load and Review Plan" section.

Current:

```markdown
1. Read plan file
2. Review critically - identify any questions or concerns about the plan
3. If concerns: Raise them with your human partner before starting
4. If no concerns: Create TodoWrite and proceed
```

Replace with:

```markdown
1. Read plan file
2. **Check for design document**: If plan header references a design doc, read it
3. Review critically - identify questions or concerns about plan or design alignment
4. If concerns: Raise them with your human partner before starting
5. If no concerns: Create TodoWrite and proceed
```

**Step 3: Add design reminder to Step 2**

Find "### Step 2: Execute Batch" section.

After the "For each task:" line and before the numbered list, add:

```markdown
**Design context:** If plan references design doc, keep architectural intent in mind during implementation. Check design doc if implementation questions arise.
```

**Step 4: Enhance Step 3 reporting**

Find "### Step 3: Report" section.

Current:

```markdown
When batch complete:

- Show what was implemented
- Show verification output
- Say: "Ready for feedback."
```

Replace with:

```markdown
When batch complete:

- Show what was implemented
- Show verification output
- Note any deviations from design/plan (if applicable)
- Say: "Ready for feedback."
```

**Step 5: Verify changes**

```bash
grep -A 6 "Step 1: Load and Review Plan" skills/executing-plans/SKILL.md
grep "Design context" skills/executing-plans/SKILL.md
grep "deviations from design" skills/executing-plans/SKILL.md
```

Expected: All three enhancements present

**Step 6: Commit**

```bash
git add skills/executing-plans/SKILL.md
git commit -m "feat(skills): enhance executing-plans to reference and validate against designs"
```

---

## Task 5: Enhance subagent-driven-development Skill - Load and Dispatch

**Files:**

- Modify: `skills/subagent-driven-development/SKILL.md`

**Step 1: Read current Step 1 and Step 2 sections**

Read `skills/subagent-driven-development/SKILL.md` lines 35-62 (Steps 1-2)

**Step 2: Enhance Step 1 with design check**

Find "### 1. Load Plan" section.

Current:

```markdown
Read plan file, create TodoWrite with all tasks.
```

Replace with:

```markdown
1. Read plan file
2. **Check for design document**: If plan header references a design doc, read it
3. Create TodoWrite with all tasks
4. Note: Design doc (if exists) will be passed to implementation subagents for architectural context
```

**Step 3: Enhance Step 2 subagent dispatch**

Find the "**Dispatch fresh subagent:**" section in Step 2.

Current prompt:

```markdown
Task tool (general-purpose):
description: "Implement Task N: [task name]"
prompt: |
You are implementing Task N from [plan-file].

    Read that task carefully. Your job is to:
    1. Implement exactly what the task specifies
    2. Write tests (following TDD if task says to)
    3. Verify implementation works
    4. Commit your work
    5. Report back

    Work from: [directory]

    Report: What you implemented, what you tested, test results, files changed, any issues
```

Replace with:

```markdown
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

**Step 4: Enhance Step 3 code review**

Find the "### 3. Review Subagent's Work" section.

After the code-reviewer tool template block, add:

```markdown
**If design doc exists:** Include design doc path in code review context:
```

DESIGN_DOC: docs/designs/YYYY-MM-DD-feature-design.md

```

Code reviewer checks alignment with both plan and design.
```

**Step 5: Verify changes**

```bash
grep -A 5 "1. Load Plan" skills/subagent-driven-development/SKILL.md
grep "Design Context" skills/subagent-driven-development/SKILL.md
grep "DESIGN_DOC" skills/subagent-driven-development/SKILL.md
```

Expected: All three enhancements present

**Step 6: Commit**

```bash
git add skills/subagent-driven-development/SKILL.md
git commit -m "feat(skills): enhance subagent-driven-development to use design docs"
```

---

## Task 6: Enhance documentation-management Skill - Retcon Writing and Design Docs

**Files:**

- Modify: `skills/documentation-management/SKILL.md`

**Step 1: Read current Essential Rules and Documentation Types sections**

Read `skills/documentation-management/SKILL.md` lines 63-91

**Step 2: Add retcon writing to Essential Rules**

Find "**ALWAYS:**" section in Essential Rules.

After the "Include inline source links for factual claims..." bullet, add:

```markdown
- Use retcon writing for design docs: write in present tense as if feature exists ("The system validates..." not "The system will validate...")
```

**Step 3: Add design docs to Documentation Types**

Find "## Documentation Types" section.

After the "**Guides**:" line, add:

```markdown
- **Design** (designs/): Feature designs with architecture, components, data flow (if `docs/designs/` exists)
```

**Step 4: Add design pattern to Update Patterns table**

Find "## Update Patterns by Change Type" table.

Add row after "**Architecture change**":

```markdown
| **New feature (design phase)** | Design doc in designs/, README features, CHANGELOG |
```

**Step 5: Verify changes**

```bash
grep "retcon writing" skills/documentation-management/SKILL.md
grep "Design.*designs/" skills/documentation-management/SKILL.md
grep "New feature (design phase)" skills/documentation-management/SKILL.md
```

Expected: All three additions present

**Step 6: Commit**

```bash
git add skills/documentation-management/SKILL.md
git commit -m "feat(skills): enhance documentation-management with design docs and retcon writing"
```

---

## Task 7: Update Design Document Status

**Files:**

- Modify: `docs/designs/2025-11-03-lightweight-ddd-patterns-design.md`

**Step 1: Read current design doc header**

Read first 10 lines of `docs/designs/2025-11-03-lightweight-ddd-patterns-design.md`

**Step 2: Update status field**

Find:

```markdown
**Status:** Draft
```

Replace with:

```markdown
**Status:** Approved
```

**Step 3: Commit**

```bash
git add docs/designs/2025-11-03-lightweight-ddd-patterns-design.md
git commit -m "docs: approve lightweight DDD patterns design"
```

---

## Task 8: Final Verification

**Files:**

- All modified files

**Step 1: Verify all skill references**

```bash
echo "=== Checking skill integrations ==="
grep -l "docs/designs" skills/*/SKILL.md
echo ""
echo "=== Checking retcon writing mentions ==="
grep -l "retcon writing" skills/*/SKILL.md
echo ""
echo "=== Checking design infrastructure ==="
ls -la docs/designs/
```

Expected:

- brainstorming, writing-plans, executing-plans, subagent-driven-development, documentation-management all reference designs
- brainstorming and documentation-management mention retcon writing
- docs/designs/ contains README.md and the design doc

**Step 2: Verify graceful degradation**

Check that skills have optional checks:

```bash
grep "test -f docs/designs" skills/*/SKILL.md
grep "if exists" skills/*/SKILL.md | grep -i design
```

Expected: Multiple matches showing optional/conditional usage

**Step 3: Review commit history**

```bash
git log --oneline -8
```

Expected: 7 commits (1 infrastructure + 5 skills + 1 design status update + 1 verification)

**Step 4: Verify git status clean**

```bash
git status
```

Expected: No uncommitted changes (everything committed in tasks)

---

## Completion Checklist

- [ ] Design infrastructure created (docs/designs/README.md)
- [ ] brainstorming enhanced (Phase 4 → docs/designs/, retcon writing, approval gate)
- [ ] writing-plans enhanced (header with design link, survey step 0)
- [ ] executing-plans enhanced (load design, design context reminder, report deviations)
- [ ] subagent-driven-development enhanced (load design, pass to subagents, code review alignment)
- [ ] documentation-management enhanced (retcon writing, design doc type, update pattern)
- [ ] Design doc status updated to Approved
- [ ] All changes committed with conventional commits
- [ ] Skills reference designs optionally (graceful degradation)
- [ ] Verification tests pass

## Estimated Time

- Task 1: 30 min (design infrastructure)
- Task 2: 20 min (brainstorming)
- Task 3: 15 min (writing-plans)
- Task 4: 15 min (executing-plans)
- Task 5: 20 min (subagent-driven-development)
- Task 6: 15 min (documentation-management)
- Task 7: 5 min (design status update)
- Task 8: 15 min (verification)

**Total: ~2.5 hours** (less than estimated 6-7 hours because infrastructure already created during brainstorming, and tasks are straightforward skill enhancements)
