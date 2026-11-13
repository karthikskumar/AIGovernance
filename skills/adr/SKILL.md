---
name: adr
description: Sets up, audits and repairs a project's decision records in .adr/, an index of live decisions loaded every session plus one record file per decision. Use only when the user invokes /adr or explicitly asks to set up, audit or fix decision records.
argument-hint: init | audit | fix
disable-model-invocation: true
---

Run the mode the user gave. With no mode: `init` if `.adr/INDEX.md` does not exist in the project root, otherwise `audit`.

The template is `INDEX.md` in this skill's directory. Its block from `<!-- adr:start` to `<!-- adr:end -->` (the managed block) is the source of truth for every project's copy. It also defines the record format and index line format that `audit` checks against. Agents record decisions by following the managed block, not this skill.

## init

1. If `.adr/INDEX.md` exists, run `fix` instead and stop.
2. Copy the template to `.adr/INDEX.md` byte for byte.
3. Propose the project's domains from its structure and purpose, as `- <domain>: <keyword>` lines with short lowercase keywords. Write them under Domains once the user approves.
4. Make every agent that works on this project load `.adr/INDEX.md` at the start of every session, including cloud sessions that only have the repository. Find the instruction files present (such as `CLAUDE.md`, `.claude/CLAUDE.md`, `AGENTS.md`, `GEMINI.md`, `.cursor/rules/`, `.github/copilot-instructions.md`) and propose the fewest committed changes that achieve it. For Claude Code the line must be the import `@.adr/INDEX.md` in a CLAUDE.md it loads; create `CLAUDE.md` if there is none. An agent without imports gets `Before any other work, read .adr/INDEX.md and follow it.` as the first line of its instruction file. Also propose adding `.adr/INDEX.md merge=union` to `.gitattributes`, so lines added on parallel branches merge without conflict. Show each file and the exact line. Write nothing until the user approves.
5. Report what was written. Do not create records.

## audit

Read-only. Use `git grep` for code searches, since it covers tracked files only and does not skip hidden directories. Check:

- The managed block in `.adr/INDEX.md` is identical to the template's.
- The instruction files still load `.adr/INDEX.md`, and `.gitattributes` has `.adr/INDEX.md merge=union`.
- Each record `.adr/<ID>.md`: the header has every field, `id` matches the file name, the keyword is listed under Domains, `status` is one of `proposed`, `accepted`, `superseded`, `deprecated`, `summary` has the form `<what>, because <why>` in 20 words or fewer, a superseded record has a `superseded_by` that exists, and the body starts with the one-sentence statement and has the four sections.
- Decisions has exactly one line `- [P] <ID>: <summary>` or `- [A] <ID>: <summary>` per proposed or accepted record, with the marker matching `status` and the text equal to `summary`, and no other lines.
- No two records share an ID, ignoring case.
- Every `decision: <ID>` comment in code names an existing record. Flag comments naming superseded records (point to the successor) or deprecated ones.
- Every path in a record's `paths` still exists.
- Size: estimate the tokens in `.adr/INDEX.md` as words × 1.3.

Report one line per finding: `<file>:<line> — <problem> — mechanical` or `— judgment`. End with the size estimate.

## fix

Run `audit`, then fix the mechanical findings:

- Replace the managed block with the template's.
- Rebuild the Decisions lines from the record headers, in ID order.
- Restore a missing load line in the file the project already uses for it. If there is none, ask as in `init` step 4. Restore a missing `.gitattributes` line.
- For a duplicate ID, give the newer record (by `date`) a new ID, rename its file, and update its index line, `supersedes`/`superseded_by` references and code comments.

Do not change judgment findings, such as a summary without a reason, an unknown keyword, a missing path, a dangling code comment or code that contradicts a record; list them for the user. Then run `audit` again and report what remains.
