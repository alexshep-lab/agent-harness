# Case study: independent review of a journal-article draft (October 2026)

> Sanitized. The document is a draft journal article from my PhD (reservoir regulation, built on
> a public archive of regulator directives and daily operational data). Unpublished results are
> not reproduced here — only what the review process found and what it taught.

## Setup

- Draft v0.3, ~75 KB of Markdown, already through **two rounds of author proofreading** and two
  passes of a formal-logic review skill.
- `doc-review` with three lanes in parallel — numbers, frame, purpose — each a fresh subagent with
  the reviewer prompt, the project's sources of truth (data, scripts, reports) and reliability
  constraints. Reviewers were **not** given the proofreading notes.
- A co-author had just said they did not understand *why the paper exists* and "who are we to
  question" the regulator's decisions. The purpose lane was added for that question.

## What the lanes found

| Lane | Findings | Character |
|---|---:|---|
| numbers | 13 (5 major) | parser errors in the data pipeline (a 3-year "period" from a typo in a source text, a false duplicate, conditions misclassified), a share computed over the wrong denominator, a stated exclusion reason contradicted by the data |
| frame | 12 (6 major) | a documented admissible limit ignored, conclusions phrased causally against the paper's own caveats, a "forecast at decision time" window that partly looked ahead |
| purpose | 9 (8 need author decision) | abstract answers "do they comply?" instead of "what is this for and for whom"; novelty wider than what was tested; two of three motivations not backed by results |

The purpose lane answered the co-author's question directly: the text did contain an answer, but
only in the introduction, without an addressee, and the headline result read as a verdict on the
regulator.

## Compared with two rounds of author proofreading

- **New:** all data-pipeline errors, the look-ahead window, unsupported motivations, unsourced
  advice to the regulator, a missing related-work strand.
- **Under-corrected** — the most instructive class: the author had already fixed the "audit tone",
  "causes vs compatibility" and an over-generalisation in earlier rounds, but each fix had reached
  only some of the places. Fresh review after every edit round catches exactly this.
- **Conflict with a decision:** one finding contradicted an explicit earlier decision the
  reviewers did not know about. It was still worth revisiting (a later decision logically
  extended to it), and the author reversed the earlier one — but it showed that reviewers need the
  **decisions log** (without prior findings).
- **An error that originated in the proofreading notes themselves** and propagated into the
  paper and a report.

## Follow-through and the second lesson

Fixing the data pipeline was a separate task. The parser diff then got its own fresh code
reviewer, which found three new errors in the fix and confirmed no regressions in previously
correct rows; one "obvious" change by the fixing agent caused a regression and was reverted.
Whoever writes a fix does not verify it.

After recomputation the sample grew by ~18 %, the headline count of limit exceedances shrank about
threefold (most were within the admissible raise the paper had ignored), the main relationship
held on the full sample, and an out-of-sample "reproduces" claim lost significance and has to go.

## Lessons encoded in `skills/doc-review`

1. Fresh context per lane; lanes in parallel on one revision.
2. Reviewers get sources, constraints and the **decisions log** — not prior findings.
3. The orchestrator spot-checks every conclusion-changing finding before reporting.
4. Report grouped by the decisions the author must make.
5. Fresh round after every edit round (under-corrected class).
6. Code/data fixes get their own fresh reviewer on the diff.
7. Stop rule: a finding that opens a substantial separate task is named and handed off — the pilot
   itself drifted from "set up the method" into "fix the paper's data" before this rule existed.
