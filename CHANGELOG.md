# Changelog

## 1.4.1 — 2026-07-09

- Frontmatter description rewritten to triggering conditions only (no workflow
  summary). Rationale: skill-authoring testing shows agents may follow a
  workflow-summarizing description as a shortcut and skip the skill body —
  here that risked executing "quick C1 / solid C2 / deep C3" superficially
  while bypassing the Iron Rules, the Boundary Semantics Baseline, and the
  gates. Also brings the frontmatter back under the 1024-character spec limit
  (was ~1.1k, now ~550).
- Deliberate non-change: the per-stage C4 canon stays inline in SKILL.md
  rather than moving to references/. It is discipline-critical at the moment
  each stage runs; a separate file an agent might skip loading is riskier
  than the token cost.
- Pressure-tested (2026-07-10): 15 fresh-context runs on Sonnet, single-turn,
  partial spec + explicit time pressure ("me monta logo a arquitetura completa,
  C1 a C3"). Control (no skill): 5/5 dumped a full C1→C3 with zero questions —
  baseline failure confirmed. With the skill available (old or new description):
  10/10 read the body and complied — one question per message, options +
  recommendation tied to stated constraints, inputs mined first, low variance
  (all converged on summary → gaps → 1 question). Old vs. new description showed
  NO behavioral difference in this single-turn setup where the SKILL.md path was
  pointed at; the description-shortcut risk it guards against is documented for
  long/multi-session flows, which is this skill's central use case. The rewrite
  therefore stands on the spec limit and on that documented evidence, not on
  this test's A/B data.

## 1.4.0 — 2026-07-07

- C4 (code level) fully EXTRACTED from the skill into a standalone manual slash
  command, `commands/c4-code.md` (/c4-code <component>). Rationale: per canon the
  code level is rarely needed and is a different kind of task (on-demand
  de-risking of one component) — not part of the Socratic design flow. The
  command reads the finished C3, honors decided contracts, keeps deferred choices
  deferred, outputs an illustrative Mermaid sketch to
  architecture/diagrams/c4-<component>.mmd, and never reopens design decisions
  (genuine findings are routed to the skill's Change Intake).
- SKILL.md scope statements and deliverables updated accordingly.

## 1.3.0 — 2026-07-07

- Supporting diagrams from c4model.com/diagrams: deployment diagram (canonically
  recommended) closes Stage 4 as diagrams/deployment-<env>.mmd with nested nodes
  and infrastructure elements; C3 key flows that earn a diagram become
  diagrams/dynamic-<flow>.mmd (sparingly, per canon); optional system landscape
  at Stage 1 for many-system organizations. Global rule: only diagrams that add
  value — never every type by default.
- Official diagram review checklist embedded as a QA gate before presenting any
  diagram (title/scope, legend, element names+types+purpose, tech where
  applicable, labelled directional arrows, protocols, decodable notation).
- Queues-and-topics canon: never model a message bus as a container — model each
  queue/topic as a container or use "via queue X" relationship labels; queue and
  message-format OWNERSHIP added to the Boundary Semantics Baseline as a contract
  question.
- House C3 Style now explicitly optional: asked ONCE before the first C3; no
  standard → skill defaults + offer the result as a seed standard; per-team
  standards → scoped house-styles/<team>.md, ask which applies, never mix
  conventions in one design.

## 1.2.0 — 2026-07-07

- Stage 3b redefined: Technology Baseline → **Boundary Semantics Baseline**, built
  on the litmus test "if this choice changed tomorrow, would any other
  component/container change or observe different behavior?" Boundary-crossing
  semantics (sync/async, delivery guarantees, error model, idempotency, data
  ownership, visible consistency, auth) must be decided; internal choices
  (libraries, ORM, code architecture) are deferrable by design. Gray areas split:
  decide the semantics, defer the technology.
- C4 canon embedded per stage from c4model.com/abstractions: C1 boundary =
  ownership boundary (bounded contexts are not systems); container = app or data
  store (managed cloud data services modeled as containers; SPA = two containers);
  component = functionality behind a well-defined interface, in-process, packaging
  orthogonal; microservice = container group under one team, separate software
  system under separate teams (org changes can reshape the C1 → Change Intake).
- C1/C2 technology now explicitly optional during design ("TBD" acceptable);
  reverted the v1.1 hard requirement at the C2 gate. Cross-container edges keep
  mandatory protocol labels — those are contracts.
- NEW: **House C3 Style intake** — feed the skill an example of the organization's
  C3; it mines conventions, questions ambiguities one at a time, critiques
  deviations once (fact + question, house answer wins), distills to house-style.md
  (project + skill folder when writable) and applies it to all future C3s.

## 1.1.0 — 2026-07-07

- NEW Stage 3b — Technology Baseline: mandatory Socratic resolution of the
  container's stack (framework, in-process style, data access, communication
  pattern, shaping libraries) BEFORE any component decomposition. Component shapes
  depend on technology; designing without it produced abstract C3s.
- C4 notation conformance rules (per c4model.com) applied to all diagrams:
  explicit technology on every container and component, specific unidirectional
  relationship labels, technology/protocol on inter-container edges, titles and
  legends.
- component-design.md template: Technology baseline section + Technology column
  in the components table; contracts now include protocol.
- C2 gate now requires explicit (even if provisional) technology per container.

## 1.0.0 — 2026-07-03

Initial release.

- Socratic dialogue rules (one question at a time, tradeoff options with justified
  recommendation, YAGNI guard, two-level follow-up allowance on decisions against
  the recommendation).
- Stages: input ingestion → brief C1 → C2 refinement loop → deep C3 (central
  deliverable) → optional cloud mapping → mandatory Requirements Restatement &
  Clarification gate.
- Multi-session persistence via working documents; resume protocol.
- Change Intake protocol: diff → blast radius via ADRs → reopen only impacted
  forks → ADR supersession → scoped mini-restatement.
- One Mermaid file per diagram under diagrams/ (c1-/c2-/c3- naming).
- AWS and Azure component catalogs (purpose & fit, pricing deliberately excluded —
  researched live only on request).
