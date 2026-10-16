# How I actually work with agents

Working notes and real artifacts from an agent-driven setup that currently runs
five parallel workstreams: two production web products, an engineering R&D program,
a PhD, and a stack of personal tools.
One person, no team, no review queue — agents fill every role a team would.

**Published August 2026 as a sanitized snapshot of my private workspace** —
every date and incident inside comes from the live system, which stays
private because it names clients and stakeholders. Client names, stakeholder
maps, server IPs and task-tracker IDs are replaced with `<placeholders>`;
structure and content are otherwise verbatim and in daily use.

## October 2026: from snapshot to working harness

The August snapshot described the setup; this repo is now becoming its source — skills and
reviewer prompts that I install and run, with a case study per component. Inspired by
[pavel-molyanov/molyanov-ai-dev](https://github.com/pavel-molyanov/molyanov-ai-dev) (process in
skills, fresh-context reviewers, evidence-gated findings), adapted for heavy, domain-bound work —
two products, a PhD and engineering R&D — where the bottleneck is correctness and my attention,
not throughput.

- **[skills/doc-review](skills/doc-review/SKILL.md)** — independent review of documents that make
  factual claims, by fresh reviewers in parallel lanes (numbers / frame / purpose), checked against
  the project's sources of truth. First pilot:
  [case study](docs/case-studies/2026-10-article-review.md).
- **Project contract** in [templates/AGENTS.template.md](templates/AGENTS.template.md): ranked
  sources of truth, reliability constraints, how to verify, accepted decisions — what reviewers
  check against.

What changed in my thinking since August, and is corrected below: reviewers **diagnose and do not
fix their own findings**; there **are** human gates, deliberately few (approving a plan, deciding
on findings that need a decision); context files hold rules and routes, **not current status**;
skill triggers are plain and specific — aggressive "fire on any mention" descriptions over-trigger
with current frontier models.

## The one idea everything hangs on: AI-portability

**Every folder must be a self-contained context.** A fresh agent — Claude Code /
Cowork, Codex, Cursor, Gemini — dropped into any project reads one file
(`AGENTS.md` or `CLAUDE.md`) and can work: no chat history, no verbal
explanations, no tribal knowledge.

This is a written rule of my workspace ("§15 AI-portability" in the workspace
constitution), and it's enforced in both directions: if an external agent can't
operate from folder context alone, that is a defect of the context file — I fix
the file, not the prompt.

What it looks like in practice:

- **`AGENTS.md` at every level.** Root workspace → sphere (products / research /
  R&D / personal) → individual project. Each: a TL;DR for the agent, folder map,
  conventions, the project contract (sources of truth, reliability constraints,
  how to verify, accepted decisions) and explicit "do not touch" lists. The root
  file is a short context router: it says where to look, not what is going on.
  → [examples/AGENTS.workspace.md](examples/AGENTS.workspace.md) (sanitized root file),
  [templates/AGENTS.template.md](templates/AGENTS.template.md) (the template new projects start from).
- **`CLAUDE.md` per codebase** — architecture, deploy topology, and a *known
  gotchas* section where every production incident becomes a permanent
  constraint line. → [examples/CLAUDE.sphere.md](examples/CLAUDE.sphere.md)
  (a 17-app Django platform I built and operate solo). The gotchas section is
  the valuable part: agents stop repeating my incidents.
- **`NOTEBOOK_LLM.md` per Django app** — module-level context files inside each
  application of the larger codebases, so an agent working on one module loads
  only that module's contract.
- **Skills as compressed judgment.** My private skills encode
  stakeholder maps, negotiation context and terminology guardrails (e.g. "term X
  is reserved for the dissertation — in R&D documents always say Y instead").
  In August their triggers were tuned aggressively to fire on casual mentions;
  with current models that over-triggers, so descriptions are now plain and
  specific. Real ones stay private (they contain names and client
  politics); the structure is shown in
  [skills/project-context-example/SKILL.md](skills/project-context-example/SKILL.md).
- **One source of truth for tasks** — Todoist over MCP. Agents read and write
  the same task tree I use on my phone; markdown TODO files are banned. Project
  names and IDs live in Todoist and in project-local context, not in the root
  file.

## What agents actually do here, day to day

See [docs/how-i-work.md](docs/how-i-work.md) for the full loop. Highlights:

- **Multi-round adversarial review.** Before a release, a reviewer agent audits
  the diff and files a round report; another round runs on the fresh state,
  until rounds come back clean. A recent solver release took 6 documented
  rounds (round 1 alone: a class of path-traversal issues closed + regression
  tests for each). Until October 2026 the reviewer also fixed what it found —
  partial self-verification. Now the roles are split: a fresh reviewer
  diagnoses, the working session fixes within the agreed scope, the next fresh
  reviewer verifies, and findings that need a decision come to me.
- **Parallel agents via worktrees** — independent features proceed in isolated
  git worktrees while I do something else entirely.
- **Cross-tool by design** — the same folder context drives Claude Code/Cowork,
  Codex and Cursor interchangeably; whichever agent is open, it lands in the
  same rules.

## Receipts

- Conference-management platform: **17 Django apps**, built solo, in production
  through a live 5-day industry conference (registration, CfP, scheduling,
  dynamic form builder with conditional logic, CRM, GDPR-analog compliance).
- FEA web platform (proprietary): two Django services, ANSYS integration
  (APDL + DPF), Three.js 3D field visualization, Redis/RQ queues, hardware-bound
  licensing.
- Public side project, agent-built:
  [video-feed](https://github.com/alexshep-lab/video-feed) — self-hosted video
  streaming server (FastAPI + React, FFmpeg/NVENC pipeline, HLS). Its
  `CLAUDE.md` is a live, unsanitized example of the per-codebase context this
  repo describes.
- Research side: physics-informed ML (LSTM/Transformer over a physical water
  balance model) + CVaR optimization on forecast ensembles — PhD in progress,
  which is where the ML depth behind the engineering comes from.
