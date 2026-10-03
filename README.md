# m4k1

Group project modelling physical violence against journalists in Europe using Zero-Inflated Negative Binomial (ZINB) regression.

## Project Structure
- **MODEL.ipynb** — main notebook: data cleaning, merging, EDA, and all models
- **DATA/** — raw and processed datasets (see below)
- **Drafts/** — Failed analyses & earlier draft versions of the notebook

## Data Sources
- `DATA/MMF/` — Mapping Media Freedom: physical incident counts per country-year (outcome variable)
- `DATA/V-Dem-CD-v16_csv/` — V-Dem v16: judicial independence, government attacks on judiciary, media corruption, civil liberties
- `DATA/Econ_Data/` — Our World in Data: Gini coefficient, mean income, relative poverty headcount
- `DATA/military_data/` — World Bank: military expenditure (% of GDP)
- `DATA/PopuList/` — PopuList 3.0: populist party classifications (via parlgov_id)
- `DATA/ParlGov/` — ParlGov: cabinet composition and seat shares
- `DATA/RSF/` — RSF Press Freedom Index: annual press freedom scores (2018–2025)
- `DATA/weighted_populism_score.csv` — computed seat-weighted populist share of government
- `DATA/aggregated_data.csv` — final merged country-year panel used for modelling

## How to Run The Project
1. Install packages if needed: `pandas`, `numpy`, `matplotlib`, `seaborn`, `statsmodels`
2. Open and run `MODEL.ipynb` top to bottom 

## Pipeline
- **Data cleaning & merging** — loads all sources, standardises country names and years, merges into a single country-year panel
- **Outcome variable** — physical incidents (assault, injury, death, abduction, sexual assault, arson) aggregated from MMF, filtered to European countries 2015–2025
- **Populism score** — seat-weighted share of populist parties in government, matched via `parlgov_id`
- **RSF harmonisation** — pre-2022 uses composite score excluding Safety/exactions; 2022+ averages four structural sub-components for comparability and preventing circularity with what we are predicting (physical violence)
- **One-year lag** — all predictors are lagged one year to reduce reverse causality
- **Imputation** — forward-fill within country groups for slowly-changing structural indicators
- **Exclusions** — Russia and Ukraine removed as outliers due to ongoing armed conflict
- **EDA** — outcome distribution diagnostics confirming overdispersion and zero-inflation justify ZINB

## Models

All models are ZINB, estimated via BFGS MLE; continuous predictors are z-scored; evaluated on 70/30 hold-out + 5-fold CV. Coefficients reported as IRRs (exp(β)).

- **Model 1** — baseline controls: military exp., court independence, poverty, judiciary attacks
- **Model 2** — Model 1 + weighted populism (tests RQ1: does populism predict violence?)
- **Model 3** — Model 2 + populism × media capture interaction (tests RQ2: moderation by media capture)
- **Model 4** — Model 2 + gender power distribution (gender robustness check)
- **Model 5** — Model 3 split by journalist gender (male vs. female victim comparison)
