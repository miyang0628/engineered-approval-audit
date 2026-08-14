# Engineered Approval Audit

Code and reproducibility materials for an anonymous submission under review.

## Anonymity notice

This repository is anonymized for double-blind review. Author names,
affiliations, funding sources, and acknowledgments are intentionally omitted.
Please do not attempt to de-anonymize the authors.

## Overview

This project develops a **measurement tool — not a predictor** — for auditing
the epistemic robustness of already-granted approvals, and applies it across
intersectional demographic groups. The central question is not *who* gets
approved, but *how well-grounded* each approval decision is, and whether that
quality differs across groups.

A key methodological finding is that approval quality does **not** reduce to a
single composite score. The audit instead yields **two independent diagnostics**
computed in a financial-behavior feature space:

* **Diagnostic 1 — Stability**: consistency of local SHAP attributions among
  neighbors (noise-floor corrected).
* **Diagnostic 2 — Typicality / Density**: inverse counterfactual neighbor
  density; the diagnostic signal is the *typicality paradox*.

A third candidate axis (NonFragility) was **excluded** by a pre-registered
independence rule (it duplicates density; see below), and a naive composite
ensemble was **dropped** because it has no convergent validity.

## Headline findings

* **Two diagnostics, not one score.** The two diagnostics are independent
  (raw |r| = 0.28) and each converges with realized default in the *same*
  positive direction. A composite that averages them cancels their signals
  (Stability enters positively, LowDensity negatively) and fails convergent
  validity — so the audit is reported as two separate diagnostics.
* **Typicality paradox (Diagnostic 2).** Among borderline approvals, more
  typical (higher-density) cases default *more*, not less
  (density -> default coef +0.976, 95% CI [+0.332, +1.639]). Robust within the
  primary dataset across random splits; not externally generalized (see
  limitations).
* **Stability profile differs across groups (Diagnostic 1).** Disadvantaged
  borderline approvals are more locally stable than advantaged ones
  (gap +0.086, 95% CI [+0.045, +0.125]) — a layer invisible to approval-rate
  metrics. Stability is a measure of local consistency, not of safety.

All confidence intervals are two-sided nonparametric bootstrap (2000 draws).

## Repository structure

```
.
├── notebooks/                          # analysis pipeline, run in order
│   ├── 00_gatekeeping.ipynb                # data load, VIP label, viability gates
│   ├── 01_cohort_and_model.ipynb           # cohort + audited model + group tags
│   ├── 02_indices.ipynb                    # three indices (real SHAP; compute-heavy)
│   ├── 03_diagnostics_and_validity.ipynb   # two diagnostics + validity; ensemble dropped
│   ├── 04_group_tests.ipynb                # cross-group tests (Stability)
│   ├── 05_external_validity.ipynb          # external attempt + internal robustness
│   ├── 06_mechanism.ipynb                  # density-NonFragility overlap (axis-exclusion basis)
│   ├── 07_robustness.ipynb                 # two-sided bootstrap CIs
│   └── 08_paper_figures.ipynb              # paper tables + figures (grayscale, 600 dpi)
├── data/                               # source data (not redistributed; see below)
├── results/                            # pipeline artifacts (parquet / csv)
├── figures/                            # generated figures (600-dpi grayscale PNG)
├── tables/                             # generated tables (csv + LaTeX booktabs)
├── requirements.txt
└── README.md
```

## Data

Two public datasets are used. Neither is redistributed here; download them and
place the raw files under `data/`.

* **Primary:** Default of Credit Card Clients (Taiwan), UCI ML Repository
  (id 350). Loaded automatically via `ucimlrepo` on first run.
* **External-validity attempt:** Give Me Some Credit (public competition data).
  Place the training file under `data/` as `gmsc_raw.parquet` (a small
  preparation step is documented in `05_external_validity.ipynb`).

## Reproducing

```
python -m venv .venv && source .venv/bin/activate   # or conda
pip install -r requirements.txt
# then run notebooks/00 through 08 in order (from inside notebooks/)
```

Notes:

* Notebooks read/write via relative paths (`../data`, `../results`,
  `../figures`, `../tables`) and are meant to be executed from inside
  `notebooks/`.
* `02_indices.ipynb` is compute-heavy: it fits the model under multiple seeds
  and computes SHAP values to establish a noise floor. Expect a long runtime on
  CPU. Downstream notebooks reuse its saved output in `results/`, so it need not
  be re-run to reproduce later stages.
* `08_paper_figures.ipynb` reads only frozen artifacts in `results/`; it
  recomputes nothing except trivial reshaping, and derives three small summary
  tables from existing parquet output.
* All randomness is seeded (`RANDOM_STATE = 42`).

## Design decisions recorded in code

For transparency, three consequential decisions are made explicit and testable
in the notebooks rather than only in the paper:

* **NonFragility excluded (pre-registered).** Raw Density–NonFragility
  correlation is 0.71 > the pre-registered 0.70 threshold; `06_mechanism.ipynb`
  confirms the overlap (density explains ~47% of NonFragility variance). It is
  reported in an appendix only.
* **Subsampling control dropped.** An earlier fixed-smaller-group subsampling
  control understated variance; it is replaced by the two-sided bootstrap in
  `07_robustness.ipynb`.
* **Composite ensemble dropped.** The averaged ensemble has no convergent
  validity (its CI straddles zero) because the two diagnostics enter with
  opposite signs; this is documented in `03` and `07`.

## Requirements

Python 3.10+. Key packages: `numpy`, `pandas`, `scikit-learn`, `shap`,
`statsmodels`, `scipy`, `ucimlrepo`, `joblib`, `pyarrow`, `matplotlib`.

## License

Released under the MIT License for review purposes. See `LICENSE`.
