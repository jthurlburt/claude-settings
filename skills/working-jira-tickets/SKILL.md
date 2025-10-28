---
name: Working Jira Tickets
description: Use when working with JIRA tickets - creating well-structured stories and tasks, splitting large stories, updating status, adding progress comments, linking related issues, and searching for tickets. Ensures clear DoD, appropriate sprint scope, and measurable acceptance criteria
---

# JIRA Ticket Creation

## Overview

Create well-structured JIRA tickets with clear requirements, specific success criteria, and appropriate scope. Good tickets enable efficient pickup and unambiguous validation.

**Core principle:** Specific, measurable Definition of Done for single-sprint scope.

## When to Use

- Creating new stories or tasks
- Requirements feel vague ("improve performance", "fix bugs")
- Story DoD has 10+ items (needs splitting)
- Converting informal requirements into tickets
- Before sprint planning

## Ticket Type Selection

| Type | Use When | Examples |
|------|----------|----------|
| **Story** | Code changes requiring testing/deployment | API changes, UI features, migrations |
| **Task** | No code deployment needed | Documentation, discovery, data analysis |

## Story Structure (3 Required Sections)

### 1. Description

**Format:** 1-3 sentences: what + why

```
✅ Implement Pydantic models for config files to catch validation
errors at startup. Currently getting silent failures on typos.

❌ Add validation (no context)
```

### 2. Definition of Done (DoD)

**Format:** Bullet list of specific, measurable outcomes (NOT implementation steps)

```
✅ Good (outcomes):
* Config files validate on startup
* Invalid configs produce clear error with field name
* All existing configs pass validation
* Unit tests cover each config type

❌ Bad (implementation):
* Use Pydantic BaseModel
* Create schema for each file
* Add try/catch blocks
```

**DoD characteristics:**
- Specific and measurable
- Outcome-focused
- Includes code + tests + docs
- 3-7 items (if more, split story)

### 3. Implementation Details (Optional)

**Use for:** Doc links, constraints, gotchas, related tickets

```
✅ See Databricks docs: [link]
   Requires S3:PutBucketNotification permission
   Related: DPLAT-823

❌ Step-by-step implementation instructions
```

## Title Format

**Pattern:** `[Verb] + [Object] + [Context]`

```
✅ Implement Configuration Validation with Pydantic
✅ Switch Auto Loader to File Notification Mode

❌ Configuration (no verb)
❌ URGENT: CRITICAL BUG!!! (use Priority field)
```

**Use imperative verbs:** Implement, Add, Switch, Remove, Update, Fix

## Story Sizing

**Right-sized:** 2-5 days, single responsibility, demo-able, fits one sprint

**Too large signs:**
- Multiple "and"s in description
- DoD has 15+ items
- Spans multiple layers

**Split strategies:**
1. By functionality (Registration → Profile → Deletion)
2. By layer (API → UI → Caching)
3. By scenario (Single upload → Bulk → Progress)

## Default Settings

| Field | Default | When to Change |
|-------|---------|----------------|
| **Priority** | Medium | Critical: outage/security; High: blocking work |
| **Sizing** | Empty | Fill during sprint refinement, not creation |
| **Parent** | Fill if known | Link to epic for context |

## Linking Related Work

**When to link:**
- Splitting one story into multiple stories
- Creating multiple related tickets
- Work has dependencies

**Link types:**
- **Relates**: General relationship, use when unsure (default)
- **Blocks**: Hierarchical dependency (this ticket blocks another / is blocked by another)
- **Duplicate**: Duplicate tickets
- **Cloners**: Cloned from another ticket

