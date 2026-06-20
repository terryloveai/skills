---
name: cybernetic-four-concepts-pharma
description: Full reference for the four engineering cybernetics concepts (前馈/积分控制/层级分离/自监控) applied to pharma AI system design. Grounded in Qian Xuesen's Engineering Cybernetics and adapted from the cybernetic-your-agent methodology. Load when elaborating the [四控制论机制分析] section or when the user asks how these concepts apply to their specific pharma scenario.
---

# Engineering Cybernetics — Four Concepts Applied to Pharma AI

Source framework: Qian Xuesen's *Engineering Cybernetics* (《工程控制论》), operationalized via the [cybernetic-your-agent](https://github.com/zeronesun/cybernetic-your-agent) methodology.

The core insight: **a high-performance system must combine feedforward AND feedback — not rely on feedback alone.** Pharma AI systems that only react to failures (pure feedback) are inherently slower, riskier, and more costly to operate in regulated environments than systems that anticipate and pre-empt them (feedforward + feedback).

---

## Concept 1: 前馈 (Feedforward) — Pre-empt errors before they occur

### What it is in control theory

Feedforward and feedback are the two fundamental control modes:
- **Feedback**: measure output → detect deviation → correct input. Always after the fact.
- **Feedforward**: detect the disturbance source before it propagates → adjust control signal proactively. Before the fact.

Classic engineering example: a boiler system detects a drop in inlet water temperature and increases heat output immediately — before steam pressure drops. It measures the *disturbance source*, not the *output deviation*.

Qian Xuesen's principle: **high-performance systems require both feedforward and feedback. Feedback corrects residual error; feedforward eliminates primary disturbances.**

### Why pharma AI needs feedforward

A pure-feedback pharma AI system looks like this:
```
Batch monitoring AI → detects out-of-spec reading → alerts QA → QA investigates → 
deviation report filed → CAPA opened → process adjusted
```
The error has already happened. The batch may be quarantined. The investigation costs time and money.

A feedforward-enabled system looks like this:
```
Batch monitoring AI → at batch start, pre-loads: product CPP ranges, batch history, 
known failure signatures for this process → monitors with context → 
detects early trend before breach → alerts process engineer proactively
```
The disturbance (drift toward spec limit) is caught before it becomes a deviation.

### Pharma AI design questions for feedforward

For each AI use case, ask:

1. **What is the disturbance source?** What inputs are known in advance that, if not pre-loaded, will cause the AI to miss important context?
   - CMC: Product CPP ranges, proven acceptable ranges (PAR), historical batch anomaly signatures
   - Clinical: Active protocol amendments, site-specific deviations, patient population constraints
   - Commercial: Currently approved label indications, active safety communications (DHCP letters, REMS)

2. **What is the startup checklist?** Before executing its primary task, what must the AI retrieve and validate?
   - Example for a clinical AE detection AI: Pre-load the current MedDRA version, the product's known adverse event profile, and the active safety monitoring plan before processing any new ICSR.

3. **What is the failure mode if feedforward is skipped?** If the AI starts without the pre-load, what category of error will it produce?

### Verification criterion

The AI has functional feedforward if: it produces an observable startup output (checklist, constraint summary, or pre-load confirmation) before executing its primary task — and the same class of error does not recur after the first correction.

---

## Concept 2: 积分控制 (Integral Control) — Threshold-based response, not impulsive reaction

### What it is in control theory

PID control has three components:
- **P (Proportional)**: response proportional to current error magnitude
- **I (Integral)**: response proportional to accumulated error over time — only triggers when deviation is persistent
- **D (Derivative)**: response proportional to rate of change — anticipates direction

Pure proportional control is fragile: a single-point sensor spike triggers a full-magnitude correction, causing system oscillation. Integral control smooths this by requiring error to accumulate before triggering action. One data point is noise; a persistent trend is signal.

### Why pharma AI needs integral control

Without an accumulation threshold, pharma AI systems exhibit two failure modes:

**Over-reactive (pure proportional):** One anomalous HPLC reading → AI immediately triggers a batch hold. One patient reporting a mild headache → AI flags as a potential safety signal requiring expedited reporting. This creates alert fatigue, unnecessary CAPA burden, and slows operations.

