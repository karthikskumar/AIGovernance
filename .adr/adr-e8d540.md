---
id: adr-e8d540
status: accepted
date: 2026-11-13
summary: Built our own adr skill instead of adopting DLD, because DLD surfaces only decisions linked to code
paths: [skills/adr/]
supersedes:
superseded_by:
---
In the context of choosing a mechanism for per-project decision records, facing the cost of building versus adopting one, we decided for our own `adr` skill and neglected DLD, Sundial, Claude memory and a SessionStart hook, to achieve every live decision loading in every session for any agent, including cloud sessions, accepting that we maintain the skill.

## Context
The records must be committed, work with any LLM and in cloud sessions that have only the repository, and have nothing that exists on one host only. The skill installs like `handoff`, with `npx skills add`.

## Decision
`skills/adr` in this repository, with `init`, `audit` and `fix` modes that only the user invokes. The always-loaded index carries all the rules, so agents record decisions without the skill installed.

## Rejected
- DLD (dld-kit): its always-on rule only covers code with `@decision` annotations, and each repository commits 13 skills plus a 428 KB CLI that needs Node 22. Its statuses and drift audit were adopted.
- Sundial: pre-1.0, Claude Code and Codex only, decisions retrieved only on demand, no code links.
- Claude Code memory or a SessionStart hook in `~/.claude/settings.json`: local to one machine and not run in cloud sessions or by other agents.

## Consequences
The managed block in each project's copy of the index is synced from `skills/adr/INDEX.md` by `/adr fix`.
