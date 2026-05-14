# Longitudinal EHR Modelling & Outcome Prediction

**MD Thesis — Marta Bolognini**
Vita-Salute San Raffaele University / IRCCS San Raffaele Hospital, Milan

*Supervisors: Prof. Patrizia Rovere Querini (IRCCS San Raffaele) · Prof. Francesca Ieva (Politecnico di Milano / Human Technopole) · Marco Montagna, MD, PhD Health Data Science *

---

Clinical reasoning in internal medicine is fundamentally temporal. An internist does not 
assess a patient once — she tracks whether creatinine is stabilising or rising, whether 
inflammatory markers are resolving or persisting. Yet most clinical prediction tools reduce 
hospitalisation to a snapshot taken at admission or discharge.

This project addresses that gap directly. Using longitudinal EHR data from **1,249 internal 
medicine hospitalisations** within the MED-Cli prospective study at IRCCS San Raffaele Hospital, 
it evaluates whether trajectory-based laboratory representations improve prediction of composite 
adverse in-hospital outcomes and 90-day post-discharge mortality over baseline clinical variables — 
and investigates what real-world longitudinal clinical data actually encode.

---

## Research Questions

- Can longitudinal laboratory trajectories improve outcome prediction over static clinical baselines?
- Is laboratory missingness clinically informative rather than random?
- What are the representational limitations of hand-engineered temporal features?
- Does trajectory-based characterisation of patient state translate into trajectory-based predictive gain?

---

## Methods

**Cohort:** 1,249 internal medicine hospitalisations (MED-Cli study, IRCCS San Raffaele Hospital, 
Feb 2024 – Jan 2026). Strict temporal validation: Cohort 1 (Year 1) → training; 
Cohort 2 (Year 2) → independent test.

**Feature engineering:** 145 features from 157,219 longitudinal laboratory observations 
across three temporal windows (days 0–3, 3–7, 0–7): biomarker summaries, trajectory deltas, 
monitoring intensity metrics, and trajectory observability indicators.

**Supervised prediction:** Logistic regression, Random Forest, HistGradientBoosting. 
Evaluated on composite adverse in-hospital outcome and 90-day post-discharge mortality.

**Survival analysis:** Penalised Cox proportional hazards models with temporal validation 
and Kaplan–Meier risk stratification (full cohort, non-metastatic, metastatic subgroups).

**Missingness analysis:** Formal evaluation of whether laboratory observability and 
monitoring intensity carry independent clinical and prognostic information.

---

## Key Findings

- **Trajectory features showed modest but context-dependent predictive gains.** Best combined 
  model: ROC-AUC 0.783, PR-AUC lift 2.96 in the 0–7 day window (+0.088 PR-AUC over clinical 
  variables alone). Early 0–3 day window: near-zero increment (+0.017). Laboratory-only models 
  underperformed clinical baselines in both windows.

- **Baseline clinical variables remained the dominant prognostic backbone** for 90-day 
  post-discharge survival. Cox clinical model: C-index ~0.705 on temporal test set. 
  Adding laboratory trajectories did not substantially improve discrimination.

- **Laboratory missingness is formally informative.** Measurement frequency correlated 
  significantly with vasopressor use, oxygen therapy, transfusion burden, and length of stay 
  (Spearman rho up to 0.31). Missingness indicators were retained as model predictors.

- **A conceptual distinction emerged between trajectory-based characterisation and 
  trajectory-based prediction.** Biomarker trajectory patterns (persistent neutrophilia, 
  lymphopenia, CRP elevation, renal dysfunction) were biologically coherent and statistically 
  robust after FDR correction — yet their incremental predictive value was limited.

- **Real-world EHR data encode care processes, not only biology.** The data reflect clinical 
  surveillance decisions, monitoring intensity, and physician concern — with direct implications 
  for training and evaluating ML models on routine clinical data.

- **Clinically meaningful survival risk stratification generalised temporally.** Full cohort 
  KM curves: low risk ~6%, intermediate ~13%, high risk ~32% 90-day mortality in the 
  independent temporal test cohort.

---

## Notebook Structure

| Notebook | Description |
|----------|-------------|
| `01_data_preparation` | Cohort harmonisation from heterogeneous EHR sources; 157,219 lab observations; laboratory intensity metrics |
| `02_cohort_definition` | Final cohort definition, temporal split, outcome definitions, baseline characterisation |
| `03_feature_engineering` | 145 temporal features across 3 windows; trajectory observability encoding; missingness indicators |
| `04_missingness_analysis` | Formal evaluation of informative missingness; monitoring intensity associations |
| `05_biomarker_trajectories` | Longitudinal trajectory characterisation; FDR-corrected group comparisons; effect sizes |
| `06_supervised_temporal_validation` | Supervised prediction with strict temporal validation; ROC/PR-AUC; calibration; feature importance |
| `07_survival_analysis` | Cox PH models; Kaplan–Meier risk stratification; bootstrap CIs; hazard ratio interpretation |
| `08_refined_modelling/05b` | Refined feature construction and selection |
| `08_refined_modelling/06b` | Refined supervised modelling with temporal validation |
| `08_refined_modelling/07b` | Refined survival analysis with temporal validation |

---

## Data Availability

This project uses routinely collected clinical data from the MED-Cli prospective study at 
IRCCS San Raffaele Hospital. Patient-level data cannot be shared publicly due to data 
governance and patient privacy constraints.

All notebooks have been cleared of patient-level outputs. The repository contains analysis 
code and structure; figures are included where they do not contain patient-identifying information.

---

## Technical Stack

Python — pandas · NumPy · scikit-learn · lifelines · matplotlib · seaborn  
Infrastructure — Microsoft Azure Machine Learning · MLflow
