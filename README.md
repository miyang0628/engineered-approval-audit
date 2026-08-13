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
