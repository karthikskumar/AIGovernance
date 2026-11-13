---
id: adr-8bdeb6
status: accepted
date: 2026-11-13
summary: Every session loads INDEX.md, one what-because-why line per live decision, because decisions not tied to code otherwise never surface
paths: [skills/adr/INDEX.md]
supersedes:
superseded_by:
---
In the context of new sessions starting without the project's decisions, facing the token cost of loading them every session, we decided for an index that loads every session with one `- [P|A] <ID>: <what>, because <why>` line per live decision and neglected an instructions-only index and a separate how-to file, to achieve every live decision being known from the start, accepting about 20 to 25 tokens per decision per session.

## Context
Decisions linked to code reach an agent through code comments. Decisions about motivation and direction have no such link and reach a session only if something loads them.

## Decision
`.adr/INDEX.md` holds the rules, the domain mapping and one line per proposed or accepted decision. Each line states what was decided and why, joined by "because". The line equals the record's `summary`, so `/adr fix` can rebuild the lines from the headers. Each agent loads it from whichever file it reads at session start: by import where the agent supports imports, otherwise through a first-line pointer. In this repository, `CLAUDE.md` imports `AGENTS.md`, which has the pointer and imports the index.

## Rejected
- An index with instructions only: decisions not tied to code would never reach a session.
- Moving the rules to a separate `HOWTO.md`: saves about 300 tokens per session, not worth a second file to keep in sync.
- `<P>` and `<A>` markers: GitHub renders them as HTML tags. `[P]` and `[A]` instead.
- The full Y-statement as the index line: 40 or more words. It opens each record instead.
- A hard line cap: `/adr audit` reports the size instead.

## Consequences
The index grows with the number of live decisions. Superseded and deprecated decisions leave it.
