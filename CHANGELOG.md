# Changelog

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
