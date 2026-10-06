# The daily loop

## Session start

Every workstream is a folder; every folder opens with its `AGENTS.md` /
`CLAUDE.md`. A session starts with the agent already knowing: what this project
is, what state it's in, what the conventions are, and what it must not touch.
I never re-explain a project to an agent — if I catch myself doing it, the
context file gets patched instead.

## Capture → processing → execution

- **Capture** is frictionless and messy (message-to-self, voice note).
- **Processing** is agent work: raw phrases become structured tasks with
  project, date and priority — filed into Todoist over MCP, which both I and
  every agent treat as the single source of truth. No markdown task lists,
  ever: two task systems means both are wrong.
- **Execution** happens in Cowork/Claude Code sessions per workstream.
  Independent features run in parallel through git worktrees.

## Review: adversarial, multi-round, until dry

The pattern that replaced "nobody reviews my code" as a solo developer:

1. A fresh reviewer agent audits the release scope with a written focus
   (security, error handling, cross-platform readiness, …) and files a round
   report (`REVIEW_<scope>_ROUND<n>.md`) that explicitly lists *the
   limitations of its own review* — what it could not verify and why. It
   diagnoses only.
2. The working session fixes what lies inside the agreed scope and writes a
   regression test per fix. Findings that change behaviour, scope or approach
   come to me as decisions.
3. The next round — a new fresh reviewer — runs against the new state. Repeat
   until a round comes back clean.

*Revised October 2026.* Until then the reviewer also fixed its own findings and
wrote the tests for its own fixes — partial self-verification — and I claimed
"no human review gate anywhere". The gates are now deliberate and few. The same
pattern for documents is [skills/doc-review](../skills/doc-review/SKILL.md);
its first pilot showed why the split matters
([case study](case-studies/2026-10-article-review.md)).

Real numbers from one proprietary release: a solver Linux-port review ran
**6 documented rounds**; round 1 closed a family of path-traversal issues
(model-name validation, root-directory deletion guards) and added a regression
test per fix. A parallel release branch took 3 rounds. The round reports live
in the repo permanently — they are context for the next agent.

## Incidents become constraints

Every production incident ends as a line in the codebase's `CLAUDE.md` gotchas
section, stated as a constraint, with the date. Examples from a live platform:
an OS unattended-upgrade once took the database down mid-conference — the
package is now blacklisted from auto-upgrade and the rule (with the incident
date) is in the file; a proxy-header misconfiguration that caused redirect
loops is documented with the exact settings line that parses it. Agents read
these before touching deploy config, so no agent — and no future me —
re-discovers an incident the hard way.

## Skills as compressed judgment

Private skills carry what code context can't: who the stakeholders are and how
they behave in meetings, which terminology is banned in which documents and
why, how to prepare me for a negotiation. In August their trigger descriptions
were written broadly on purpose ("even casual mentions of 'the meeting' should
trigger this"). With current frontier models that over-triggers: descriptions
are now plain and specific, and the skill body does the work.

## Cross-tool portability as a test

The same folders are periodically driven by Codex and Cursor, and fed to
Gemini/NotebookLM for second opinions. If a non-Claude agent stumbles, the
context file was Claude-shaped — that's a bug, and fixing it keeps the whole
setup vendor-portable.
