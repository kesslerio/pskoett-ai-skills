---
name: self-improvement
description: "Captures learnings, errors, and corrections to enable continuous improvement. Use when: (1) A command or operation fails unexpectedly, (2) User corrects Claude ('No, that's wrong...', 'Actually...'), (3) User requests a capability that doesn't exist, (4) An external API or tool fails, (5) Claude realizes its knowledge is outdated or incorrect, (6) A better approach is discovered for a recurring task. Also review learnings before major tasks. For CI-only/headless learning capture, use self-improvement-ci."
---

# Self-Improvement Skill

Capture mistakes and hard-won lessons in a durable format. Use this to prevent repeat failures and promote recurring patterns into project memory.

## Install

```bash
npx skills add pskoett/pskoett-ai-skills/self-improvement
```

CI/headless variant:

```bash
npx skills add pskoett/pskoett-ai-skills/self-improvement-ci
```

## When to Log

Log immediately when any of these occur:

- Command or operation fails unexpectedly
- User corrects your answer
- User asks for a missing capability
- External API/tool fails
- Knowledge was outdated or wrong
- A better recurring approach is discovered

## Setup

Create a local learnings folder in the repository root:

```bash
mkdir -p .learnings
```

Use templates from `assets/LEARNINGS.md` and `assets/SKILL-TEMPLATE.md` if desired.

## Files

- `.learnings/LEARNINGS.md`: corrections, knowledge gaps, best practices
- `.learnings/ERRORS.md`: command/tool/runtime failures
- `.learnings/FEATURE_REQUESTS.md`: missing capabilities requested by users

## Entry IDs

Format: `TYPE-YYYYMMDD-XXX`

- `TYPE`: `LRN`, `ERR`, or `FEAT`
- `YYYYMMDD`: current date
- `XXX`: sequence or short unique token

Examples:
- `LRN-20250115-001`
- `ERR-20250115-A3F`
- `FEAT-20250115-002`

## Learning Entry Template

Append to `.learnings/LEARNINGS.md`:

```markdown
## [LRN-YYYYMMDD-XXX] category

**Logged**: ISO-8601 timestamp
**Priority**: low | medium | high | critical
**Status**: pending
**Area**: frontend | backend | infra | tests | docs | config

### Summary
One-line learning

### Details
What happened, what was wrong, what is now correct

### Suggested Action
Concrete follow-up

### Metadata
- Source: conversation | error | user_feedback
- Related Files: path/to/file.ext
- Tags: tag1, tag2
- See Also: LRN-20250110-001 (optional)
- Pattern-Key: simplify.dead_code (optional)
- Recurrence-Count: 1 (optional)
- First-Seen: 2025-01-15 (optional)
- Last-Seen: 2025-01-15 (optional)

---
```

## Error Entry Template

Append to `.learnings/ERRORS.md`:

```markdown
## [ERR-YYYYMMDD-XXX] skill_or_command_name

**Logged**: ISO-8601 timestamp
**Priority**: high
**Status**: pending
**Area**: frontend | backend | infra | tests | docs | config

### Summary
Brief description of failure

### Error
```
Actual error text
```

### Context
- Command/operation attempted
- Inputs/parameters
- Environment notes (if relevant)

### Suggested Fix
Potential resolution

### Metadata
- Reproducible: yes | no | unknown
- Related Files: path/to/file.ext
- See Also: ERR-20250110-001 (optional)

---
```

## Feature Request Entry Template

Append to `.learnings/FEATURE_REQUESTS.md`:

```markdown
## [FEAT-YYYYMMDD-XXX] capability_name

**Logged**: ISO-8601 timestamp
**Priority**: medium
**Status**: pending
**Area**: frontend | backend | infra | tests | docs | config

### Requested Capability
What user asked for

### User Context
Why they need it

### Complexity Estimate
simple | medium | complex

### Suggested Implementation
How to build it

### Metadata
- Frequency: first_time | recurring
- Related Features: existing_feature_name

---
```

## Status Lifecycle

Use these statuses:

- `pending`
- `in_progress`
- `resolved`
- `wont_fix`
- `promoted`
- `promoted_to_skill`

When resolved, append:

```markdown
### Resolution
- **Resolved**: 2025-01-16T09:00:00Z
- **Commit/PR**: abc123 or #42
- **Notes**: what changed
```

## Promotion Rules

Promote broad, recurring learnings to durable project docs.

