# Engineered Approval Audit

Code and reproducibility materials for an anonymous submission under review.

> **Anonymity notice.** This repository is anonymized for double-blind review.
> Author names, affiliations, funding, and acknowledgments are intentionally omitted.
> Please do not attempt to de-anonymize the authors.

## Overview

This project develops a **measurement tool** — not a predictor — for auditing the
*epistemic robustness* of already-granted approvals, and applies it across
intersectional demographic groups. The central question is not who gets approved,
but how well-grounded each approval decision is, and whether that quality differs
in **kind** across groups.

The core metric (`EngineeredScore`) combines three axes computed in a financial-
behavior feature space:

- **Stability** — consistency of local SHAP attributions among neighbors
- **LowDensity** — inverse of counterfactual neighbor density
- **NonFragility** — robustness of the decision under input perturbation (Δp)

## Headline findings

- The score carries reliability signal **beyond predicted probability** (it is not
  confidence repackaging) and converges with realized default (external criterion).
- **Typicality paradox**: among borderline approvals, more *typical* (higher-density)
  cases default *more*, not less — robust within the primary dataset across random
  and temporal splits, but not externally generalized (see limitations).
- **Composition, not amount**: disadvantaged and advantaged approvals have
  *equal composite reliability* but a *robustly different composition* — disadvantaged
  approvals are simultaneously more stable yet more fragile. This gap survives a
  subsampling control and is a layer of unfairness invisible to approval-rate metrics.

## Repository structure

.
├── notebooks/ # analysis pipeline, run in order
│ ├── 00_gatekeeping.ipynb # data load, VIP label, viability gates
│ ├── 01_cohort_and_model.ipynb # cohort + audited model + group tags
│ ├── 02_indices.ipynb # three indices (real SHAP; compute-heavy)
│ ├── 03_ensemble_and_validity.ipynb# 3-axis EngineeredScore + four-fold validity
│ ├── 04_group_tests.ipynb # cross-group tests + subsampling control
│ ├── 05_external_validity.ipynb # external attempt + internal robustness
│ ├── 06_mechanism.ipynb # density-mediation hypothesis (tested, rejected)
│ └── 07_robustness.ipynb # bootstrap confidence intervals
├── data/ # source data (not redistributed; see below)
├── results/ # pipeline artifacts (parquet / npy / joblib / csv)
├── requirements.txt
└── README.md


## Data

Two public datasets are used. Neither is redistributed here; download them and
place the raw files under `data/`.

- **Primary**: *Default of Credit Card Clients* (Taiwan), UCI ML Repository
  (id 350). Loaded automatically via `ucimlrepo` on first run.
- **External-validity attempt**: *Give Me Some Credit* (public competition data).
  Place the training CSV/zip under `data/` as `gmsc_raw.parquet` (a small
  preparation step is documented in `05_external_validity.ipynb`).

## Reproducing

```bash
python -m venv .venv && source .venv/bin/activate   # or conda
pip install -r requirements.txt
# then run notebooks/00 through 07 in order (from inside notebooks/)
```

Notes:
- Notebooks read/write via relative paths (`../data`, `../results`) and are meant
  to be executed from inside `notebooks/`.
- `02_indices.ipynb` is compute-heavy: it fits the model under multiple seeds and
  computes SHAP values to establish a noise floor. Expect a long runtime on CPU.
  Downstream notebooks reuse its saved output in `results/`, so it need not be
  re-run to reproduce later stages.
- All randomness is seeded (`RANDOM_STATE = 42`).

## Requirements

Python 3.10+. Key packages: `numpy`, `pandas`, `scikit-learn`, `shap`,
`statsmodels`, `scipy`, `ucimlrepo`, `joblib`, `pyarrow`.

## License

Released under the MIT License for review purposes. See `LICENSE`.
