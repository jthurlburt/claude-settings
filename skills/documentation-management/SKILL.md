---
name: Documentation Management
description: Use when documentation is outdated after code changes - systematically updates README, CHANGELOG, API docs, and guides based on actual implementation changes
---

# Documentation Management

## Overview

Systematically update project documentation to match code reality. Analyze changes, identify affected documentation, and update all relevant files maintaining consistency across README, CHANGELOG, API docs, and guides.

## When to Use

- After implementing features
- After fixing bugs
- After refactoring or architecture changes
- When documentation is outdated across multiple files
- Before creating pull requests

## Core Approach

**Analyze changes → Identify affected docs → Update systematically → Verify consistency**

1. **Understand scope**: Analyze code changes from conversation or codebase comparison
2. **Read all docs**: Use Glob to find markdown files, Read each documentation file completely
3. **Identify gaps**: Compare implementation vs documentation (new features, changed APIs, removed functionality)
4. **Update comprehensively**: Modify all affected docs, not just README

## Documentation Modes

**Overview mode** (when starting): Glob markdown files, read each, present summary with gaps

**Sync mode** (during work): Update docs incrementally, CHANGELOG with changes, README for features

**Session mode** (after coding): Review conversation, group by type (Added, Fixed, Changed), update all affected docs

## Update Patterns by Change Type

| Change Type                    | Documentation Updates                                                   |
| ------------------------------ | ----------------------------------------------------------------------- |
| **New feature**                | README features, CHANGELOG (minor bump), API docs, usage examples       |
| **New feature (design phase)** | Design doc in designs/, README features, CHANGELOG                      |
| **Bug fix**                    | CHANGELOG (patch bump), troubleshooting if significant                  |
| **Refactoring**                | Architecture docs, migration guide if breaking, CHANGELOG               |
| **Architecture change**        | README architecture section, CHANGELOG, ADR in decisions/ (if exists)   |
| **Security fix**               | Security policy, CHANGELOG with Security section, README if user-facing |
| **Performance**                | Benchmarks, CHANGELOG with improved metrics, optimization guides        |
| **Breaking change**            | CHANGELOG (major bump), migration guide, README with upgrade path       |

## CHANGELOG Version Guidance

Suggest version bumps following semver:

- **MAJOR**: Breaking changes, API removals, incompatible modifications
- **MINOR**: New features, enhancements, deprecations (backward compatible)
- **PATCH**: Bug fixes, documentation updates, internal changes

Format follows [Keep a Changelog](https://keepachangelog.com/):

- Group by type: Added, Changed, Deprecated, Removed, Fixed, Security
- Newest entries at top
- Link to issues/PRs when available

## Essential Rules

**ALWAYS:**

- Read existing docs completely before updates
- Find exact section needing update, modify in-place
- Preserve custom content and existing formatting
- Match project's documentation style and conventions
- Update all affected files, not just one
- Include inline source links for factual claims: `[claim](https://source.url)`. Unverified claims are unverifiable
- Use retcon writing for design docs: write in present tense as if feature exists ("The system validates..." not "The system will validate...")

**AVOID:**

- Deleting existing documentation
- Overwriting custom sections (respect `<!-- CUSTOM:START -->` markers)
- Changing documentation style drastically
- Adding AI attribution or Claude Code markers
- Creating unnecessary new documentation files

## Documentation Types

- **README**: Features, setup, usage, architecture
- **CHANGELOG**: Version history with categorized changes
- **API docs**: Endpoints, parameters, responses
- **Configuration**: Environment variables, settings
- **Guides**: Architecture, deployment, troubleshooting, migration
- **Design** (designs/): Feature designs with architecture, components, data flow (if `docs/designs/` exists)
- **ADR** (decisions/): Architecture decisions with rationale (if `docs/decisions/` exists)
- **DISCOVERIES** (discoveries/): Known issues and solutions (if `docs/discoveries/DISCOVERIES.md` exists)

## Red Flags - Retcon Writing Rationalizations

**If you're thinking:**

- "Future tense flows more naturally"
- "I'm too tired to fix tense"
- "I'll fix it tomorrow"
- "Both tenses are fine"

**All of these mean: Use present tense now. The rule exists for exhausted moments.**

| Excuse                         | Reality                                                                                                 |
| ------------------------------ | ------------------------------------------------------------------------------------------------------- |
| "Future tense flows naturally" | Natural doesn't mean clear. Present tense removes ambiguity about what system does vs. plans to do.     |
| "Too tired to fix"             | Exhaustion is when tense matters most. Ambiguous docs cause more questions during implementation.       |
| "Fix tomorrow"                 | Tomorrow you won't remember context. Fix takes 30 seconds now, 10 minutes later.                        |
| "Both are fine"                | Future tense creates interpretation drift. "Will validate" means different things to different readers. |

## Completion

Ask how to proceed: update all, focus on specific files, create missing docs, or generate migration guide.