Suggested destinations:

- `CLAUDE.md`: project conventions and hard rules
- `AGENTS.md`: workflow/automation guardrails
- `.github/copilot-instructions.md`: Copilot-facing standards
- `SOUL.md` / `TOOLS.md`: workspace-level behavior/tool guidance

Promote when all are true:

- `Recurrence-Count >= 3`
- Seen in at least 2 different tasks
- Seen within 30 days

Write promoted rules as short prevention guidance, not incident narratives.

## Recurrence Handling

When a new item looks similar to an existing one:

1. Search `.learnings/` first
2. Link with `See Also`
3. Increment recurrence metadata
4. Raise priority if repeatedly recurring
5. Consider systemic fix (docs, automation, or architecture)

## Simplify & Harden Feed

When `simplify-and-harden` returns `simplify_and_harden.learning_loop.candidates`:

1. Use `Pattern-Key` as the dedupe key
2. If key exists, increment `Recurrence-Count` and update `Last-Seen`
3. If key does not exist, create a new `LRN-*` entry with:
   - `Source: simplify-and-harden`
   - `Pattern-Key`
   - `Recurrence-Count: 1`
   - `First-Seen` and `Last-Seen`
4. Add `See Also` links to related entries/tasks

## Detection Triggers

Corrections:
- “No, that’s wrong”
- “Actually...”
- “You’re wrong about...”
- “That’s outdated...”

Feature requests:
- “Can you also...”
- “I wish you could...”
- “Is there a way to...”
- “Why can’t you...”

Errors:
- Non-zero command exit
- Exception/traceback
- Timeout/connection failure
- Unexpected output

## Priority Guide

- `critical`: blocks core function, data-loss risk, or security issue
- `high`: common workflow impact or recurring failure
- `medium`: moderate impact, workaround exists
- `low`: minor inconvenience or edge case

## Hook Integration (Optional)

Enable automatic reminders and error capture with hooks.

Minimal setup:

```json
{
  "hooks": {
    "UserPromptSubmit": [{
      "matcher": "",
      "hooks": [{
        "type": "command",
        "command": "./skills/self-improvement/scripts/activator.sh"
      }]
    }]
  }
}
```

With error detection:

```json
{
  "hooks": {
    "UserPromptSubmit": [{
      "matcher": "",
      "hooks": [{
        "type": "command",
        "command": "./skills/self-improvement/scripts/activator.sh"
      }]
    }],
    "PostToolUse": [{
      "matcher": "Bash",
      "hooks": [{
        "type": "command",
        "command": "./skills/self-improvement/scripts/error-detector.sh"
      }]
    }]
  }
}
```

See `references/hooks-setup.md` for details.

## Skill Extraction

Extract a recurring high-value learning into a dedicated skill when it is:

- recurring,
- resolved/verified,
- non-obvious,
- broadly reusable, or
- explicitly requested by the user.

Helper script:

```bash
./skills/self-improvement/scripts/extract-skill.sh skill-name --dry-run
./skills/self-improvement/scripts/extract-skill.sh skill-name
```

Manual option: create `<skill-dir>/SKILL.md` using `assets/SKILL-TEMPLATE.md`.

### Extraction Traceability (Required)

After extracting a learning into a skill:

1. Update source learning entry status to `promoted_to_skill`
2. Add `Skill-Path: skills/<skill-name>` in metadata
3. Verify the extracted skill in a fresh session (self-contained, no hidden context)

## Agent Support

- Claude Code / Codex CLI: hooks-based automation
- GitHub Copilot: manual workflow prompts
- OpenClaw: see `references/openclaw-integration.md`

## Periodic Review (Mini)

Run this quick review regularly (for example weekly):

```bash
# Pending items
rg -n "\*\*Status\*\*: pending" .learnings

# High-priority entries
rg -n "\*\*Priority\*\*: high|\*\*Priority\*\*: critical" .learnings

# Recurrence candidates
rg -n "Pattern-Key|Recurrence-Count|See Also" .learnings/LEARNINGS.md
```

## Validation Checklist

Before closing a task:

- [ ] New learning/error/request logged when needed
- [ ] Related entries linked with `See Also`
- [ ] Recurrence metadata updated
- [ ] Broad patterns promoted to durable docs
- [ ] Resolved items include resolution notes
- [ ] Extracted skills include traceability (`promoted_to_skill` + `Skill-Path`)
