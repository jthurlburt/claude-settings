# Lightweight DDD Patterns Integration Design

**Date:** 2025-11-03
**Status:** Implemented
**Related ADRs:** [ADR-001](../decisions/001-adopt-knowledge-management.md)
**Source:** [Amplifier Analysis - Tier 2](../amplifier-analysis.md#22-lightweight-ddd-patterns-extraction-approach)

## Goal

Integrate Document-Driven Development patterns from Microsoft Amplifier project into superpowers' existing workflow. This provides DDD benefits (artifact-driven phases, approval gates, documentation-first mindset) without heavyweight workflow complexity. The system creates a document-first artifact chain where designs inform plans, plans inform implementation, and all artifacts cross-reference each other for full traceability.

## Architecture

The integration enhances five existing skills (brainstorming, writing-plans, executing-plans, subagent-driven-development, documentation-management) and creates new infrastructure (`docs/designs/` directory with design template). The system uses retcon writing technique (present tense, as-if-exists) for all design documentation and establishes explicit approval gates at phase transitions.

**Artifact Flow:**

```
User Idea
    ↓
Brainstorming (Phases 1-3: Understanding & Exploration)
    ↓
Design Doc (docs/designs/YYYY-MM-DD-feature-design.md)
    ↓ [referenced by]
Implementation Plan (docs/plans/YYYY-MM-DD-feature-plan.md)
    ↓ [executed via]
Code Implementation (executing-plans or subagent-driven-development)
    ↓ [validated against]
Design Doc + Plan + Tests
```

## Components

### 1. Design Artifact Infrastructure

**Location:** `docs/designs/`
**Naming:** `YYYY-MM-DD-<feature-name>-design.md`

**Template Structure:**

- Goal (one paragraph problem/value statement)
- Architecture (high-level approach, present tense)
- Components (major parts and responsibilities)
- Data Flow (how data moves through system)
- Error Handling (failure modes and recovery)
- Testing Strategy (unit/integration/e2e approach)
- Open Questions (TBD items)
- Design Decisions (key choices with rationale)
- Links (Related ADRs, source inspiration)

**Status Values:** Draft | Approved | Implemented | Superseded

### 2. Skill Enhancements

#### brainstorming (Phase 4 modification)

**Current behavior:** Creates design documents in `docs/plans/`

**New behavior:**

- Phase 4 creates design docs in `docs/designs/` (not plans)
- Adds retcon writing guidance section before template
- Uses design template (Goal, Architecture, Components, Data Flow, Error Handling, Testing Strategy, Open Questions, Design Decisions)
- Adds approval gate: "Design document created. Review before proceeding to worktree setup?"
- Phase 6 still creates implementation plans in `docs/plans/`

**Retcon Writing Guidance:**

```markdown
Write the design in present tense as if the feature already exists:

- ✅ "The system validates inputs before processing"
- ❌ "The system will validate inputs before processing"
- ✅ "Authentication uses JWT tokens"
- ❌ "Authentication will use JWT tokens"

This reduces hedging, makes designs more concrete, and easier to review.
```

#### writing-plans (header modification)

**Current header:**

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans

**Goal:** [One sentence]
**Architecture:** [2-3 sentences]
**Tech Stack:** [Key technologies]
```

**Enhanced header:**

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Design:** [../designs/YYYY-MM-DD-feature-design.md](../designs/YYYY-MM-DD-feature-design.md) _(if exists)_

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

**New step in Survey Existing Patterns:**

````markdown
0. **Check for design document**: If this plan is being created from a design phase, reference it
   ```bash
   test -f docs/designs/*-feature-design.md && echo "Design exists"
   ```
````

Read the design document to understand the intended architecture before planning implementation details.

````

#### executing-plans (Step 1 enhancement)

**Current Step 1:**
1. Read plan file
2. Review critically - identify any questions or concerns about the plan
3. If concerns: Raise them with your human partner before starting
4. If no concerns: Create TodoWrite and proceed

**Enhanced Step 1:**
1. Read plan file
2. **Check for design document**: If plan references a design doc in header, read it
3. Review critically - identify questions or concerns about plan or design alignment
4. If concerns: Raise them with your human partner before starting
5. If no concerns: Create TodoWrite and proceed

**New reminder in Step 2 (Execute Batch):**
"If implementation questions arise, check design doc (if referenced in plan header) for architectural intent."

**Enhanced Step 3 (Report):**
"When batch complete: Show what was implemented, verification output, and note any deviations from design/plan. Say: 'Ready for feedback.'"

#### subagent-driven-development (Step 1 enhancement)

**Current Step 1:** Read plan file, create TodoWrite with all tasks.

**Enhanced Step 1:**
1. Read plan file
2. **Check for design document**: If plan references a design doc, read it
3. Create TodoWrite with all tasks
4. Note: Design doc (if exists) will be passed to implementation subagents

**Enhanced Step 2 (Dispatch):**
When dispatching implementation subagent, include design doc in context if it exists:

```markdown
Task tool (general-purpose):
  prompt: |
    You are implementing Task N from [plan-file].

    **Design Context:** [Read docs/designs/YYYY-MM-DD-feature-design.md for architectural intent]

    Read the task carefully. Your job is to:
    1. Implement exactly what the task specifies
    2. Follow architecture from design doc
    3. Write tests (following TDD if task says to)
    4. Verify implementation works
    5. Commit your work
    6. Report back
````

**Enhanced Step 3 (Review):**
Code reviewer receives design doc path and checks alignment:

```markdown
DESIGN_DOC: docs/designs/YYYY-MM-DD-feature-design.md (if exists)
```

#### documentation-management (two additions)

**Addition 1 - Retcon Writing in Essential Rules:**

In "ALWAYS:" section, add:

```markdown
- Use retcon writing for design docs: write in present tense as if feature exists ("The system validates..." not "The system will validate...")
```

**Addition 2 - Design Docs in Documentation Types:**

Add to list:

```markdown
- **Design** (designs/): Feature designs with architecture, components, data flow (if `docs/designs/` exists)
```

**Addition 3 - Update Patterns Table:**

Add row:

```markdown
| **New feature (design phase)** | Design doc in designs/, README features, CHANGELOG |
```

### 3. Approval Gates

**Existing gates (preserved):**

- Brainstorming Phase 3 → Phase 4: Implicit validation during design presentation
- Brainstorming Phase 6: "Ready to create the implementation plan?"
- Executing-plans batch boundaries: Report and wait for feedback
- Subagent-driven-development: Code review after each task

**New explicit gates (added):**

- Brainstorming Phase 4: "Design document created. Review before proceeding to worktree setup?"
- Writing-plans completion: "Plan created and saved. Review before starting implementation?"
- Executing-plans batch completion: "Batch complete. Verify against design/plan before continuing?"

### 4. Cross-Linking Patterns

**Design docs link to:**

- Related ADRs in header: `**Related ADRs:** [ADR-NNN](../decisions/NNN-title.md)`
- Source inspiration in header: `**Source:** [Amplifier Analysis](../amplifier-analysis.md#section)`
- Prior art in Design Decisions section

**Plans link to:**

- Design doc in header: `**Design:** [../designs/YYYY-MM-DD-feature-design.md](...)`
- Related ADRs discovered in survey
- Existing code patterns in "Existing Patterns Survey"

**Code commits reference:**

- Plan task number: `feat(auth): implement JWT validation (Task 3)`
- Design doc in PR description (via finishing-a-development-branch)

**Documentation updates reference:**

- Design docs when updating architecture sections
- Plan docs when updating implementation details

### 5. Directory Structure

```
docs/
├── decisions/              # ADRs (existing, Tier 1)
│   ├── README.md
│   └── NNN-title.md
├── discoveries/            # Known issues (existing, Tier 1)
│   └── DISCOVERIES.md
├── designs/                # Feature designs (NEW, Tier 2)
│   ├── README.md          # Template and guidance
│   └── YYYY-MM-DD-feature-design.md
├── plans/                  # Implementation plans (existing)
│   └── YYYY-MM-DD-feature-plan.md
└── amplifier-analysis.md   # Source material (existing)
```

## Data Flow

### Scenario 1: Full Workflow (Brainstorm → Design → Plan → Execute)

1. User provides idea to brainstorm skill
2. Brainstorming Phases 1-3: Understanding, exploration, design presentation
3. Brainstorming Phase 4: Creates `docs/designs/YYYY-MM-DD-feature-design.md` using retcon writing
4. Approval gate: User reviews design doc
5. Brainstorming Phase 5: Sets up worktree (if implementing now)
6. Brainstorming Phase 6: Calls writing-plans skill
7. Writing-plans reads design doc, surveys patterns, creates `docs/plans/YYYY-MM-DD-feature-plan.md` with design reference in header
8. Approval gate: User reviews plan
9. User chooses: executing-plans (human-in-loop) or subagent-driven-development (automated)
10. Implementation skill reads both design and plan, executes tasks, verifies against both
11. Batch/task completion: Approval gates ensure alignment before proceeding
12. finishing-a-development-branch creates PR with references to design and plan

### Scenario 2: Plan-Only Workflow (Skip Design)

1. User requests implementation plan without brainstorming
2. Writing-plans checks for design doc (none exists)
3. Plan created without design reference
4. Execution proceeds as before (no design doc to reference)
5. System degrades gracefully - works without designs

### Scenario 3: Design-Only Workflow (Explore, Don't Implement)

1. Brainstorming through Phase 4: Design doc created
2. User decides not to implement yet
3. Design doc remains in `docs/designs/` with Status: Draft
4. Future implementation can reference it
5. Design serves as decision record for architectural exploration

## Error Handling

**Missing design doc:**

- Plans check if design exists but don't require it
- Execution skills reference design if present, skip if absent
- System works with or without design artifacts (backward compatible)

**Stale design doc:**

- Design Status field tracks lifecycle (Draft → Approved → Implemented → Superseded)
- Plans can reference newer designs, updating Status of old ones
- Documentation-management handles design doc updates

**Conflicting design and plan:**

- Execution skills prioritize plan (more specific) but note conflicts
- Code reviewer checks alignment with both, flags mismatches
- Approval gates catch discrepancies before implementation continues

**Broken cross-links:**

- Use relative paths `../designs/...` and `../decisions/...`
- Git preserves structure, links remain valid
- If file missing, graceful degradation (continue without reference)

## Testing Strategy

**Template validation:**

- Create design doc manually using template
- Verify all sections present and make sense

**Skill integration testing:**

- **brainstorming**: Run through Phase 4, verify design doc created in `docs/designs/` with retcon writing
- **writing-plans**: Create plan referencing a design doc, verify header contains design link
- **executing-plans**: Execute plan with design reference, verify Step 1 reads design
- **subagent-driven-development**: Execute plan with design reference, verify subagents receive design context
- **documentation-management**: Update docs after feature implementation, verify design docs included

**Workflow testing:**

- **Full workflow**: Brainstorm → Design → Plan → Execute, verify artifact chain
- **Plan-only**: Create plan without design, verify graceful degradation
- **Design-only**: Create design without implementation, verify standalone value

**Cross-linking verification:**

- Create design with ADR reference, verify link works
- Create plan with design reference, verify link works
- Check relative paths work from different locations

## Open Questions

None - design is complete based on amplifier analysis recommendations and existing skill patterns.

## Design Decisions

### Decision 1: Enhance Existing Skills vs New Orchestrator Skill

**Chosen:** Enhance existing skills

**Rationale:**

- User preference for enhancing existing workflows
- Skills already work together via explicit calls
- New orchestrator would duplicate brainstorming/executing-plans logic
- Amplifier's DDD is heavyweight 5-phase workflow; we want lightweight patterns

**Trade-off:** More files to modify (5 skills) vs creating 1 new skill, but better integration

### Decision 2: Design Docs in `docs/designs/` vs `docs/plans/`

**Chosen:** Separate `docs/designs/` directory

**Rationale:**

- User specified `docs/designs/` location
- Clear separation: designs are architectural, plans are implementation tasks
- Allows design-without-implementation workflow
- Plans reference designs (designs come first in artifact chain)

**Trade-off:** Another directory to maintain, but clearer organization

### Decision 3: Retcon Writing in documentation-management Only vs Multiple Skills

**Chosen:** documentation-management + brainstorming Phase 4

**Rationale:**

- documentation-management is the authoritative doc writing skill
- brainstorming Phase 4 is where design docs are created (needs technique at point of use)
- Other skills reference these two for guidance
- Avoids duplicating writing guidance across many skills

**Trade-off:** Guidance lives in two places, but those are the right two places

### Decision 4: Approval Gates - Explicit vs Implicit

**Chosen:** Mix of explicit and implicit, add explicit where missing

**Rationale:**

- Brainstorming already has implicit gates (conversation flow)
- executing-plans already has explicit gates (batch boundaries)
- Add explicit gates only where workflow transitions warrant them (design → worktree, plan → execution)
- User noted brainstorm already asks for approval between phases

**Trade-off:** Not uniform, but respects existing patterns

### Decision 5: Comprehensive vs Minimal Integration

**Chosen:** Comprehensive (user selected via AskUserQuestion)

**Rationale:**

- User wants full artifact chain with cross-linking
- Amplifier analysis shows 4-5 hour estimate for lightweight DDD
- Comprehensive pattern provides complete document-first workflow
- Worth the extra effort (6-7 hours) for full traceability

**Trade-off:** More work (6-7 hours vs 2-3), but complete solution

### Decision 6: Design Template Structure

**Chosen:** 8-section template (Goal, Architecture, Components, Data Flow, Error Handling, Testing Strategy, Open Questions, Design Decisions)

**Rationale:**

- Based on Amplifier's DDD artifacts and common design doc patterns
- Comprehensive enough for informed implementation
- Lightweight enough to avoid heavyweight documentation
- Matches information needed by writing-plans and execution skills

**Trade-off:** Could be simpler (fewer sections) or more detailed (add diagrams requirement), but this balances completeness with ease-of-use
