# Changelog

## Fork Information

**Forked from**: [obra/superpowers](https://github.com/obra/superpowers) v3.2.2
**Marketplace**: `jthurlburt/claude-settings`

This is a personal fork with additional skills and enhancements borrowed from multiple sources.

## [Unreleased]

### Added

- Knowledge management integration: Opt-in [ADR](https://adr.github.io/) (Architecture Decision Records) and DISCOVERIES patterns from [Microsoft Amplifier](https://github.com/microsoft/amplifier) (2025-11-03)
  - `/setup-knowledge-management` slash command with embedded templates
  - 9 skills updated to integrate with `docs/decisions/` and `docs/discoveries/` when present
  - Skills fall back to `mem` for personal tracking when patterns not enabled
  - Comprehensive integration documented in `docs/decisions/001-adopt-knowledge-management.md`
- Lightweight DDD (Document-Driven Development) patterns integration (2025-11-03)
  - Design artifact infrastructure in `docs/designs/` with comprehensive README template
  - 5 workflow skills enhanced to create, reference, and validate design documents
  - Retcon writing technique (present tense) for design docs
  - Rationalization defenses (red flags and tables) to prevent skipping design context
  - Graceful degradation when design docs absent (backward compatible)
- Background process cleanup protocols for all subagent workflows (2025-11-04)
  - Mandatory cleanup steps with imperative language (Authority principle)
  - Social proof patterns ("Every time" language for universal behaviors)
  - Anti-rationalization tables preemptively countering common excuses
  - Process hygiene checks added to code review template

## Attribution

Skills and concepts borrowed from:

- [obra/superpowers](https://github.com/obra/superpowers) - Base framework and core skills
- [superpowers-skills](https://github.com/obra/superpowers-skills) - Problem-solving patterns
- [CCPlugins](https://github.com/brennercruvinel/CCPlugins) - Development workflow skills
- [claude-codex-settings](https://github.com/fcakyon/claude-codex-settings/tree/main) - Enhancement concepts and hooks
- [Microsoft Amplifier](https://github.com/microsoft/amplifier) - Ambiguity and tension management patterns

## Added Skills

**From superpowers-skills:**

- `simplification-cascades` - Find unifying principles that eliminate components
- `collision-zone-thinking` - Force unrelated concepts together for innovation
- `meta-pattern-recognition` - Spot universal patterns across domains
- `inversion-exercise` - Flip assumptions to reveal alternatives
- `scale-game` - Test at extremes to expose fundamental truths
- `when-stuck` - Dispatch router to appropriate problem-solving technique
- `tracing-knowledge-lineages` - Understand technical decision evolution
- `preserving-productive-tensions` - Recognize when to preserve multiple approaches

**From CCPlugins:**

- `documentation-management` - Holistic documentation maintenance
- `predict-issues` - Proactive problem identification with risk assessment
- `code-and-project-cleanup` - Safe cleanup of code and project artifacts

**New skills:**

- `extracting-patterns-from-projects` - Systematic analysis methodology for external projects (2025-10-23)
- `enhancing-superpowers` - Project-specific integration guide for superpowers (2025-10-23)

## Enhanced Skills

Skills with added capabilities from concept extraction:

**brainstorming:**

- External source adaptation pattern
- Progressive reasoning framework (steel man alternatives, bias checking)
- Design documentation phase with retcon writing technique (2025-11-03)

**systematic-debugging:**

- Auto-fix patterns with skill linkage for common test failures

**predict-issues:**

- Security-specific risk analysis (exploitability, exposure, blast radius)

**executing-plans:**

- Continuous validation between tasks (type checks, import validation)
- Design document loading and context awareness (2025-11-03)
- Rationalization defenses for skipping design docs (2025-11-03)

**finishing-a-development-branch:**

- Documentation synchronization check
- Optional commit history cleanup guidance

**writing-plans:**

- Existing pattern survey before planning
- Design document references in plan headers (2025-11-03)

**receiving-code-review:**

- Pattern alignment checks before implementing feedback

**dispatching-parallel-agents:**

- Generalized from debugging-only to cover research, analysis, and any parallel tasks (2025-10-23)
- Background process cleanup protocols with anti-rationalization defenses (2025-11-04)

**executing-plans:**

- Background process cleanup at completion step with mandatory language (2025-11-04)

**subagent-driven-development:**

- Design document context passed to implementation subagents (2025-11-03)
- Rationalization defenses for architectural alignment (2025-11-03)
- Background process cleanup protocols with persuasion principles (2025-11-04)

**requesting-code-review:**

- Mandatory cleanup after code reviewer subagents complete (2025-11-04)
- Process hygiene added to code review checklist template (2025-11-04)

**testing-skills-with-subagents:**

- CLEANUP phase added to TDD cycle with resource waste warnings (2025-11-04)

**documentation-management:**

- Retcon writing guidance for design docs (2025-11-03)
- Design doc type added to documentation types (2025-11-03)
- Rationalization defenses for correct tense usage (2025-11-03)

## Structural Changes

- Flattened skills directory structure (removed nested categories)
- Standardized skill names to Title Case per Anthropic guidelines
- Added hooks from claude-codex-settings for session management
