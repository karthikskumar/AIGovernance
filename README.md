# AIGovernance
Collection of foundational and operational rules for LLMs

## Skills
Skills live in [skills/](skills/) in the vendor-neutral [Agent Skills](https://agentskills.io/specification) format. Install them into any supported agent with:

```bash
npx skills add karthikskumar/AIGovernance --skill handoff -g
```

- [handoff](skills/handoff/SKILL.md): writes a complete handoff prompt and opens it in a new conversation. Opening it automatically needs Claude Code in VS Code on macOS, or a host with its own equivalent. Anywhere else, the prompt is printed to paste into a new conversation.

TODO:
1. To solve the problem of LLMs losing decision and motivation context between sessions, maintain a log of decision contexts. This is similar to memory but logs the constant stream of what decisions where made and why so that on a new session start, the model doesn't start with a "fresh perspective".
2. A fully automated hand-off mechanism. This may need to be hook instead.
