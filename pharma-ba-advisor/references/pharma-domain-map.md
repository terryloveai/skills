---
name: pharma-domain-map
description: Bounded context map for pharma AI systems. Covers R&D, Clinical, CMC, Supply Chain, and Commercial domains — including key entities, domain events, typical AI use cases, and cross-context integration points.
---

# Pharma AI Bounded Context Map

Load this file when:
- The user's AI scenario spans multiple departments or systems
- You need to identify which bounded context owns a given entity or process
- Mapping data flows across organizational boundaries

---

## BC-1: Drug Discovery & Research (R&D)

**Core entities:** Target, Molecule, ADMET Profile, Assay Result, Research Hypothesis
**Value objects:** IC50 Value, LogP, Molecular Weight, Selectivity Index
**Domain events:** `TargetValidated`, `MoleculeGenerated`, `AssayCompleted`, `HitIdentified`, `LeadSelected`

**Typical AI use cases:**
- De novo molecule generation (generative models, diffusion-based)
- ADMET property prediction (toxicity, solubility, permeability, metabolic stability)
- Target identification and validation (knowledge graph reasoning)
- Literature mining and hypothesis generation (RAG over scientific corpus)

**Key data sources:** ChEMBL, PubChem, internal ELN (Benchling, IDBS E-WorkBook), HTS screening data
**Upstream → Downstream:** Validated leads flow into CMC (as Drug Substance candidates) and Clinical (as IND candidates)

**Common language conflicts:**
- "Compound" in R&D (a specific chemical entity) ≠ "compound" in CMC (may refer to a formulation)
- "Batch" in R&D (small-scale synthesis lot) ≠ "Batch" in CMC (GMP manufacturing batch with formal batch record)

---

## BC-2: Clinical Development

**Core entities:** Protocol, Study, Site, Patient/Subject, Adverse Event, Endpoint, eCRF, CIOMS Form
**Value objects:** Dose Level, Visit Window, Eligibility Criterion, Severity Grade (CTCAE)
**Domain events:** `ProtocolAmended`, `PatientEnrolled`, `AEReported`, `DataLocked`, `DMCReviewTriggered`

**Typical AI use cases:**
- Protocol design optimization (feasibility scoring, historical benchmark comparison)
- Patient recruitment prediction (site performance modeling, enrollment rate forecasting)
- Safety signal detection (pharmacovigilance NLP, disproportionality analysis)
- eCRF auto-coding (MedDRA, WHO Drug Dictionary)
- Clinical trial operations (deviation prediction, site risk scoring)

**Key data sources:** EDC systems (Medidata Rave, Veeva Vault), safety databases (Argus, ARISg), EHR (with consent)
**Regulatory boundary:** GCP (ICH E6 R3), 21 CFR Part 11, GDPR/HIPAA apply to all patient data

**Common language conflicts:**
- "Safety" in clinical (patient adverse events) ≠ "safety" in CMC (process hazard/chemical safety)
- "Protocol" in clinical (study design document) ≠ "protocol" in IT (communication protocol)
- "Sponsor" in clinical (the pharma company) ≠ "sponsor" in business (budget owner)

---

## BC-3: CMC (Chemistry, Manufacturing & Controls)

**Core entities:** Drug Substance, Drug Product, Batch Record, Process Parameter, CQA (Critical Quality Attribute), CPP (Critical Process Parameter), Specification, Deviation, CAPA
**Value objects:** Process Parameter Range (NOR/PAR/proven acceptable range), Yield %, Purity %, Particle Size Distribution
**Domain events:** `BatchStarted`, `BatchReleased`, `BatchFailed`, `DeviationRaised`, `CAPAClosed`, `SpecificationChanged`

**Typical AI use cases:**
- Process analytical technology (PAT): real-time batch monitoring with anomaly detection
- Design of Experiments (DoE) augmentation: ML surrogate models for process optimization
- Predictive maintenance for manufacturing equipment
- Batch disposition recommendation (release/reject/quarantine)
- Regulatory document generation (CTD Module 3 sections, method validation reports)

