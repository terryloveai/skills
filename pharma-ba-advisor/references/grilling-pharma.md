---
  name: grilling-pharma
  description: The design-tree question order and recommended defaults for grilling a BA about a pharma AI design. Load when entering Grill mode in pharma-ba-advisor.
  ---

  # Grilling a Pharma AI Design

  Interview the BA one question at a time, in dependency order. Each later node inherits constraints from the ones above it, so resolving them out of order produces rework. For every question, state your **recommended answer** (grounded in the pillars and the relevant GxP standard) before waiting for the BA's response. If the conversation, attached docs, or codebase already answer a node, state the answer and skip the question.

  The tree below is the order. Walk it top to bottom.

  ## 1. Bounded context (resolve first — everything inherits from it)

  - Which single bounded context does this AI serve? (CMC Process Optimization, Clinical Protocol Design, Pharmacovigilance, Demand Forecasting, Medical Affairs content, etc.)
  - What is explicitly **out of scope**? Name the upstream data sources and downstream systems that are in vs. out.
  - Is there a ubiquitous-language conflict to resolve before anything else? (e.g. whose definition of "batch" governs.)

  *Why first:* the context determines the GxP standard, the entities, and the layer boundary. Get it wrong and every answer below is wrong.

  ## 2. Strategic vs. execution layer boundary (层级分离 — the highest-stakes node)

  - Which decisions must a human make, and which may the AI make autonomously?
  - Recommended default: AI operates the **execution layer only** (extract, match, flag, draft within approved templates). All **strategic-layer** decisions (batch release, eligibility rules, label wording, expedited-reporting causality) stay human-owned. The AI reads strategic constraints; it never writes them.
  - Probe any answer that lets the AI "auto-approve", "auto-adjust a threshold", or "decide" — these are usually ⚠️ LAYER VIOLATIONs.

  *Why second:* it defines what the system is even allowed to do; the remaining cybernetic nodes only make sense once this is fixed.

  ## 3. Compliance standard and validation burden

  - Which GxP standard governs? (GLP / GCP / GMP / GDP / GVP — see `gxp-compliance-quick-ref.md`.)
  - Does output reach a record regulated under 21 CFR Part 11? If so, audit trail, e-signatures, and CSV (IQ/OQ/PQ) are in scope.
  - For China deployment: does PIPL/DSL data-localization apply? Could the system be classed as an AI Medical Device by NMPA?
  - Recommended default: assume Part 11 applies whenever output touches a GxP record, and plan validation artifacts from the start rather than retrofitting.

  ## 4. Feedforward (前馈) — what to pre-load

  - What disturbance sources must the AI pre-load before acting? (CPP ranges, active protocol amendments, current approved label, MedDRA version.)
  - What is the fail-safe if the pre-load source is unavailable?
  - Recommended default: define a mandatory startup checklist; fail safe (refuse to act) rather than fail open (act without context) in any GxP context.

  ## 5. Integral control (积分控制) — thresholds

  - What accumulation rule separates a one-off anomaly from a systematic trend, per output type?
  - Who owns the threshold value, and is it configurable + change-controlled?
  - Recommended default: thresholds are explicit configuration owned by QA/Medical, version-controlled, never hardcoded — because they encode the regulated deviation-vs-trend decision.

  ## 6. Self-monitoring (自监控) — delivery gates

  - Which of the five gates (compliance / data integrity / confidence / completeness / cleanliness) apply, and what is each gate's fail action?
  - Recommended default: G1 (off-label/out-of-spec) and G2 (ALCOA+ traceability) are hard blocks for any pharma system; the rest are scenario-dependent.

  ## 7. Human integration points

  - At which decision gates must a named human role (QP, PI, QA Director, Medical Director) be present?
  - How is that presence enforced architecturally, not just by instruction? (e.g. write-access controls, mandatory human task node.)
  - Recommended default: every cross-boundary decision from execution to strategic layer requires a named, authenticated human approval with an e-signature.

  ## 8. Success and acceptance

  - How will the BA know the system works? What does a passing UAT look like, including deliberate gate-failure cases?
  - Recommended default: UAT must include negative tests (inject a gate-failing output and verify rejection), not only happy-path cases.
  