**After creating multiple tickets:** Link them together for traceability. See [jira-api-reference.md](jira-api-reference.md#link-issues) for API linking.

## Updating Issue Status

**When user starts work:** Update status using transitions API (NOT comments)

**Process:**
1. Get available transitions for the issue to find the "In Progress" transition ID
2. Execute the transition to update status

**Important:** Status changes are done via transitions, not by adding comments. See [jira-api-reference.md](jira-api-reference.md#update-issue) for API details.

## Progress Updates via Comments

**When to add comments:**
- Completing a milestone or checkpoint with substantial progress
- Encountering blockers or changes in approach
- Linking to external work (PRs, docs, branches) with context
- End of day updates summarizing work done

**Comment format:** Concise 1-3 sentences + bullet points

```
✅ Good:
Completed schema migration and validation logic.
* PR: https://github.com/org/repo/pull/123
* Updated schema in src/models/config.py:45-120
* Added 15 validation tests covering all config types
* Next: Integration testing with production configs

❌ Bad:
Working on it. Making progress. Will update later.
(Too vague, no specifics or links)
```

**Guidelines:**
- Only add comments when there's substance to share
- Include links to PRs, commits, files, documentation
- List specific accomplishments or next steps
- Keep bullet points to 3-5 items max
- Don't comment just to say "started work" - update status instead

**API:** See [jira-api-reference.md](jira-api-reference.md#add-comment) for comment creation/update/deletion.

## Quality Checklist

- [ ] Title: imperative verb, specific, <60 chars
- [ ] Description: 1-3 sentences, explains what and why
- [ ] DoD: outcome-focused, specific, measurable, 3-7 items
- [ ] Implementation Details: links/constraints (if helpful)
- [ ] Story fits one sprint
- [ ] Ticket type matches work (Story=code, Task=docs)

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| **Vague** ("Fix login issues") | Specific ("Fix login timeout for SSO users >8KB SAML") |
| **Implementation as DoD** ("Create UserService class") | Outcomes ("Passwords hashed with bcrypt") |
| **Scope creep** ("Add auth and authorization and...") | Split into multiple stories |
| **Missing context** ("Switch to file notification mode") | Why ("...to reduce CPU by 80%") |

## Complete Example

```
Title: Switch Auto Loader to File Notification Mode

Description: Switch auto loader from directory scanning to file
notification mode to reduce cluster CPU. Directory scans cause
CPU spikes every 30s affecting other workloads.

Definition of Done:
* All auto loader configs use file notification mode
* S3 event notifications configured for monitored buckets
* CPU usage reduced by >80% during file arrival
* No files missed during transition (verified with test dataset)
* Monitoring dashboard tracks notification lag
* Runbook updated with troubleshooting steps

Implementation Details:
See Databricks docs: https://docs.databricks.com/...
Requires S3:PutBucketNotification. Coordinate with platform team.
Test in dev first with small dataset.

Priority: Medium
Parent: DPLAT-760
Connected: Relates to DPLAT-801, Blocked by DPLAT-799
```

## When Story Too Large

**If DoD >10 items or multiple "and"s: STOP. Split.**

1. Identify independently valuable pieces
2. Create multiple focused stories
3. Link with "relates to"
4. Consider creating Epic if part of larger initiative

## API Reference

For all JIRA API interactions using curl commands, see [jira-api-reference.md](jira-api-reference.md).

**Available operations:**
- [Authentication setup and testing](jira-api-reference.md#authentication)
- [Create issues (Stories, Tasks)](jira-api-reference.md#create-issue)
- [Link related issues](jira-api-reference.md#link-issues)
- [Update issues (description, parent, fields)](jira-api-reference.md#update-issue)
- [Transition status (To Do → In Progress)](jira-api-reference.md#update-issue)
- [Add/update/delete comments](jira-api-reference.md#add-comment)
- [Search issues with JQL](jira-api-reference.md#search-issues-jql)
- [Get issue details](jira-api-reference.md#get-issue)
- [ADF (Atlassian Document Format) reference](jira-api-reference.md#atlassian-document-format-adf)

**Reference jira-api-reference.md for correct curl commands using JIRA REST API v3.**

## Related Skills

- **superpowers:writing-plans**: Convert ticket to implementation plan
- **superpowers:verification-before-completion**: Verify DoD before closing
