# Jira Burn Down Skill

A Claude Code skill that pulls issues from your Jira board and works through them systematically — implementing fixes, verifying with automated checks and code review, then producing session reports.

## Install

```bash
npx skills add PitlaneHQ/jira-burndown-skill
```

## Requirements

- [Claude Code](https://claude.ai/code) CLI
- [Atlassian MCP server](https://github.com/atlassian/mcp-server-atlassian) configured for Jira access
- A Jira Cloud project

## Setup

Add your Jira project details to your project's `CLAUDE.md`:

```markdown
**Jira project:** My Project (key: `PROJ`), Cloud ID: `your-cloud-id`, site: `yoursite.atlassian.net`
```

To find your Cloud ID, run:
```bash
curl -s https://yoursite.atlassian.net/_edge/tenant_info | jq .cloudId
```

## Two Modes

### Burn Down (continuous)

Works through the entire backlog non-stop until all issues are done or you say stop. Final report at the end.

**Trigger:** Say "burn down the backlog", "work through the issues", or "clear the board".

### Batch (5 at a time)

Works through 5 issues, produces a batch report, then pauses and waits for your go-ahead before the next 5. Final summary when you're done.

**Trigger:** Say "do a batch", "work through 5 issues", or "batch mode".

## What It Does (per issue)

1. Fetches open issues from Jira, sorted by priority
2. Presents the next issue and transitions it to "In Progress"
3. Runs a pre-change checklist (read files, check existing libs/patterns, review schema)
4. Asks for clarification if the issue lacks enough detail
5. Implements the fix or feature following your project's conventions
6. Runs lint, typecheck, and tests
7. Spawns a code-reviewer agent to verify the work
8. Fixes any problems found before marking Done
9. Transitions the issue to "Done" in Jira
10. Produces a report with all completed, skipped, and remaining issues

## Reports

### Burn Down Report
```
## Burn Down Report — 2026-03-27

### Completed (8 issues)
| Key | Summary | Type | Files Changed | Verified |
|-----|---------|------|---------------|----------|
| PROJ-12 | Fix login redirect | Bug | auth.ts, login.tsx | Yes |
| ... | ... | ... | ... | ... |

### Skipped / Blocked (1 issue)
| Key | Summary | Reason |
|-----|---------|--------|
| PROJ-15 | Add OAuth | Needs API credentials from team |

### Remaining (3 issues still open)
| Key | Summary | Status |
|-----|---------|--------|
| PROJ-18 | Dashboard redesign | To Do |
```

### Batch Report (per batch + final summary)
Same format, plus batch count and cumulative totals.

## Pre-Change Checklist

Before writing any code, the skill enforces:

1. Read the relevant files first
2. Check existing libraries — no duplicates
3. Search for existing patterns to follow
4. Check the database schema if applicable
5. No unnecessary new dependencies

## Post-Change Verification

After each issue:

- Lint must pass with zero warnings
- Typecheck must pass with zero errors
- Tests must pass (if they exist for changed areas)
- Code-reviewer agent verifies requirements, conventions, and security

## License

MIT