**Inert (no integration):** AI logs every anomalous reading individually but never connects repeated patterns into a systemic signal. Fifteen consecutive batches showing the same slight pH drift → each logged as an isolated observation, never triggering a process review.

### Pharma AI design pattern for integral control

Define an **accumulation rule** for each output type. Structure:

| Error Pattern | Accumulation Threshold | Trigger Action | Reset Condition |
|---------------|------------------------|----------------|-----------------|
| Sensor reading trending toward spec limit | 3 consecutive readings trending in same direction | Alert process engineer (not QA) | Return within NOR |
| HPLC purity below spec | 1 occurrence | Batch hold + QA notification | QA disposition decision |
| Patient missed visit | 1 occurrence | Record deviation | — |
| Site-level missed visits >15% | Sustained >2 consecutive monitoring periods | RBM escalation | Site corrective action confirmed |
| AE term mismatch in ICSR | 1 occurrence | Route to medical coder | — |
| Same AE mismatch pattern across >5 ICSRs | ≥5 occurrences | Signal to safety management team | — |

**Key design rule:** The accumulation threshold and the trigger action must be **explicitly configured parameters** in the system — not hardcoded logic. They must be reviewable and adjustable by QA/Medical as the product lifecycle evolves. This makes them part of the validated configuration that requires change control.

### Why this matters for GxP

In GMP, the distinction between a "deviation" (one-off, investigation required) and a "trend" (systemic, CAPA required) is fundamental. The AI system's integral control logic is, in effect, encoding the deviation/trend classification decision. If this logic is opaque or hardcoded, it cannot be validated — and it cannot be audited. The accumulation table must be transparent, documented, and traceable.

### Verification criterion

The system has functional integral control if: a single anomalous event does NOT trigger the same response as a sustained pattern of the same anomaly — and this distinction is documented in a configuration file that is version-controlled and change-controlled.

---

## Concept 3: 层级分离 (Hierarchical Decomposition) — Policy and execution must not collapse

### What it is in control theory

Qian Xuesen's hierarchical recursive control: a complex system must be decomposed into levels. Upper levels set strategy, objectives, and constraints; lower levels execute, regulate, and provide feedback. Information flows between levels through clean interfaces; upper levels do not manage execution details, and lower levels do not formulate strategy.

When this separation breaks down — upper level micromanages execution, or lower level autonomously redefines its own objectives — the system becomes fragile: one change cascades unpredictably, and the system oscillates.

### Pharma AI design rules for hierarchical separation

Define two layers explicitly for every AI system:

**Strategic Layer (人工策略层 — human-owned, AI reads only)**

Contains: GMP release criteria, CPP specification ranges, patient eligibility rules, approved label indications, regulatory submission standards, quality agreements, SOPs.

Properties:
- Immutable to the AI. The AI reads these as hard constraints; it cannot modify, override, or extrapolate beyond them.
- Owned by a named human role (QA Director, Regulatory Affairs Lead, Medical Director).
- Any change requires formal change control (and re-validation for computerized systems).

**Execution Layer (AI执行层 — AI-operated, within strategic constraints)**

Contains: data extraction, pattern matching, alert generation, document drafting (within approved templates), report aggregation.

Properties:
- Fully automated within the strategic layer's constraints.
- All outputs are traceable to the strategic layer inputs that governed them.
- The AI acts; it does not decide on strategy.

### Warning signs of layer collapse

Flag these as **⚠️ LAYER VIOLATION** in any design review:

| Proposed Design | Violation |
|-----------------|-----------|
| "AI auto-approves batch disposition if all parameters are in spec" | AI making a GMP release decision — requires QP/authorized person |
| "AI adjusts the CPP alert threshold dynamically based on recent batch performance" | AI modifying a validated parameter — this is unauthorized change control |
| "AI generates new patient eligibility criteria based on enrollment patterns" | AI rewriting protocol — requires IRB/Ethics Committee approval |
| "AI decides whether a safety signal requires expedited reporting" | Medical causality determination requires qualified physician |
| "AI updates the approved label wording based on new literature" | Regulatory authority must approve label changes, not AI |

Each of these situations has a GxP equivalent: the AI would be acting as a "qualified person" or a regulatory authority — roles it cannot legally hold.

### Implementation pattern

