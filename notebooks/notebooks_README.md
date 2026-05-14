# Notebooks

All notebooks have been cleared of patient-level outputs prior to publication.
Patient data cannot be shared due to data governance constraints (MED-Cli study, IRCCS San Raffaele Hospital).

---

## Pipeline Overview

| Notebook | Stage | Description |
|----------|-------|-------------|
| `01_data_preparation.ipynb` | Data | Harmonisation of heterogeneous EHR sources; 157,219 lab observations; laboratory intensity metrics; unit normalisation |
| `02_cohort_definition.ipynb` | Cohort | Final cohort definition (n=1,249); temporal validation split; outcome definitions; baseline characterisation |
| `03_feature_engineering.ipynb` | Features | 145 temporal features across 3 windows (days 0–3, 3–7, 0–7); trajectory deltas; observability indicators; missingness encoding |
| `04_missingness_analysis.ipynb` | Missingness | Formal evaluation of informative missingness; monitoring intensity associations; clinical complexity correlations |
| `05_biomarker_trajectories.ipynb` | Trajectories | Longitudinal trajectory characterisation; LOWESS smoothing; FDR-corrected group comparisons; effect sizes |
| `05b_feature_construction_and_selection.ipynb` | Features (refined) | Refined feature construction and selection pipeline for downstream modelling |
| `06b_supervised_temporal_modelling.ipynb` | Prediction | Supervised prediction with strict temporal validation; ROC/PR-AUC; calibration; feature importance |
| `07b_survival_analysis_temporal_validation.ipynb` | Survival | Penalised Cox PH models; Kaplan–Meier risk stratification; bootstrap CIs; hazard ratio interpretation |

---

## Notebook Numbering

Notebooks `05b`, `06b`, and `07b` represent the refined final modelling pipeline, developed
after methodological review with supervisors. They supersede the earlier exploratory
versions (`06`, `07`) and constitute the definitive analytical results of the thesis.

---

## Data Availability

Input datasets are not included in this repository. All notebooks reference data stored
on Azure ML (MED-Cli study, IRCCS San Raffaele Hospital) and cannot be executed without
access to the secure data environment.
