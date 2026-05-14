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
