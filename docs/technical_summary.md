# Technical Summary

Condensed methodological documentation for the MD thesis pipeline.
Full analysis is documented in the thesis manuscript (in preparation).

---

## 01 — Data Preparation

**Objective:** Harmonise heterogeneous real-world EHR sources into a unified analysis-ready dataset.

**Inputs:** Administrative records, laboratory data, oncological history, mortality registry, hospital clinical information systems (CGP platform, IRCCS San Raffaele Hospital).

**Key steps:**
- Standardised episode-level identifiers across all source datasets
- Integration of patient, admission, mortality, and oncological variables
- Temporal alignment of laboratory measurements within hospitalisation windows
- Unit harmonisation (haemoglobin g/L → g/dL; creatinine μmol/L → mg/dL)
- Generation of laboratory measurement intensity metrics

**Outputs:** `01_episode_master.parquet`, `01_clinical.parquet`, `01_labs_selected.parquet`, `01_lab_measurement_intensity.parquet`

**Key findings:** 1,352 hospitalisation episodes; 1,249 unique patients; 157,219 laboratory observations across 10 biomarkers; median 92 observations per episode.

---

## 02 — Cohort Definition

**Objective:** Define the final analytical cohort and temporal validation structure.

**Cohort derivation:**
- Study window: February 2024 – January 2026
- Internal Medicine wards A and B only
- Last hospitalisation per patient retained (one episode per patient)
- Temporal split: Cohort 1 (Year 1, n=624) → training; Cohort 2 (Year 2, n=625) → independent test

**Outcomes:**
- Primary outcome 1: composite of in-hospital mortality or discharge to palliative care (n=229 events)
- Primary outcome 2: 90-day post-discharge mortality (n=231 events among 1,158 eligible patients)

**Key findings:** Balanced temporal split; meaningful clinical drift between cohorts (Cohort 2 showed higher comorbidity burden but shorter LOS and lower 90-day mortality); metastatic subgroup showed ~43% composite outcome and 90-day mortality rates.

---

## 03 — Feature Engineering

**Objective:** Transform longitudinal laboratory measurements into structured temporal features.

**Feature categories:**
- Biomarker summary variables (mean per window)
- Trajectory delta features (last – first observed value)
- Laboratory monitoring intensity metrics
- Trajectory observability indicators and missingness flags

**Temporal windows:** Days 0–3 (early acute phase), days 3–7 (intermediate phase), days 0–7 (global trajectory)

**Biomarkers:** CRP, WBC, Neutrophils, Lymphocytes, Haemoglobin, Platelets, Creatinine, Urea, Sodium, Potassium

**Output:** 145 engineered features; 1,240/1,249 patients (99.3%) had laboratory data within days 0–7.

---

## 04 — Missingness Analysis

**Objective:** Formally evaluate whether laboratory missingness is clinically informative.

**Key findings:**
- Laboratory availability overall high (98.7% of patients with ≥1 measurement in days 0–7)
- Highest missingness in late dynamic features: Urea_delta_3_7 (~28%), CRP_delta_3_7 (~23%)
- Missingness statistically associated with outcomes: 7/50 features (composite), 5/50 features (90-day mortality)
- Monitoring intensity correlated with hospitalisation complexity: transfusion burden (ρ=0.31), length of stay (ρ=0.25), vasopressor use, oxygen therapy
- Monitoring intensity quartile analyses confirmed progressive increase in adverse outcomes with higher laboratory intensity

**Conclusion:** Laboratory missingness is not random. Monitoring intensity reflects clinical surveillance, patient instability, and escalation of care — not merely data density.

---

## 05 — Biomarker Trajectories

**Objective:** Characterise longitudinal biomarker evolution during hospitalisation by outcome group.

**Methods:** Daily median trajectories; LOWESS smoothing; Mann-Whitney U tests; Cliff's delta effect sizes; Benjamini-Hochberg FDR correction. 90 total comparisons; 49 significant after FDR correction.

**Key findings:**
- Adverse outcome groups showed persistent neutrophilia, lymphopenia, CRP elevation, and renal dysfunction
- Favourable outcome groups showed inflammatory resolution and metabolic stabilisation
- Late-window values (days 3–7) stronger predictors than early-window values (days 0–3)
- Effect sizes predominantly small-to-negligible, suggesting prognostic information arises from integrated longitudinal patterns rather than isolated biomarker levels
- Metastatic patients showed persistently elevated inflammatory markers and lower haemoglobin

**Conclusion:** Trajectory-based characterisation is biologically coherent and statistically robust. This does not automatically translate into trajectory-based predictive gain — a distinction central to the thesis.

---

## 05b — Feature Construction and Selection (Refined)

**Objective:** Refined feature construction for downstream supervised and survival modelling.

**Outputs:** `05b_features_0_3.parquet` (early prediction), `05b_features_0_7.parquet` (discharge-time modelling)

**Methodological choices:** Two temporal windows; mean, delta, and trajectory direction per biomarker; feature selection guided by clinical interpretability and statistical utility; RobustScaler normalisation.

---

## 06b — Supervised Temporal Modelling (Definitive)

**Objective:** Evaluate whether longitudinal laboratory trajectories improve prediction of adverse in-hospital outcomes over clinical baselines.

**Models:** Logistic Regression (L1/L2), Random Forest, Extra Trees, HistGradientBoosting. Feature sets: clinical only (Model A), clinical + laboratory summaries (Model B), clinical + trajectories (Model C).

**Validation:** Strict temporal validation (Cohort 1 → train, Cohort 2 → test); 500 bootstrap iterations.

**Key findings:**
- Best combined model (0–7 window): ROC-AUC 0.783, PR-AUC lift 2.96 (+0.088 PR-AUC over clinical variables alone)
- Early window (0–3 days): near-zero incremental gain (+0.017 PR-AUC)
- Laboratory-only models underperformed clinical baselines in both windows
- Monitoring intensity variables (lab days, density) contributed independent prognostic signal
- Performance temporally stable across cohorts

---

## 07b — Survival Analysis (Definitive)

**Objective:** Predict 90-day post-discharge mortality using penalised Cox proportional hazards models with temporal validation.

**Models:** Cox_A (clinical variables); Cox_B (clinical + laboratory trajectory features). Survival time origin: hospital discharge date.

**Validation:** Cohort 1 → training; Cohort 2 → temporal test; 500 bootstrap iterations; proportional hazards assumption tested explicitly.

**Key findings:**
- Full cohort temporal test C-index: ~0.705 (Cox_A clinical)
- Non-metastatic subgroup: C-index ~0.685; metastatic subgroup: ~0.585
- Adding laboratory trajectories did not substantially improve discrimination over clinical baseline
- Kaplan–Meier risk stratification generalised temporally: low risk ~6%, intermediate ~13%, high risk ~32% 90-day mortality in Cohort 2
- Strongest predictors: metastatic cancer (HR ~1.82), older age (HR ~1.23–1.34), longer LOS (HR ~1.17–1.22)

**Conclusion:** Baseline frailty, comorbidity, and oncological burden remain dominant determinants of post-discharge prognosis. Longitudinal laboratory trajectories characterise patient evolution meaningfully but contribute limited incremental predictive discrimination in this cohort and modelling framework.
