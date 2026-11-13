# AIGovernance
Collection of foundational and operational rules for LLMs

## Skills
Skills live in [skills/](skills/) in the vendor-neutral [Agent Skills](https://agentskills.io/specification) format. Install them into any supported agent with:

```bash
npx skills add karthikskumar/AIGovernance --skill handoff -g
npx skills add karthikskumar/AIGovernance --skill adr -g
```

- [handoff](skills/handoff/SKILL.md): writes a complete handoff prompt and opens it in a new conversation. Opening it automatically needs Claude Code in VS Code on macOS, or a host with its own equivalent. Anywhere else, the prompt is printed to paste into a new conversation.
- [adr](skills/adr/SKILL.md): keeps a project's decisions and their reasons in `.adr/`, so a new session starts with them instead of a fresh perspective. `.adr/INDEX.md` holds the rules and one line per live decision, and is loaded into every session; each decision's full record is `.adr/<ID>.md`, read only when needed. Run `/adr init` once per project and commit the result. After that, any agent that loads the index records decisions on its own, including in cloud sessions, without the skill installed. `/adr audit` reports drift and `/adr fix` repairs it.

TODO:
1. A fully automated hand-off mechanism. This may need to be hook instead.
