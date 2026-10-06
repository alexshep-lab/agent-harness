---
name: doc-review
description: Independent review of a document that makes factual claims — a paper, thesis chapter, technical report, CV or cover letter — by fresh reviewer agents in parallel lanes (numbers, framing, purpose), each checking claims against the project's sources of truth. Use when the user asks to check, verify or review such a document before it goes out, or after a round of edits. Not for code review.
---

# Document review

Fresh reviewers find what the author no longer sees. This skill orchestrates them; it does not
edit the document. Instructions are in English; reviewers answer in the document's language.

## 1. Gather inputs

From the project's `AGENTS.md` (contract sections, see `templates/AGENTS.template.md`):

- **sources of truth**, ranked — data, scripts, reports, code, contractual documents;
- **reliability constraints** — what may not be claimed, mandatory caveats, reserved terms;
- **decisions log** — decisions the author has already made (definitions, scope, accepted
  limitations). If decisions live inside old review notes, extract the decisions only.

Do **not** pass prior review findings or earlier drafts to reviewers: independence is the point.
Do pass the decisions — otherwise reviewers re-raise settled questions (observed in the pilot).

Choose a scratch directory outside the project for reviewers' temporary scripts.

## 2. Choose lanes proportionally

See `references/lanes.md`. Defaults:

| Document | Lanes |
|---|---|
| Paper, thesis chapter, client report | numbers + frame + purpose |
| Internal note with numbers | numbers + frame |
| CV, cover letter | numbers (claims vs profile / evidence bank) + purpose |
| Argument-heavy text | add logic |

A one-paragraph fix does not need three lanes.

## 3. Run lanes in parallel

One fresh subagent per lane, all on the same revision, all in one message. Each prompt = the
contents of `references/reviewer.md` + its lane section + concrete paths for document, sources,
constraints, decisions, scratch. Reviewers get read access and may run read-only computations;
they never write into the project.

## 4. Verify, consolidate, report

- **Spot-check before reporting**: reopen the cited lines and rerun one key computation for every
  finding that would change a conclusion. A reviewer's confidence is not evidence.
- Deduplicate across lanes; note where lanes agreed independently.
- Group the report by **decision the author must make**, not by finding. Then: data/calculation
  errors (fix, then recompute), local text fixes, what was checked and holds, what was not checked.
- Mark explicitly where a finding conflicts with a logged decision.
- Write the report next to the document (e.g. `Review_<version>.md`) and give the user the
  decisions first.

## 5. After fixes — review again

The most common class in the pilot was *under-corrected*: an issue fixed in one place and left in
another. After edits, run a fresh round (new reviewers, new revision). Default limit: two rounds,
then report what remains.

If fixing a finding means changing code or a data pipeline, the change gets its own fresh code
reviewer on the diff — whoever wrote a fix does not verify it.

## 6. Stop rule

Findings are proposals, not authorisation. When a finding opens a separate, substantial piece of
work (a data-pipeline bug, a recalculation), name it as a separate task and ask. If the user takes
it on in the same session, finish it, then write a handoff file for the original thread instead of
letting the session drift.
