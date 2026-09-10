# TrialMatch AI

**Evidence-Grounded Clinical Trial Eligibility Matching Using MIMIC-IV**

## Problem Statement

Clinical trial recruitment is slow and inefficient, largely because identifying eligible patients requires manually reconciling complex eligibility criteria against heterogeneous electronic health records (EHR) data. This project builds an AI-assisted system that matches patient records to clinical trial eligibility criteria, with every match decision grounded in explicit evidence extracted from the patient's chart.

Given a patient's clinical record and a trial's eligibility criteria, the system determines, for each criterion, whether the patient:

- **SATISFIED** — the chart contains explicit evidence meeting the criterion
- **NOT_SATISFIED** — the chart contains explicit evidence violating the criterion
- **UNKNOWN** — the required information is absent or ambiguous in the chart

The **UNKNOWN** state is a first-class output: the system must never guess when evidence is missing, which keeps every decision auditable and reviewable by a clinician.

## MVP Scope

- Parse a small set of representative trial eligibility criteria (inclusion and exclusion)
- Retrieve relevant patient facts (demographics, diagnoses, labs, medications) from MIMIC-IV
- Classify each criterion as SATISFIED / NOT_SATISFIED / UNKNOWN with a citation to the supporting chart evidence
- Present per-criterion results and an overall eligibility summary in a simple interface

Out of scope for the MVP: real-time EHR integration, multi-trial batch screening at scale, automated enrollment, and any clinical decision-making without human review.

## Planned Pipeline

1. **Data exploration** — profile MIMIC-IV tables relevant to eligibility criteria (patients, admissions, diagnoses, labs, prescriptions)
2. **Criterion representation** — encode trial criteria as structured, machine-checkable conditions
3. **Patient fact extraction** — derive normalized patient features from MIMIC-IV
4. **Matching engine** — evaluate criteria against extracted facts, producing per-criterion states with evidence references
5. **Evaluation** — score agreement against clinician-labeled ground truth on a small benchmark set
6. **Reporting** — summarize results for review, with traceability from every verdict back to source data

## ⚠️ MIMIC-IV Data Source Warning

This project uses **MIMIC-IV**, a restricted-access clinical database containing real patient health records. Access requires completing CITI training and a data use agreement, and the data **must never be committed to this repository, shared, or redistributed**. All raw data stays in `data/`, which is excluded from version control via `.gitignore`. Any derived files that could contain PHI are likewise excluded. Do not place real patient data anywhere else in the project.

## Project Structure

```
TrialMatch_AI/
├── data/           # MIMIC-IV raw/derived data (git-ignored, never committed)
├── docs/           # Design notes, criteria definitions, data dictionaries
├── evaluation/     # Benchmark sets, labeling, and evaluation scripts
├── notebooks/      # Exploratory data analysis and prototyping
├── src/            # Source code (to be added in later phases)
└── README.md
```

## Current Status

**Week 1 — Problem Definition and Data Exploration**

- [x] Repository scaffolded with folder structure
- [x] Problem statement and output-state design documented
- [ ] Obtain/verify MIMIC-IV access
- [ ] Explore and profile relevant MIMIC-IV tables
- [ ] Select representative trials and extract eligibility criteria
- [ ] Define the labeled benchmark for evaluation

## Disclaimer

This is a **research/portfolio project**, not a medical device and not clinical software. It is not validated for, and must not be used for, real patient care, recruitment decisions, or any clinical purpose. All outputs are experimental and require review by qualified clinicians. The MIMIC-IV data used is subject to the terms of the PhysioNet data use agreement.
