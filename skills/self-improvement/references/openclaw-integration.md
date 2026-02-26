# OpenClaw Integration Guide

Use this reference when running `self-improvement` in OpenClaw environments.

## Core Model

OpenClaw favors workspace-level memory and prompt injection over tool-specific hooks.

- Project-scoped learnings stay in `.learnings/`
- Cross-project patterns are promoted to workspace guidance files
- Session-to-session sharing uses `sessions_*` tools

## Recommended Workspace Layout

Use placeholders instead of machine-specific paths:

```text
<workspace>/
├── AGENTS.md
├── SOUL.md
├── TOOLS.md
├── skills/
│   └── <skill-name>/
│       └── SKILL.md
└── sessions/
    └── <session-id>.jsonl
```

## Promotion Targets in OpenClaw

Promote only broadly reusable patterns:

- `AGENTS.md`: workflow and delegation rules
- `SOUL.md`: behavior/style guidelines
- `TOOLS.md`: tool/MCP usage and gotchas

Keep one-off details in project `.learnings/` files.

## Promotion Decision Tree

```text
Project-specific?
├── Yes -> keep in .learnings/ or project docs
└── No  -> Behavioral?
          ├── Yes -> SOUL.md
          └── Tool/integration?
                    ├── Yes -> TOOLS.md
                    └── AGENTS.md
```

## Inter-Session Sharing Pattern

When you discover something important in one session:

1. Identify related active sessions (`sessions_list`)
2. Send concise learning summary (`sessions_send`)
3. Log durable rule to workspace or project file as appropriate

Keep shared messages short and actionable.

### Command Snippets

```bash
# Find active/recent sessions
sessions_list --active
sessions_list --recent 10

# Send a learning to another session
sessions_send --to <session-id> --message "Learning: <short actionable summary>"

# Pull recent context from a session transcript
sessions_history --session <session-id> --last 50
```

## Hybrid Usage (Claude Code + OpenClaw)

Recommended split:

- `CLAUDE.md`: repository conventions
- `.learnings/`: repository-local evidence/history
- `SOUL.md` / `TOOLS.md` / `AGENTS.md`: workspace-global guidance

Flow:
1. Log locally first
2. Promote globally only if reusable across projects
3. Keep wording consistent for searchability

## OpenClaw-Specific Triggers

Capture these as learnings when recurring:

- MCP server errors or undocumented behavior
- Session handoff confusion
- Model behavior surprises that affect output quality
- Skill registry/install gotchas

## Troubleshooting Checklist

If learnings are not sticking:

- Confirm you wrote to a persistent workspace or repo file
- Confirm target file is loaded/injected in your environment
- Confirm session context includes the relevant guidance file
- Prefer explicit writes over assumed automatic persistence

## Notes

Keep this reference focused on integration patterns.
Detailed self-improvement entry formats and lifecycle remain in `SKILL.md`.
