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
