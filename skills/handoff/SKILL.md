---
name: handoff
description: Hands the current conversation off to a fresh agent by writing a complete handoff prompt and opening it in a new conversation. Use only when the user explicitly asks to hand off or invokes /handoff, never on your own initiative.
argument-hint: <word or sentence describing what the next conversation is for>
allowed-tools: Bash(python3:*)
disable-model-invocation: true
---

Hand this conversation off to a new one. The focus of the handoff is the text the user gave when invoking this skill.

If the focus is empty, hand off all ongoing tasks and any relevant context that has not been completed.

Never write the prompt to a file.

## Prompt

The reader is a fresh agent with none of this conversation. It will have the same working directory, project instruction files (such as CLAUDE.md or AGENTS.md), and memory, so leave out anything those already supply. Everything else it needs must be in the prompt. Include what serves the focus and drop the rest.

Cover, in prose with sections only where they help:

- The goal, and the motivation behind it, so the agent can make judgment calls the way the user would.
- Current state: what is done, what is verified versus assumed, and what is in progress.
- Decisions already made and the reason for each, including approaches tried and rejected. Leave out any already recorded in the project's `.adr/` decision records.
- The specific files, functions, commands, and errors involved, with exact paths and verbatim text where precision matters.
- What to do next, and what done looks like.
- Constraints and preferences the user stated in this conversation that are not in those instruction files.
- Open questions that are the user's to answer.

Address the agent directly. Write full sentences and define any label coined in this conversation. Describe the situation as it stands now, not the conversation chronologically.

## Open the new conversation

If you are Claude Code running in VS Code on macOS, run this, replacing `<PROMPT>` with the prompt verbatim:

```bash
python3 -c 'import sys,urllib.parse,subprocess;subprocess.run(["open","vscode://anthropic.claude-code/open?prompt="+urllib.parse.quote(sys.stdin.read())],check=True)' <<'HANDOFF_EOF'
<PROMPT>
HANDOFF_EOF
```

In any other environment, use your host's own way of opening a new conversation pre-filled with a prompt, if it has one.

- On success, reply with one line: "The conversation has been handed-off." Do not print the prompt.
- If there is no way to open a conversation, reply with one line: "No way to open a new conversation here." then print the prompt in full so the user can paste it into a new conversation (respect code block or bash commands formatting).
- On failure, reply with one line: "Prompt could not be handed off due to an unknown error." then print the prompt in full so the user can paste it into a new conversation (respect code block or bash commands formatting).
