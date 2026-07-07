# Changelog

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
