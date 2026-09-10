# TrialMatch AI

### Evidence-Grounded Clinical Trial Eligibility Matching Using MIMIC-IV

TrialMatch AI is a research prototype for evaluating whether de-identified patient records potentially satisfy clinical trial eligibility criteria.

The system is designed to transform natural-language inclusion and exclusion criteria into structured rules, evaluate those rules against patient evidence from MIMIC-IV, and produce transparent criterion-level eligibility decisions.

> **Status:** 🚧 In development — Week 1

---

## Problem

Clinical trial eligibility criteria are typically written as complex natural-language statements involving demographics, diagnoses, laboratory measurements, medications, procedures, and temporal conditions.

Determining whether a patient potentially satisfies these criteria requires connecting trial requirements with information distributed across the patient's clinical record.

TrialMatch AI explores a transparent approach to this problem by combining:

- clinical data engineering
- structured eligibility rules
- LLM-based criterion extraction
- clinical concept mapping
- evidence retrieval
- deterministic eligibility evaluation

Rather than returning only an overall eligibility prediction, the system is designed to explain the decision for **each individual criterion**.

---

## Core Output

Each eligibility criterion is assigned one of three states:

| Status | Meaning |
|---|---|
| `SATISFIED` | Available patient evidence supports the criterion |
| `NOT_SATISFIED` | Available patient evidence contradicts the criterion |
| `UNKNOWN` | Available data is insufficient to determine the criterion |

`UNKNOWN` is treated as a first-class outcome rather than assuming that missing information means a criterion is satisfied or not satisfied.

Each decision will also include the patient evidence used to produce the result.

Example:

```json
{
  "criterion": "Creatinine < 2.0 mg/dL",
  "status": "SATISFIED",
  "observed_value": 1.4,
  "unit": "mg/dL",
  "source_table": "labevents",
  "reason": "Relevant creatinine measurement was below the eligibility threshold."
}
```

---

## Planned Architecture

```text
Clinical Trial
Eligibility Criteria
        |
        v
+-----------------------+
| LLM Criterion         |
| Extraction            |
+-----------------------+
        |
        v
+-----------------------+
| Structured Criteria   |
+-----------------------+
        |
        v
+-----------------------+
| Validation & Clinical |
| Concept Mapping       |
+-----------------------+
        |
        v
+-----------------------+
| MIMIC-IV Patient      |
| Evidence              |
+-----------------------+
        |
        v
+-----------------------+
| Eligibility Rule      |
| Engine                |
+-----------------------+
        |
        v
 SATISFIED
 NOT_SATISFIED
 UNKNOWN
        |
        v
 Supporting Evidence
```

The LLM is intended primarily to structure trial eligibility language. Patient eligibility decisions will be grounded in retrieved clinical evidence rather than relying solely on free-form LLM judgment.

---

## MVP Scope

The initial prototype will focus on:

- one clinical disease area
- approximately five real clinical trials
- de-identified patient records from MIMIC-IV
- structured demographic, diagnosis, laboratory, procedure, and medication data where appropriate
- LLM-assisted eligibility criterion extraction
- deterministic evaluation of measurable criteria
- explicit `UNKNOWN` / abstention handling
- criterion-level supporting evidence
- quantitative evaluation against manually labelled examples

The project intentionally begins with a narrow clinical scope so that the matching logic can be inspected and evaluated carefully.

---

## Example Target Output

```text
TrialMatch AI
--------------------------------------------------

Trial: Example Clinical Trial
Patient: De-identified MIMIC-IV Patient

✓ SATISFIED
Age >= 50 years
Evidence: Patient age = 67 years

✓ SATISFIED
Documented heart failure
Evidence: Relevant diagnosis identified

✗ NOT_SATISFIED
No myocardial infarction within previous 6 months
Evidence: MI identified 74 days before admission

? UNKNOWN
No treatment with Drug X during previous year
Reason: Available record does not establish
12 months of medication history

--------------------------------------------------

Overall result:
Potentially ineligible

Human review required.
```

This is the **target output format** for the completed prototype and is not yet an implemented system output.

---

## Data

### MIMIC-IV

Patient information for this project will come from the **MIMIC-IV Clinical Database**.

