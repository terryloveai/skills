---
  name: ba-deliverables-pharma
  description: PRD template and 交付切片 (vertical-slice / tracer-bullet) template for pharma AI projects. Load when entering PRD or Slice mode in pharma-ba-advisor.
  ---

  # BA Deliverable Templates — Pharma AI

  Two deliverables: a PRD (PRD mode) and a 交付切片 breakdown (Slice mode). A 交付切片 (vertical slice / tracer bullet) is filed as one issue/工作项 in the tracker. Both use the project's domain glossary vocabulary throughout and respect any prior architecture decisions in the area being touched. Neither should contain specific file paths or code snippets — they go stale fast. Exception: a snippet that encodes a decision more precisely than prose (a schema, state machine, or type shape from a prototype) may be inlined, trimmed to the decision-rich part.

  ---

  ## PRD Template

  Before writing, identify the **seam**: the single highest integration point where the AI plugs into the regulated workflow. Prefer existing seams; the fewer the better, ideally one. Confirm the seam with the user before writing the body.

  ### Problem Statement
  The problem the user faces, from the user's perspective.

  ### Solution
  The solution, from the user's perspective.

  ### User Stories
  A long, numbered list. Format: *As a [role], I want [capability], so that [benefit].* Cover every aspect of the feature, including the operator roles that supervise the AI (QA reviewer, PI, medical coder), not just the end consumer of its output.

  ### Implementation Decisions
  The modules built/modified and their interfaces, architectural decisions, schema and API contracts, and specific interactions. For a pharma AI system, this section must also record:
  - **Layer boundary (层级分离):** which decisions are execution-layer (AI) vs. strategic-layer (human), and how the boundary is enforced.
  - **Feedforward (前馈):** the startup constraint-preload requirement and its fail-safe.
  - **Integral control (积分控制):** accumulation thresholds as named, configurable, change-controlled parameters.
  - **Self-monitoring (自监控):** the delivery gates and their fail actions.
  No file paths or code (see exception above).

  ### Compliance & Validation Decisions
  The pharma-specific section absent from a generic PRD. Record:
  - The governing GxP standard(s) and whether 21 CFR Part 11 applies.
  - Audit-trail, e-signature, and data-integrity (ALCOA+) requirements.
  - CSV scope: which functional requirements become OQ test cases.
  - Any ⚠️ COMPLIANCE RISK or ⚠️ LAYER VIOLATION carried forward from analysis, with its mitigation.
  - For China: PIPL/DSL data-localization and any NMPA AI Medical Device classification implication.

  ### Testing Decisions
  What makes a good test (test external behavior, not implementation detail); which modules are tested; prior art in the codebase. Include the gate negative-tests: deliberately inject gate-failing output and assert rejection before it reaches the regulated system.

  ### Out of Scope
  What this PRD does not cover.

  ### Further Notes
  Anything else.

  ---

  ## Vertical-Slice Issue Template

  Break the PRD into **tracer-bullet 交付切片**, each filed as one issue/工作项: a thin slice cutting through every layer end-to-end (schema → API → UI → validation/UAT), independently demoable, NOT a horizontal slice of one layer. Prefactor first. Publish in dependency order so blockers are referenceable.

  For each slice:

  ### Title
  Short descriptive name in glossary vocabulary.

  ### What to build
  The end-to-end behavior of this slice, not a layer-by-layer implementation list. No file paths or code (see exception above).

  ### Acceptance criteria
  - [ ] Behavioral criterion 1
  - [ ] Behavioral criterion 2
  - [ ] **GxP-aware criterion** — e.g. "output to the EBR carries a complete audit-trail entry"; "a gate-failing output is rejected before reaching the regulated record". Every slice that touches a regulated record needs at least one.

  ### Blocked by
  Reference to blocking slices, or "None — can start immediately".

  ### User stories covered
  Which PRD user stories this slice addresses.

  ---

  ## Granularity check (Slice mode)

  Present the breakdown as a numbered list showing each slice's title, blocked-by, and user-stories-covered. Ask the user: is the granularity right (too coarse / too fine)? Are the dependencies correct? Should any slice merge or split? Iterate until approved before publishing. Every user story must map to at least one slice.
  