**Key data sources:** LIMS (LabVantage, STARLIMS), MES (Emerson DeltaV, Rockwell FactoryTalk), EBR systems, SCADA/DCS historian data (OSIsoft PI)
**Regulatory boundary:** GMP (21 CFR Parts 210/211, EU GMP Annex 11, ICH Q8/Q9/Q10/Q11)

**AI Harness priority:** Highest. Any AI output that influences batch release decisions must have a mandatory human QA review gate. AI must not autonomously trigger batch rejection or release — it can recommend, flag, and summarize only.

---

## BC-4: Supply Chain & Distribution

**Core entities:** SKU, Inventory Lot, Purchase Order, Distribution Channel, Cold Chain Shipment, Demand Forecast, Safety Stock
**Value objects:** Shelf Life Remaining, Temperature Excursion Log, Lead Time, MOQ
**Domain events:** `ShipmentDispatched`, `ExcursionDetected`, `StockOutAlerted`, `ForecastRevised`, `RecallInitiated`

**Typical AI use cases:**
- Demand forecasting (especially for biologics with long lead times)
- Cold chain monitoring anomaly detection
- Supplier risk scoring
- Recall scope prediction and traceability analysis
- Serialization and track-and-trace (DSCSA, EU FMD compliance)

**Key data sources:** ERP (SAP S/4HANA, Oracle), WMS, IoT cold chain sensors, syndicated sales data (IQVIA, Symphony)
**Regulatory boundary:** GDP (EU GDP Guidelines), DSCSA (US), GMP storage requirements, serialization mandates

---

## BC-5: Commercial & Medical Affairs

**Core entities:** HCP (Healthcare Provider), Account, Territory, Approved Promotional Material, Medical Information Request, Publication
**Value objects:** Prescribing Behavior Segment, Channel Preference, Formulary Status, Market Access Tier
**Domain events:** `DetailingCompleted`, `MedInfoRequestReceived`, `MaterialApproved`, `ComplianceViolationFlagged`

**Typical AI use cases:**
- Next-best-action recommendation for field sales/MSLs
- Approved content generation (MLR-reviewed, on-label only)
- HCP segmentation and targeting
- Adverse event detection from field reports and social media
- Market access and pricing scenario modeling

**Regulatory boundary:** FDA 21 CFR Part 202 (prescription drug advertising), OPDP guidance (promotional labeling), ABPI/IFPMA codes, FCPA/anti-bribery
**⚠️ HARD COMPLIANCE LINE:** AI must never generate or suggest off-label promotional content. Any text generation in this context requires a mandatory MLR (Medical/Legal/Regulatory) review gate. Off-label suggestions are a regulatory red line regardless of accuracy.

---

## Cross-Context Integration Points

| Data Flow | Source BC | Target BC | Key Risk |
|-----------|-----------|-----------|----------|
| Lead molecule → IND submission | R&D | Clinical | ADMET data completeness; translation of research purity specs to GMP specs |
| Clinical safety signal → label update | Clinical | Commercial | Speed of safety communication; off-label implication management |
| Process change → regulatory variation | CMC | Clinical / Regulatory | Change control triggers; comparability studies required |
| Demand signal → production planning | Commercial | Supply Chain | Forecast accuracy; launch supply for new indications |
| Batch genealogy → pharmacovigilance | CMC | Clinical | Lot traceability in AE reports; recall scope determination |

---

## Entity Identity Across Contexts

The same "thing" often has different identity and attributes depending on which bounded context owns it. Always clarify which context's definition is canonical for the AI system being designed.

| Concept | R&D Identity | CMC Identity | Clinical Identity |
|---------|-------------|--------------|-------------------|
| **The drug** | Molecule / lead compound (structural) | Drug substance / drug product (physical batch) | Investigational Product (with kit number, dispensing record) |
| **A "batch"** | Synthesis lot (mg scale, no formal record) | GMP Batch (kg scale, full batch record, QA release) | IMP batch (labeled, randomized, blinded) |
| **"Approval"** | Internal go/no-go decision | QA batch release decision | Regulatory authority marketing approval |
