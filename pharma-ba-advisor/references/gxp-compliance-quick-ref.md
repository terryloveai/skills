---
name: gxp-compliance-quick-ref
description: Quick reference for GxP regulatory standards relevant to AI system design in pharma. Covers GLP, GCP, GMP, GDP, pharmacovigilance, 21 CFR Part 11, and CSV/computer system validation. Load when the user asks about regulatory alignment, audit trails, or validation requirements for an AI system.
---

# GxP Compliance Quick Reference for AI Systems

Load this file when:
- The user asks how to make an AI system GxP-compliant
- Designing audit trail, data integrity, or change control requirements
- Writing validation protocols (IQ/OQ/PQ) or CSV documentation requirements
- Assessing regulatory risk for a specific AI use case

---

## GxP Standards at a Glance

| Standard | Full Name | Primary Regulator | Applies To |
|----------|-----------|-------------------|------------|
| GLP | Good Laboratory Practice | FDA 21 CFR Part 58 / OECD GLP | Non-clinical safety studies (tox, carcinogenicity) |
| GCP | Good Clinical Practice | ICH E6 R3, FDA 21 CFR Parts 312/314 | Clinical trials (all phases) |
| GMP | Good Manufacturing Practice | FDA 21 CFR 210/211, EU Annex 11, ICH Q7 | Drug manufacturing and quality control |
| GDP | Good Distribution Practice | EU GDP Guidelines 2013/C 68/01, USP <1079> | Storage and distribution of medicinal products |
| GSP | Good Supply Practice | NMPA (China) | Supply chain in China-specific contexts |
| GVP | Good Pharmacovigilance Practice | EMA GVP Modules, FDA 21 CFR Part 314.81 | Safety monitoring and adverse event reporting |

---

## 21 CFR Part 11 (Electronic Records & Signatures)

**Why it matters for AI systems:** Any AI that creates, modifies, maintains, archives, retrieves, or transmits records that are required by FDA regulations must comply with 21 CFR Part 11.

**Key requirements an AI system must satisfy:**

| Requirement | Design implication |
|-------------|-------------------|
| Audit trail | Every AI-generated output that enters a regulated record must log: who triggered it, when, with what input version, and what model version produced it. Immutable, time-stamped. |
| Access controls | Role-based access. The AI service account must have least-privilege access to regulated data. |
| Electronic signatures | If a human "approves" an AI recommendation, that approval must be a qualified e-signature linked to the individual, not just a button click. |
| System validation | The AI system is a computer system — it requires IQ/OQ/PQ or equivalent validation evidence before use in a regulated process. |
| Record retention | AI output logs must be retained for the same period as the regulated record they contributed to (often 2–15 years depending on record type). |

---

## Computer System Validation (CSV) for AI

Traditional CSV follows GAMP 5 categories. AI/ML systems present new challenges because model behavior is not fully deterministic and models may be retrained.

**GAMP 5 Category for most AI/ML models:** Category 4 (Configured Software) or Category 5 (Custom Software), depending on architecture. A fine-tuned LLM is Category 5.

**Minimum validation artifacts a BA should plan for:**

1. **URS (User Requirements Specification)** — What the AI must and must not do. Include acceptance criteria for each AI output type, including failure modes and guardrail behavior.
2. **Functional Specification** — How the AI system implements the URS. For an LLM: include model version, prompt templates (treated as configurable parameters), and output parsing logic.
3. **IQ (Installation Qualification)** — Confirms the system is installed correctly (correct model version, correct environment, correct integration endpoints).
4. **OQ (Operational Qualification)** — Confirms the system operates per specification under normal and boundary conditions. For an AI: test with curated datasets covering normal, edge, and adversarial inputs.
5. **PQ (Performance Qualification)** — Confirms the system performs correctly in the actual production environment with real data.
6. **Change Control** — Model retraining, prompt changes, or threshold adjustments are **changes to a validated system** and require a change control record before deployment.

**⚠️ Key risk for LLM-based systems:** Prompt engineering changes are not "just configuration" — they alter model behavior and must be treated as changes to a validated parameter. Document prompt versions and require re-OQ evidence when prompts change in regulated contexts.

---

## GMP-Specific AI Considerations (21 CFR 211 / EU GMP Annex 11)

**EU GMP Annex 11** (Computerised Systems) is the most directly applicable standard for AI systems used in manufacturing. Key clauses:

