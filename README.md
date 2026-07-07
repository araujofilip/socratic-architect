# socratic-architect

A Claude skill for **greenfield architecture design through Socratic dialogue**.

It takes vague or partial inputs (spec fragments, domain models, a rough idea) and
drives a long, multi-session question→answer→question process to co-design a new
system: one question at a time, options with honest tradeoffs, every decision recorded
as an ADR, and a mandatory Requirements Restatement & Clarification gate before
anything is frozen.

The process walks the C4 model top-down — brief C1 (context), solid C2 (containers) —
but its center of gravity is a **deep, refined C3 component design**: responsibilities,
boundaries, contracts, data ownership, failure paths. Code-level detail (C4) is
produced only on explicit request.

Includes cloud specialist mode for **AWS or Azure** as component catalogs (purpose and
fit — not pricing; prices are researched live only when asked).

## Installation

Claude Code — clone into your skills directory:

```bash
git clone https://github.com/<you>/socratic-architect ~/.claude/skills/socratic-architect
```

Or per-project: `<repo>/.claude/skills/socratic-architect`.

Claude.ai — upload the packaged `.skill` file and click **Save skill**.

## How it works

| Stage | What happens |
|---|---|
| 0 — Ingest | Reads every spec/model you provide, mines it completely, lists gaps and contradictions. Questions target only the gaps — never what the material already answers. |
| 1 — Context (C1) | Brief Socratic discovery: purpose, actors, external systems, scale ranges, constraints, **ranked** non-functional priorities. Gate: confirm before proceeding. |
| 2 — Containers (C2) | Refinement loop per fork (monolith vs. services, sync vs. async, stores, API style…): hypothesis → tradeoff options with a recommendation tied to *your* constraints → decision → immediate mini-ADR. |
| 3 — Components (C3) | **The core.** Deep component design of the 1–3 containers that matter: responsibility slicing, domain/infrastructure seams, contracts per edge, data ownership, failure paths, cross-cutting concerns, evolution seams. Expect several sessions here. |
| 4 — Cloud mapping | Maps elements to AWS/Azure services by purpose and fit; Well-Architected quick check (top-3 risks + cheapest mitigations). |
| 5 — Restatement | Mandatory final gate: restates everything understood, lists falsifiable assumptions and deferred questions, waits for explicit confirmation. |

Requirement changes mid-process are handled by the **Change Intake** protocol:
diff against recorded state → trace blast radius through the ADRs → reopen only the
impacted forks → supersede (never rewrite) reversed ADRs → scoped mini-restatement.

## Repository layout

```
socratic-architect/
├── SKILL.md                 # the skill: rules, stages, loops, gates
├── references/
│   ├── aws.md               # AWS component catalog (purpose & fit, no pricing)
│   └── azure.md             # Azure component catalog (purpose & fit, no pricing)
├── README.md
├── CHANGELOG.md
└── LICENSE
```

## Artifacts the skill produces in YOUR project

Everything lives under `architecture/` at the root of the project being designed:

```
architecture/
├── working-notes.md          # living memory: inputs, answers, interpretations,
│                             # current stage, change-event log (multi-session state)
├── component-design.md       # the refined C3 — the central deliverable
│                             # (per container: component map, responsibilities,
│                             #  contracts, key flows incl. failure paths, decisions,
│                             #  "deliberately not decided" with revisit-triggers)
├── open-questions.md         # deferred items, each with a "revisit when <trigger>"
├── decision-log.md           # the final Restatement, frozen as the design preamble
├── cloud-mapping.md          # cloud mode only: element → service, fit rationale,
│                             # Well-Architected risks
├── adrs/
│   ├── ADR-001-<slug>.md     # one per decision; sequential 3-digit numbering,
│   ├── ADR-002-<slug>.md     # kebab-case slug; reversed decisions are superseded
│   └── ...                   # (Supersedes / Superseded by), never edited or deleted
├── house-style.md            # optional: the organization's C3 conventions,
│                             # distilled from an example via Socratic intake;
│                             # overrides default notation/templates when present
└── diagrams/                 # ONE file per diagram, Mermaid source
    ├── c1-system-context.mmd
    ├── c2-containers.mmd
    └── c3-<container>.mmd    # one per deep-dived container; updated in place —
                              # the file is current truth, history lives in git
```

ADR lifecycle statuses: `Proposed` → `Accepted` → `Superseded by ADR-NNN` /
`Deprecated` / `Rejected`. A reversed decision gets a new ADR marked
`Supersedes ADR-NNN`; the old record is never rewritten — the trail of *why the
design changed* is part of the design.

## The Iron Rules (summary)

1. One question per message — never batch.
2. Multiple choice with one-line tradeoffs + a recommendation justified by *your*
   stated constraints.
3. Each question is earned by the previous answer — no checklists.
4. Never ask what the provided material already answers.
5. YAGNI guard: complexity must justify itself ("what breaks in v1 without this?").
6. Deciding against the recommendation: one follow-up to capture the driver; a second
   only if the answer reveals a high-risk/irreversible consequence, stated as
   fact + question — never re-litigation.
7. Stop when marginal value drops.
8. Everything is persisted; resumed sessions read the state first and never restart
   discovery.

## License

MIT
