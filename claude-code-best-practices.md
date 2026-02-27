# Claude Code Best Practices Guide

A comprehensive guide extracted from the HumanLayer team's battle-tested Claude Code commands and workflows.

---

## Table of Contents

1. [Core Principles](#core-principles)
2. [Research Before Action](#research-before-action)
3. [Planning Workflows](#planning-workflows)
4. [Implementation Patterns](#implementation-patterns)
5. [Git & Version Control](#git--version-control)
6. [Task Tracking & Progress](#task-tracking--progress)
7. [Documentation Standards](#documentation-standards)
8. [Session Continuity & Handoffs](#session-continuity--handoffs)
9. [Debugging Strategies](#debugging-strategies)
10. [Sub-Agent Orchestration](#sub-agent-orchestration)
11. [Success Criteria Design](#success-criteria-design)
12. [Command Design Patterns](#command-design-patterns)

---

## Core Principles

### 1. Read Before You Act

**Never propose changes to code you haven't read.** This is the foundational principle:

```
- Read all mentioned files FULLY before spawning sub-tasks
- Use the Read tool WITHOUT limit/offset parameters to read entire files
- CRITICAL: DO NOT spawn sub-tasks before reading files yourself in the main context
- NEVER read files partially - if a file is mentioned, read it completely
```

### 2. Be Skeptical and Thorough

Question everything, verify with code:
- Don't assume - verify with actual codebase investigation
- If the user corrects a misunderstanding, spawn research tasks to verify
- Cross-reference requirements with actual code
- Identify discrepancies between plans and reality

### 3. Be Interactive, Not Autonomous

Work collaboratively with the user:
- Don't write full plans in one shot
- Get buy-in at each major step
- Allow course corrections
- Present understanding before taking action
- Confirm before making significant changes

### 4. Document What IS, Not What SHOULD BE

When researching:
- Describe existing code as-is without suggesting improvements
- DO NOT perform root cause analysis unless explicitly asked
- DO NOT propose enhancements unless explicitly asked
- Create technical maps/documentation of existing systems

---

## Research Before Action

### The Research Workflow

1. **Read mentioned files first** - Always in the main context
2. **Decompose the research question** - Break into composable areas
3. **Spawn parallel sub-agents** - For concurrent investigation
4. **Wait for ALL sub-tasks to complete** - Never proceed with partial results
5. **Gather metadata** - Before writing any documentation
6. **Synthesize findings** - Connect discoveries across components
7. **Generate structured documentation** - With proper references

### Specialized Research Agents

Use the right agent for each type of research:

| Agent Type | Purpose |
|------------|---------|
| `codebase-locator` | Find WHERE files and components live |
| `codebase-analyzer` | Understand HOW specific code works |
| `codebase-pattern-finder` | Find examples of existing patterns |
| `thoughts-locator` | Discover relevant documents in thoughts/ |
| `thoughts-analyzer` | Extract insights from key documents |
| `linear-ticket-reader` | Get full details of a specific ticket |
| `linear-searcher` | Find related tickets or historical context |

### Research Document Structure

```markdown
---
date: [ISO format with timezone]
researcher: [Name]
git_commit: [Current commit hash]
branch: [Current branch]
repository: [Repo name]
topic: "[Research Topic]"
tags: [research, codebase, component-names]
status: complete
last_updated: [YYYY-MM-DD]
last_updated_by: [Name]
---

# Research: [Topic]

## Research Question
[Original query]

## Summary
[High-level findings]

## Detailed Findings
### [Component/Area 1]
- Finding with reference ([file.ext:line](link))

## Code References
- `path/to/file.py:123` - Description

## Architecture Insights
[Patterns and design decisions]

## Open Questions
[Areas needing further investigation]
```

---

## Planning Workflows

### The Plan-Then-Implement Pattern

**Critical Rule**: All decisions must be made BEFORE finalizing the plan. No open questions in final plans.

### Plan Creation Steps

1. **Context Gathering**
   - Read all ticket files, research documents, related plans
   - Spawn research tasks to find related code
   - Verify understanding against actual codebase

2. **Present Informed Understanding**
   ```
   Based on the ticket and my research, I understand we need to [summary].

   I've found that:
   - [Current implementation detail with file:line reference]
   - [Relevant pattern or constraint]

   Questions my research couldn't answer:
   - [Specific question requiring human judgment]
   ```

3. **Structure Approval**
   - Present plan outline first
   - Get feedback before writing details
   - Iterate on structure before content

4. **Detailed Plan Writing** (use this template):

```markdown
# [Feature/Task Name] Implementation Plan

## Overview
[Brief description of what and why]

## Current State Analysis
[What exists, what's missing, constraints]

## Desired End State
[Specification and verification approach]

## What We're NOT Doing
[Explicitly list out-of-scope items]

## Phase 1: [Descriptive Name]

### Overview
[What this phase accomplishes]

### Changes Required
#### 1. [Component/File Group]
**File**: `path/to/file.ext`
**Changes**: [Summary]

### Success Criteria

#### Automated Verification:
- [ ] Migration applies cleanly: `make migrate`
- [ ] Unit tests pass: `make test`
- [ ] Type checking passes: `npm run typecheck`

#### Manual Verification:
- [ ] Feature works as expected in UI
- [ ] No regressions in related features

**Implementation Note**: Pause for manual confirmation before Phase 2.
```

### Plan Iteration

When updating plans:
- Make **surgical edits**, not wholesale rewrites
- Preserve content that doesn't need changing
- Only research what's necessary for specific changes
- Verify technical feasibility with code research

---

## Implementation Patterns

### Following the Plan

1. **Read the plan completely** - Check for existing checkmarks
2. **Read all mentioned files fully** - Never use limit/offset
3. **Create todo list** to track progress
4. **Implement each phase fully** before moving to next
5. **Update checkboxes** as you complete sections
6. **Pause for human verification** after each phase

### Phase Completion Format

```
Phase [N] Complete - Ready for Manual Verification

Automated verification passed:
- [List automated checks that passed]

Please perform manual verification steps:
- [List manual items from plan]

Let me know when complete to proceed to Phase [N+1].
```

### When Things Don't Match

If reality differs from the plan:

```
Issue in Phase [N]:
Expected: [what plan says]
Found: [actual situation]
Why this matters: [explanation]

How should I proceed?
```

### Common Implementation Patterns

**Database Changes:**
1. Schema/migration first
2. Add store methods
3. Update business logic
4. Expose via API
5. Update clients

**New Features:**
1. Research existing patterns
2. Start with data model
3. Build backend logic
4. Add API endpoints
5. Implement UI last

**Refactoring:**
1. Document current behavior
2. Plan incremental changes
3. Maintain backwards compatibility
4. Include migration strategy

---

## Git & Version Control

### Commit Best Practices

1. **Review changes first**
   ```bash
   git status
   git diff
   ```

2. **Plan logical commits**
   - Group related changes together
   - Keep commits focused and atomic
   - Use imperative mood in messages
   - Focus on WHY, not just WHAT

3. **Never use broad staging**
   - NO `git add -A`
   - NO `git add .`
   - Add specific files by name

4. **Exclude from commits:**
   - `thoughts/` directory (unless syncing)
   - Dummy files and test scripts
   - Auto-generated code
   - Secrets (`.env`, credentials)

5. **Present plan before executing**
   ```
   I plan to create [N] commit(s):

   Commit 1: [files] - [message]
   Commit 2: [files] - [message]

   Shall I proceed?
   ```

### PR Description Template

```markdown
## Summary
[2-3 sentence summary of the problem/goal]

## Key Details
- [Important technical details]
- [Decisions and constraints]

## Implementation Notes
[Specific approach or steps]

## How to Verify
- [ ] Automated: `make check test`
- [ ] Manual: [specific steps]

## References
- Source: `thoughts/path/to/document.md`
- Related ticket: [link]
```

### Worktree Workflow

For parallel implementations:
1. Create worktree with ticket branch name
2. Copy settings: `.claude/settings.local.json`
3. Run setup in worktree
4. Launch implementation session

---

## Task Tracking & Progress

### Using TodoWrite Effectively

**When to use:**
- Complex multi-step tasks (3+ steps)
- User provides multiple tasks
- After receiving new instructions
- When starting work on a task
- After completing a task

**Task States:**
- `pending` - Not yet started
- `in_progress` - Currently working (limit to ONE at a time)
- `completed` - Finished successfully

**Critical Rules:**
- Mark todos complete IMMEDIATELY after finishing
- Exactly ONE task should be in_progress at any time
- Only mark complete when FULLY accomplished
- If blocked, keep as in_progress and create new task for blocker

### Task Description Format

Always provide both forms:
```json
{
  "content": "Fix authentication bug",
  "activeForm": "Fixing authentication bug",
  "status": "in_progress"
}
```

---

## Documentation Standards

### File Naming Conventions

**Plans:**
```
thoughts/shared/plans/YYYY-MM-DD-ENG-XXXX-description.md
thoughts/shared/plans/2025-01-08-ENG-1478-parent-child-tracking.md
thoughts/shared/plans/2025-01-08-improve-error-handling.md
```

**Research:**
```
thoughts/shared/research/YYYY-MM-DD-ENG-XXXX-description.md
```

**Handoffs:**
```
thoughts/shared/handoffs/ENG-XXXX/YYYY-MM-DD_HH-MM-SS_ENG-ZZZZ_description.md
```

### YAML Frontmatter

Always include standardized frontmatter:
```yaml
---
date: 2025-01-08T14:30:00-08:00
researcher: [Name]
git_commit: abc123
branch: feature/thing
repository: project-name
topic: "Feature Implementation"
tags: [implementation, feature-name]
status: complete
last_updated: 2025-01-08
last_updated_by: [Name]
type: implementation_strategy
---
```

### Code References

Always use file:line format with context:
```markdown
- `src/api/handler.go:123` - Webhook validation logic
- `lib/utils.ts:45-67` - Helper functions for parsing
```

For GitHub permalinks:
```
https://github.com/{owner}/{repo}/blob/{commit}/{file}#L{line}
```

---

## Session Continuity & Handoffs

### Creating Handoffs

When ending a session, create a handoff document:

```markdown
# Handoff: ENG-XXXX [description]

## Task(s)
[Description with status: completed, in progress, planned]
[Reference to plan/research documents]

## Critical References
[2-3 most important file paths]

## Recent Changes
[Changes made in file:line syntax]

## Learnings
[Patterns, root causes, important discoveries]

## Artifacts
[Exhaustive list of produced/updated files]

## Action Items & Next Steps
[What the next agent should do]

## Other Notes
[Additional context, references, tips]
```

### Resuming from Handoffs

1. Read handoff document completely
2. Read all linked plans/research documents
3. Spawn research tasks to verify current state
4. Present analysis before proceeding:
   - Task statuses
   - Validated learnings
   - Recent changes status
   - Recommended next actions
   - Potential issues

### Validation Scenarios

| Scenario | Approach |
|----------|----------|
| Clean continuation | All changes present, proceed with actions |
| Diverged codebase | Reconcile differences, adapt plan |
| Incomplete work | Complete in-progress tasks first |
| Stale handoff | Re-evaluate strategy entirely |

---

## Debugging Strategies

### Investigation Tools

**Logs:**
```bash
ls -t ~/.humanlayer/logs/daemon-*.log | head -1
ls -t ~/.humanlayer/logs/wui-*.log | head -1
```

**Database:**
```bash
sqlite3 ~/.humanlayer/daemon.db ".tables"
sqlite3 ~/.humanlayer/daemon.db "SELECT * FROM sessions ORDER BY created_at DESC LIMIT 5;"
```

**Service Status:**
```bash
ps aux | grep hld     # Daemon running?
ps aux | grep wui     # WUI running?
```

**Git State:**
```bash
git status
git log --oneline -10
git diff
```

### Debug Report Format

```markdown
## Debug Report

### What's Wrong
[Clear statement based on evidence]

### Evidence Found

**From Logs:**
- [Error with timestamp]

**From Database:**
```sql
[Query and result]
```

**From Git/Files:**
- [Recent related changes]

### Root Cause
[Most likely explanation]

### Next Steps
1. **Try This First**: [command]
2. **If That Doesn't Work**: [alternatives]
```

---

## Sub-Agent Orchestration

### Best Practices for Spawning Tasks

1. **Spawn multiple tasks in parallel** for efficiency
2. **Each task should be focused** on a specific area
3. **Provide detailed instructions**:
   - Exactly what to search for
   - Which directories to focus on
   - What information to extract
   - Expected output format

4. **Be EXTREMELY specific about directories**:
   - Use full paths: `humanlayer-wui/` not "UI"
   - Include path context in prompts

5. **Request specific file:line references**
6. **Wait for ALL tasks to complete** before synthesizing
7. **Verify sub-task results** - spawn follow-ups if needed

### Example Multi-Task Spawn

```python
tasks = [
    Task("Research database schema", db_research_prompt),
    Task("Find API patterns", api_research_prompt),
    Task("Investigate UI components", ui_research_prompt),
    Task("Check test patterns", test_research_prompt)
]
# Spawn all concurrently, wait for all to complete
```

### Agent Type Selection

| Task Type | Agent to Use |
|-----------|--------------|
| Find file locations | codebase-locator |
| Understand code mechanics | codebase-analyzer |
| Find similar patterns | codebase-pattern-finder |
| Find documents | thoughts-locator |
| Extract document insights | thoughts-analyzer |
| Get ticket details | linear-ticket-reader |
| Search tickets | linear-searcher |

---

## Success Criteria Design

### Always Separate Into Two Categories

**Automated Verification** (run by agents):
```markdown
- [ ] Database migration runs: `make migrate`
- [ ] All unit tests pass: `go test ./...`
- [ ] No linting errors: `golangci-lint run`
- [ ] API returns 200: `curl localhost:8080/api/endpoint`
- [ ] Type checking passes: `npm run typecheck`
```

**Manual Verification** (requires human):
```markdown
- [ ] Feature appears correctly in UI
- [ ] Performance acceptable with 1000+ items
- [ ] Error messages are user-friendly
- [ ] Works correctly on mobile devices
- [ ] No regressions in related features
```

### Best Practices

- Use `make` commands when possible: `make -C directory check` not `cd dir && npm run`
- Be specific about what passes/fails
- Include actual commands, not descriptions
- Manual steps should be actionable, not vague

---

## Command Design Patterns

### Standard Command Structure

```markdown
---
description: Brief description of what this command does
model: opus  # optional: specify model if needed
---

# Command Name

[Purpose statement]

## Initial Response
[What to say when invoked, handle different parameter scenarios]

## Process Steps
### Step 1: [Phase Name]
[Detailed instructions]

### Step 2: [Phase Name]
[Detailed instructions]

## Important Guidelines
[Key principles and constraints]

## Example Interaction Flow
[Show typical usage]
```

### Key Design Principles

1. **Handle parameter variations** - What if provided, what if not
2. **Be explicit about ordering** - Which steps depend on others
3. **Include "Important Notes"** section for gotchas
4. **Provide examples** of expected interactions
5. **Define exit conditions** - When to stop and ask
6. **Include error handling** - What to do when things fail

### Workflow Status Progression

For ticket-driven workflows:
```
Triage → Spec Needed → Research Needed → Research in Progress →
Ready for Plan → Plan in Progress → Plan in Review →
Ready for Dev → In Dev → Code Review → Done
```

---

## Quick Reference

### Do's

- Read files fully before acting
- Use parallel sub-agents for research
- Present findings before proceeding
- Track progress with TodoWrite
- Create handoffs for session continuity
- Separate automated and manual verification
- Use specific file:line references
- Wait for all sub-tasks before synthesizing

### Don'ts

- Don't spawn sub-tasks before reading context files
- Don't use `git add -A` or `git add .`
- Don't write plans with open questions
- Don't skip verification steps
- Don't proceed without user confirmation
- Don't assume handoff state matches current state
- Don't make broad changes without understanding impact
- Don't batch todo completions - mark immediately

---

## Attribution

This guide was compiled from the Claude Code commands used by the [HumanLayer](https://github.com/humanlayer/humanlayer) team. These patterns represent real-world best practices developed through extensive use of Claude Code for complex software development workflows.