- **Clause 4 (Validation):** Applies to all computerised systems including AI. Validation scope must be proportional to risk.
- **Clause 10 (Audit Trails):** Audit trails must be computer-generated and capture all changes. Cannot be disabled by the AI user.
- **Clause 12 (Security):** Physical and logical controls to prevent unauthorized access.
- **Clause 17 (Archiving):** Data must be retained for the duration defined in the archiving policy and remain retrievable and readable.

**AI in batch disposition — mandatory design rule:**
AI may recommend, flag, or score batch quality. It must NOT autonomously release or reject a batch. The final disposition decision requires a qualified person (QP in EU, equivalent in US) with a documented e-signature. Design the AI as a **decision support tool**, not a decision-making authority.

---

## GCP-Specific AI Considerations (ICH E6 R3)

ICH E6 R3 (2023 revision) explicitly addresses risk-based approaches and data governance.

**Data integrity requirements:** Source data must be ALCOA+ compliant:
- **A**ttributable (who created it)
- **L**egible (human-readable)
- **C**ontemporaneous (recorded at time of observation)
- **O**riginal (first recording, not a transcription)
- **A**ccurate
- **+ Complete, Consistent, Enduring, Available**

**AI use in clinical — design rules:**
- AI-generated data extractions from source records (EHR, wearables) must be traceable back to the original source. The AI must not alter source records.
- Any AI model used for patient eligibility screening must be validated and the sponsor must be able to demonstrate to the FDA that eligibility criteria were applied correctly.
- Adverse event detection via NLP: every AE flagged by AI must still be reviewed by a medically qualified person before being entered into the safety database.

---

## Pharmacovigilance (GVP) AI Considerations

Relevant EMA GVP Modules: Module VI (Individual Case Safety Reports), Module IX (Signal Management), Module XVI (Risk Minimisation Measures).

**AI in signal detection:**
- AI can automate disproportionality analysis (ROR, PRR) on spontaneous reporting databases.
- Signal evaluation (clinical causality assessment) must involve a medically qualified person.
- Any AI-assisted ICSR (Individual Case Safety Report) processing must maintain full traceability: which AI model, which version, what input, what output, who reviewed.

**⚠️ Hard deadline:** Serious unexpected ADRs require expedited reporting (15-day for ICSRs to FDA; 7-day for fatal/life-threatening). AI that assists in report processing must not introduce latency that causes a regulatory deadline miss. SLA requirements must be explicit in the URS.

---

## NMPA (China) Specifics

For AI systems deployed for Chinese operations:

- **Data localization:** Patient data, clinical data, and genomic data are subject to China's Personal Information Protection Law (PIPL) and Data Security Law (DSL). Cross-border data transfer requires explicit regulatory clearance.
- **AI Medical Device regulation:** If the AI system outputs a clinical decision (e.g., disease diagnosis, treatment recommendation), it may be classified as an AI Medical Device under NMPA's 2021 AI Medical Device Guidelines and require NMPA approval before deployment.
- **GMP equivalence:** China GMP (2010 revision + subsequent supplements) is broadly aligned with EU GMP but has specific requirements for traditional Chinese medicine manufacturing. Confirm which standard applies.

---

## Quick Risk Triage for BA

Use this table to quickly assess the compliance burden of a proposed AI use case:

| AI Use Case | GxP Standard | Validation Required? | Human Gate Required? | Key Risk |
|-------------|-------------|----------------------|---------------------|----------|
| Molecule property prediction (internal R&D) | GLP (if used in regulatory submission) | Yes, if results go to regulatory dossier | No (research context) | Data provenance for submission |
| Patient eligibility screening | GCP | Yes | Yes (site PI confirms) | Wrong exclusion = protocol deviation |
| Real-time batch monitoring alert | GMP / Annex 11 | Yes | Yes (QA/production supervisor) | False negative = missed OOS event |
| Batch disposition recommendation | GMP / Annex 11 | Yes | Yes (mandatory — QP or designee) | Autonomous AI release is a GMP violation |
| Demand forecasting | GDP (if affects cold chain supply) | Proportional to risk | Recommended | Stockout of critical medicine |
| HCP promotion content generation | FDA 21 CFR 202 / OPDP | Yes (MLR review process) | Yes (mandatory MLR) | Off-label promotion = regulatory violation |
| ICSR processing assistance | GVP | Yes | Yes (medically qualified reviewer) | Missed AE reporting deadline |
