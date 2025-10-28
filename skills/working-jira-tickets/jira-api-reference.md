# JIRA API Reference

Quick reference for creating JIRA tickets via Atlassian REST API.

## Critical: Avoid Common Pitfalls

1. **Never use backslash line breaks (`\`)** - They fail in bash subprocesses. Use single-line or JSON variables.
2. **Use `-s` flag** - Suppresses curl progress output
3. **Use Basic auth header** - Keeps API credentials out of Claude's context for better security

**Working pattern:**
```bash
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" "https://..."
```

## Authentication

**Required environment variables:**
```bash
JIRA_BASIC_AUTH    # Base64 encoded: echo -n "email:api_key" | base64
JIRA_DOMAIN        # e.g., "company.atlassian.net"
```

**Setup instructions:**
1. Get API token from https://id.atlassian.com/manage/api-tokens
2. Generate base64 auth: `echo -n "your-email@company.com:YOUR_API_TOKEN" | base64`
3. Set in your environment: `export JIRA_BASIC_AUTH="generated_base64_string"`

**Note:** Do not test authentication proactively. Assume environment variables are set correctly unless API calls fail.

## Create Issue

```bash
PAYLOAD='{
  "fields": {
    "project": {"key": "PROJECT"},
    "summary": "Issue Title",
    "description": {
      "type": "doc",
      "version": 1,
      "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Description"}]}]
    },
    "issuetype": {"name": "Story"}
  }
}'

curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" -X POST -H "Content-Type: application/json" -d "$PAYLOAD" "https://${JIRA_DOMAIN}/rest/api/3/issue"
```

**Issue types:** Story, Task, Bug, Epic

**Optional fields:**
```json
"parent": {"key": "EPIC-123"}        // Link to epic
"priority": {"name": "Medium"}       // Low, Medium, High, Critical
```

## Link Issues

```bash
PAYLOAD='{"type": {"name": "Relates"}, "inwardIssue": {"key": "ISSUE-1"}, "outwardIssue": {"key": "ISSUE-2"}}'
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" -X POST -H "Content-Type: application/json" -d "$PAYLOAD" "https://${JIRA_DOMAIN}/rest/api/3/issueLink"
```

**Get link types:**
```bash
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" "https://${JIRA_DOMAIN}/rest/api/3/issueLinkType" | jq '.issueLinkTypes[] | {name, inward, outward}'
```

## Get Issue

```bash
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" "https://${JIRA_DOMAIN}/rest/api/3/issue/ISSUE-123"
```

## Update Issue

```bash
# Update description
PAYLOAD='{"fields": {"description": {"type": "doc", "version": 1, "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Updated description"}]}]}}}'
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" -X PUT -H "Content-Type: application/json" -d "$PAYLOAD" "https://${JIRA_DOMAIN}/rest/api/3/issue/ISSUE-123"

# Change parent epic
PAYLOAD='{"fields": {"parent": {"key": "EPIC-456"}}}'
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" -X PUT -H "Content-Type: application/json" -d "$PAYLOAD" "https://${JIRA_DOMAIN}/rest/api/3/issue/ISSUE-123"

# Update status (use transition ID from Get Transitions)
PAYLOAD='{"transition": {"id": "31"}}'
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" -X POST -H "Content-Type: application/json" -d "$PAYLOAD" "https://${JIRA_DOMAIN}/rest/api/3/issue/ISSUE-123/transitions"
```

**Get available transitions:**
```bash
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" "https://${JIRA_DOMAIN}/rest/api/3/issue/ISSUE-123/transitions" | jq '.transitions[] | {id, name}'
```

## Add Comment

```bash
# Add a comment
PAYLOAD='{"body": {"type": "doc", "version": 1, "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Progress update here"}]}]}}'
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" -X POST -H "Content-Type: application/json" -d "$PAYLOAD" "https://${JIRA_DOMAIN}/rest/api/3/issue/ISSUE-123/comment"

