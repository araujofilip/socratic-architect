---
description: Generate C4 level-4 (code) detail for a specific component from an existing C3 design. Manual-only — never auto-invoked.
allowed-tools: Read, Write, Glob, Grep
argument-hint: <component name> [container name]
---

# /c4-code — Code-level (C4) detail for one component

Generate C4 model level-4 (code) detail for the component named in `$ARGUMENTS`.

This is deliberately SEPARATE from the socratic-architect skill: per the C4 canon,
the code level is rarely needed, goes deeper than most designs require, and is
ideally generated from real code once it exists. In greenfield it serves one narrow
purpose — sketching the riskiest component's internals to de-risk it before
implementation. It is invoked manually, on demand, never as part of the design flow.

## Process

1. **Load the design context first.** Read `architecture/component-design.md` and
   locate the component (and its container). Read the ADRs it links and
   `architecture/house-style.md` / applicable `house-styles/<team>.md` if present.
   If the component doesn't exist in the C3, stop and say so — this command
   details a designed component; it does not invent one. If `$ARGUMENTS` is
   ambiguous (same component name in two containers), ask which.

2. **Honor every decided boundary.** The component's contracts from the C3 —
   inputs, outputs, error model, idempotency, data ownership — are constraints,
   not suggestions. Anything marked "Deliberately not decided" stays undecided:
   represent it as an interface or a note, never as a concrete choice smuggled in.

3. **Sketch, don't specify.** Produce:
   - A Mermaid `classDiagram` of the key classes/interfaces (public surface,
     main collaborators, dependency direction). Follow the design's decided
     technology where it exists; where deferred, stay technology-neutral.
   - A short prose note per element: its job in one line.
   - If runtime behavior is the risky part, a Mermaid `sequenceDiagram` of the
     one flow that matters instead of (or besides) the class diagram.
   Keep it to the ~5–10 elements that carry the risk. This is an illustrative
   starting point for implementation, not a prescriptive spec — say so in the
   output.

4. **Save** as `architecture/diagrams/c4-<component-slug>.mmd` (one file per
   diagram, updated in place when regenerated) and reference it from the
   component's section in `component-design.md`.

5. **Do not** open architectural questions, revisit ADRs, or expand scope to
   other components — if the sketch reveals a genuine design problem, report it
   as a finding and suggest running it through the socratic-architect skill's
   Change Intake instead.
