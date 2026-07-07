# CLAUDE.md — Focus/Podium

## What This Is
Darrell's personal library of Claude Code skills for working McElveen's lead/CRM tools — currently FOCUS (Reynolds and Reynolds dealer CRM) and Podium (AI lead program), which are linked together via McElveen's CRM. Separate from Car-Deal-Ologist (that's a Flask app; this is just Claude Code instruction files).

## File Map
| File | Role |
|---|---|
| `focus-lead-followup/SKILL.md` | Workflow for finding, categorizing, and following up on overdue FOCUS leads. |
| `memory/` | Snapshots of persistent-memory notes learned from live FOCUS/Podium sessions (FOCUS/Podium split, opt-in rules, browser-batching discipline). Source of truth is Claude's local memory store; these are pushed here for backup/reference. |

## Conventions for skill files here
- One skill = one folder = one `SKILL.md` (frontmatter: `name`, `description`). Bundled resources go alongside it in the same folder, not loose in the repo root.
- `description` should be "pushy" about when to trigger — name the specific tool (FOCUS, Podium), the specific trigger phrases, and what the skill covers, so Claude doesn't under-trigger it.
- Document known UI/workflow blockers directly in the skill (e.g. FOCUS's email-not-setup error, text opt-in behavior) rather than relying on Claude to rediscover them each session.

## Working Style
- Keep responses short. No over-explaining decisions — just do the work.
- Don't narrate intermediate steps during multi-step tool sequences ("let me check X," "now I see Y"). Run the steps, then report the result. Speak up mid-task only for a real decision point or blocker.
- When asked to commit, push it too — commit and push together, no separate confirmation needed.
