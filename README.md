This project aims to build a model to analyse the relationship between populism and violence against jounralist by modelling physical violence in Europe using Zero-Inflated Negative Binomial (ZINB) regression.

## Project Structure
- **MODEL.ipynb** — main notebook: data cleaning, merging, EDA, and all models
- **DATA/** — raw and processed datasets (see below)
- **final_paper.pdf** - write up of the research paper

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

## Paper executive summary
This project explores the relationship between populist governance, media capture, and physical
violence against journalists in Europe. Using the Mapping Media Freedom dataset’s incident
data amongst literature-informed variables, we create five Zero-Inflated Negative Binomial
predictive models to estimate counts of physical violence against journalists. Control variables
includ poverty, military spending, and judicial independence, while other models use an altered
version of Reporters Without Borders’ media-capture index, seat-weighted populist presence
from the PopuList and ParlGov datasets, as well as gender composition of journalist victims
and the leading political cabinet. A feminist critical lens was applied to incorporate gender into
the analysis, drawing on gender data available in existing datasets, while six semi-structured
interviews provided additional qualitative insights.
Key Findings include:
- A one-standard-deviation increase in the populist share of a European government is associated with nearly fifty percent more physical violence counts against journalists.
- Adding populism variables improves the models’ fit to seen data, but not unseen, making it unreliable for predicting.
- Government attacks on the judiciary only become significant after including populism, which links populist rule to general democratic failure
- Media capture, weighted populism, and gender composition add minimal predictive power, but instead add model explainability
- Weighted populism and its interaction with media capture sharpen the significance of institutional predictors and link populist governance and attacks on the judiciary to higher rates of violence against journalists.
- Interviews concur that political polarisation and outlet independence are generally more significant than the gender of a journalist.
- Partial populist presence in government is sufficient to correlate with higher rates of physical violence against journalists.
- Populism and government attacks on the judiciary pose a positive feedback loop with a compounding effect on journalist safety.
- Adding gender composition of the cabinet and the attacked journalists does not improve the prediction.