In BPMN process diagrams: draw the boundary between the AI execution lane and the human decision lane as a **Pool boundary** (not a Swimlane within the same pool). This makes cross-boundary messaging explicit — requiring a named human task node for every decision that crosses from execution to strategy.

In system architecture: the strategic layer's configuration should be stored in a separate, access-controlled configuration store with version history. The AI's execution engine reads from this store at runtime but has no write access to it.

### Verification criterion

The design has functional hierarchical separation if: you can clearly name every decision point where a human must be present, and the system architecture physically prevents the AI from bypassing those points — not just instructs it not to.

---

## Concept 4: 自监控 (Self-Monitoring) — Close the internal feedback loop before external delivery

### What it is in control theory

Every control system requires a closed feedback loop. In engineering systems, this means the system measures its own output, compares it to the reference value, generates an error signal, and self-corrects. Without this internal loop, all error correction is delegated to the external observer (the user, the QA auditor, the patient).

For a pharma AI system, the "external observer" is a GxP-regulated process. Errors that reach the regulated process are: more expensive to correct, more likely to generate deviation reports, and potentially subject to regulatory scrutiny. Self-monitoring transfers the correction cost from the regulated process back to the AI system itself.

### Pharma AI delivery gate — five gates

Before the AI delivers any output to a downstream regulated process or human decision-maker, it must pass these gates in order. Gate failure triggers the specified fail action — output is never forwarded in a failed state.

| Gate | Priority | Check | Fail Action |
|------|----------|-------|-------------|
| **G1: 合规硬阻断** | P0 — hard block | Does the output contain any reference to an off-label indication, unapproved dosing parameter, or content that would violate the approved label or current regulatory communications? | Suppress output entirely. Route to human reviewer with flag. Do not pass to next gate. |
| **G2: 数据完整性** | P0 — hard block | Is every data point in the output traceable to a source record? Are batch IDs, patient IDs, timestamps present and matched? (ALCOA+ check) | Annotate unverified fields as "待验证 — source not confirmed". Do not forward to a regulated record without human confirmation. |
| **G3: 置信度校验** | P1 — annotate | Is the model's confidence score (or uncertainty estimate) above the validated threshold for this output type? | Attach uncertainty annotation: "Confidence: [X]% — below threshold [Y]%. Human review required before use." Do not present as authoritative. |
| **G4: 结构完整性** | P1 — reject | Are all required output fields present and within valid value ranges? (e.g., batch ID format, alert level classification, parameter name against master list) | Reject the output and re-run. Log rejection reason. |
| **G5: 交付干净度** | P2 — clean | Is the output free of internal reasoning traces, prompt artifacts, intermediate calculation notes, or model debug output that should not reach the end user or regulated system? | Strip before delivery. |

### GxP significance of the delivery gate

The delivery gate is, in practice, the AI system's equivalent of a **pre-release quality check** — the same role a QC analyst plays before a batch is released, or a medical writer plays before a regulatory submission is filed. The key difference: it is automated and runs on every output, not sampled.

For CSV purposes: the delivery gate is a **functional requirement** that must be tested in OQ. Each gate is a testable acceptance criterion. UAT scenarios must include deliberate gate failure inputs to verify correct fail behavior.

### Verification criterion

The delivery gate is functional if: deliberately injecting a gate-failing output (e.g., an output referencing an off-label indication, or missing a required field) results in the correct fail action — and the defective output does not reach the downstream system.

---

## How the Four Concepts Interact as a System

The four concepts are phases of a single control cycle, not independent modules:

```
Task initiation → [前馈] Pre-load constraints and known failure modes
       ↓
Execution → [层级分离] AI operates strictly within execution layer
       ↓
Output → [自监控] Run delivery gate before forwarding
       ↓
User/System feedback → [积分控制] Accumulate feedback; only escalate to 
                        rule/model change when threshold is reached
       ↓
Periodic review → All four concepts reviewed and updated through 
                  formal change control
```

The system is a closed loop. Each concept addresses one phase of the cycle, and all four are necessary — removing any one creates a predictable failure mode:
- Without feedforward: errors repeat because context is never pre-loaded
- Without integral control: system oscillates (over-reacts to noise) or drifts (ignores persistent signal)
- Without hierarchical separation: AI gradually colonizes decision-making that requires human authority
- Without self-monitoring: defective output reaches the regulated process before it can be corrected