# Update a comment (use comment ID from Get Comments)
PAYLOAD='{"body": {"type": "doc", "version": 1, "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Updated comment"}]}]}}'
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" -X PUT -H "Content-Type: application/json" -d "$PAYLOAD" "https://${JIRA_DOMAIN}/rest/api/3/issue/ISSUE-123/comment/12345"

# Delete a comment
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" -X DELETE "https://${JIRA_DOMAIN}/rest/api/3/issue/ISSUE-123/comment/12345"
```

**Get comments:**
```bash
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" "https://${JIRA_DOMAIN}/rest/api/3/issue/ISSUE-123/comment" | jq '.comments[] | {id, created, author: .author.displayName, body: .body.content[0].content[0].text}'
```

## Search Issues (JQL)

```bash
# Get my open tickets
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" "https://${JIRA_DOMAIN}/rest/api/3/search/jql?jql=assignee=currentUser()+AND+statusCategory!=Done&fields=key,summary,status&maxResults=20"

# Get tickets where I'm a pair
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" "https://${JIRA_DOMAIN}/rest/api/3/search/jql?jql=statusCategory!=Done+AND+pair=currentUser()+ORDER+BY+created+DESC&fields=key,summary,status&maxResults=20"

# Custom JQL query
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" "https://${JIRA_DOMAIN}/rest/api/3/search/jql?jql=YOUR_JQL_HERE&fields=key,summary,status&maxResults=50"
```

**Common JQL patterns:**
- `assignee=currentUser()` - Assigned to me
- `statusCategory!=Done` - Open tickets
- `pair=currentUser()` - I'm a pair (custom field)
- `project=DPLAT` - Specific project
- `ORDER BY created DESC` - Sort by creation date

## Get Project Metadata

```bash
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" "https://${JIRA_DOMAIN}/rest/api/3/issue/createmeta?projectKeys=PROJECT"
```

## Atlassian Document Format (ADF)

JIRA descriptions use ADF. Basic building blocks:

**Paragraph:**
```json
{"type": "paragraph", "content": [{"type": "text", "text": "Text here"}]}
```

**Bold text:**
```json
{"type": "text", "text": "Bold text", "marks": [{"type": "strong"}]}
```

**Bullet list:**
```json
{
  "type": "bulletList",
  "content": [
    {"type": "listItem", "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Item"}]}]}
  ]
}
```

**Complete structure:**
```json
{
  "type": "doc",
  "version": 1,
  "content": [
    {"type": "paragraph", "content": [{"type": "text", "text": "Paragraph"}]},
    {
      "type": "bulletList",
      "content": [
        {"type": "listItem", "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Bullet"}]}]}
      ]
    }
  ]
}
```

**For ticket content standards:** See [SKILL.md](SKILL.md) for organization-specific format (Description, DoD, Implementation Details).

## Troubleshooting

| Error | Fix |
|-------|-----|
| `Client must be authenticated` | Check env vars are set (see below); regenerate token; restart session |
| `Field 'X' cannot be set` | Check project metadata: `/rest/api/3/issue/createmeta?projectKeys=PROJECT` |
| `description: markup unsupported` | Verify valid ADF JSON structure |
| `parent issue key invalid` | Verify epic exists and is in same project |

**Check if environment variables are set:**
```bash
env | grep -E '^(JIRA_BASIC_AUTH|JIRA_DOMAIN)='
```

**Test authentication (only if experiencing auth errors):**
```bash
curl -s -H "Authorization: Basic ${JIRA_BASIC_AUTH}" "https://${JIRA_DOMAIN}/rest/api/3/myself"
```

If this returns your user information, authentication is working correctly.

## API Documentation

- REST API v3: https://developer.atlassian.com/cloud/jira/platform/rest/v3/
- ADF Format: https://developer.atlassian.com/cloud/jira/platform/apis/document/structure/