Relevant data is expected to include areas such as:

- patient demographics
- hospital admissions
- diagnoses
- laboratory measurements
- procedures
- medication information

The exact tables and variables used will be documented as the project develops.

### Clinical Trials

Eligibility criteria will be obtained from publicly available clinical trial records for the selected disease area.

---

## Data Privacy

**MIMIC-IV patient-level data is not included in this repository.**

The `data/` directory is excluded from Git tracking except for a placeholder `.gitkeep` file.

Users of this repository must obtain their own authorized access to MIMIC-IV and comply with the applicable data-use requirements.

---

## Project Structure

```text
TrialMatch_AI/
│
├── data/
│   └── .gitkeep
│
├── notebooks/
│   └── exploratory analysis and development
│
├── src/
│   └── reusable pipeline and matching code
│
├── evaluation/
│   └── evaluation scripts, labels, and metrics
│
├── docs/
│   └── architecture and project documentation
│
├── .gitignore
└── README.md
```

---

## Development Roadmap

### Week 1 — Problem Definition & Data Exploration

- [x] Define the clinical trial matching problem
- [x] Define `SATISFIED`, `NOT_SATISFIED`, and `UNKNOWN`
- [x] Create repository structure
- [x] Protect local MIMIC-IV data from Git tracking
- [ ] Explore relevant MIMIC-IV tables
- [ ] Select disease area
- [ ] Select approximately five clinical trials
- [ ] Manually analyze eligibility criteria
- [ ] Design structured criterion schema

### Week 2 — Patient Evidence & Matching Engine

- [ ] Build initial patient cohort
- [ ] Create patient feature representation
- [ ] Perform data-quality checks
- [ ] Implement deterministic eligibility rules
- [ ] Implement exclusion logic
- [ ] Add evidence tracking
- [ ] Evaluate one patient against one trial end-to-end

### Week 3 — LLM Eligibility Extraction

- [ ] Develop structured criterion extraction
- [ ] Evaluate extraction quality
- [ ] Validate model-generated criteria
- [ ] Process selected trials
- [ ] Map clinical concepts to MIMIC-IV
- [ ] Handle unsupported and ambiguous criteria
- [ ] Integrate the complete matching pipeline

### Week 4 — Evaluation & Portfolio

- [ ] Create manually labelled ground truth
- [ ] Evaluate eligibility classification
- [ ] Evaluate criterion extraction separately
- [ ] Perform failure analysis
- [ ] Build a lightweight demonstration interface
- [ ] Document results and limitations
- [ ] Create architecture diagram
- [ ] Prepare portfolio and interview material

---

## Evaluation Strategy

The completed prototype will evaluate two components separately.

**Criterion extraction**

The project will assess whether natural-language eligibility criteria are correctly converted into structured representations, including:

- criterion type
- clinical concept
- comparator/operator
- threshold
- unit

**Eligibility evaluation**

System decisions will be compared with manually labelled patient-criterion pairs across:

- `SATISFIED`
- `NOT_SATISFIED`
- `UNKNOWN`

Failure analysis will investigate issues such as missing patient evidence, incorrect clinical mappings, temporal ambiguity, unit interpretation, and criterion extraction errors.

---

## Design Principles

TrialMatch AI follows several principles:

**Evidence over prediction**  
Eligibility decisions should point back to the clinical information that produced them.

**Abstain when evidence is insufficient**  
Missing clinical information should produce `UNKNOWN` rather than an unsupported assumption.

**Separate extraction from decision-making**  
LLMs help transform unstructured trial language into structured criteria, while patient matching remains grounded in explicit clinical evidence.

**Keep the system inspectable**  
Individual criterion decisions should be understandable and manually reviewable.

---

## Current Status

**Week 1 — Problem Definition and Data Exploration**

The repository structure and initial project specification are complete.

Next milestone: **MIMIC-IV data exploration and identification of the tables required for eligibility matching.**

---

## Disclaimer

TrialMatch AI is a retrospective research and portfolio prototype.

It is **not a medical device**, has not been clinically validated, and is not intended to provide medical advice, determine actual clinical trial eligibility, or support clinical decision-making.

Any eligibility results produced by the project require appropriate human review.