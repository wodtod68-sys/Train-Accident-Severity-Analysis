# Train Accident Severity Analysis

**Evidence-Informed Recommendations for the Federal Railroad Administration (FRA)**

A multivariate statistical analysis of U.S. railroad accident data, investigating the drivers of accident severity and translating findings into actionable safety policy. Built for IMEN 415 (Multivariate Analysis), Project 1.

## Overview

This project tests four well-formed hypotheses across two severity metrics — **Accident Damage (`ACCDMG`)** and **Casualties (`TOTINJ + TOTKLD`)** — using FRA accident records filtered to extreme cases (damage above the boxplot upper whisker; casualties ≥ 1). Each hypothesis pairs a qualitative variable with train speed and is designed so that its outcome maps directly onto an FRA policy lever.

The central finding: **train speed is universally and strongly significant across every hypothesis**, with damage following a quadratic (kinetic-energy, E ∝ v²) relationship. Damage scales *multiplicatively* with infrastructure and train mass, while casualties scale *additively* — two distinct mechanisms that justify two distinct regulatory strategies.

## Hypotheses & Results

| # | Severity Metric | Interaction Hypothesis | Outcome |
|---|-----------------|------------------------|---------|
| H1 | ACCDMG | Location Type × Train Speed | **Reject H₀** — rural areas show steeper speed sensitivity |
| H2 | ACCDMG | Mega Train × Train Speed (CARS control) | **Reject H₀** — long trains amplify high-speed damage |
| H3 | Casualties | Accident Type × Train Speed (Derailment vs Collision) | Fail to Reject H₀ |
| H4 | Casualties | Track Type × Train Speed (OpenLine vs YardType) | Fail to Reject H₀ |

Interaction effects are **present for damage but absent for casualties** — confirmed consistently by both bootstrap confidence intervals and cross-validation. Where in-sample OLS suggested significance for casualty interactions (e.g. H4, p = 0.029), distribution-free methods revealed it to be overfitting to sample noise.

## Methodology

A consistent 10-step framework was applied to all hypotheses:

1. Hypothesis formulation with theoretical justification
2. Exploratory data analysis and feature engineering
3. Sequential linear models (main effects → interaction → second-order)
4. Model assessment (Adjusted R², AIC, BIC, Partial F-test)
5. Diagnostic verification (residuals, normality, homoscedasticity)
6. Box-Cox transformation for variance stabilization
7. Multicollinearity assessment via VIF and centering
8. Bootstrap confidence intervals (R = 1,000) for distribution-free inference
9. Cross-validation for out-of-sample predictive validation
10. Poisson GLM as a supplementary robustness check

## Data & Feature Engineering

- **Source:** FRA railroad accident database, combined across years and de-duplicated on incident keys.
- **Filtered sets:** 8,810 high-damage incidents (`ACCDMG` > upper whisker) and 4,027 casualty incidents (`Casualties` ≥ 1).
- **`LOCATION_TYPE`** — binary factor from great-circle distance to 25 major U.S. cities (Urban if within 200 km, otherwise Rural), proxying emergency-response availability.
- **`MEGA_TRAIN`** — binary factor on total cars (Yes if ≥ 100 cars), capturing cascading-pile-up risk.
- **`CARS`** — count of hazmat-loaded cars, retained as a control on its original scale.
- **`I(TRNSPD^2)`** — quadratic speed term reflecting the kinetic-energy structure of severity.

## Key Findings & FRA Recommendations

1. **Universal speed reduction is the single most effective intervention.** Speed is significant everywhere; a 10% reduction (60→54 mph) cuts kinetic energy ~19%. Recommend uniform speed-cap enforcement via PTC-integrated automatic governors.
2. **Rural areas are "hidden time bombs" for high-speed damage.** Rural damage curves escalate faster at speed due to slower emergency response. Recommend reallocating preventive-maintenance budget and pre-positioning rural response equipment.
3. **Mega-trains require mass-speed coupled regulation.** The mass × speed interaction is strongly positive. Recommend mega-train-specific speed caps, ECP brakes on long hazmat trains, and buffer cars between hazmat blocks.
4. **Casualty mitigation must target train type, not just track or accident type.** Commuter trains independently raise casualties; YardType has ~12% lower baseline casualties than OpenLine. Recommend a train-type-stratified strategy prioritizing passenger/commuter routes.
5. **Derailment prevention needs prioritized enforcement to fill the PTC coverage gap.** PTC suppresses high-speed collisions but is architecturally blind to derailments. Recommend a derailment risk index, mandatory ATGMS deployment, and an ATGMS↔PTC integration roadmap.

## Tech Stack

- **Language:** R
- **Libraries:** `ggplot2`, `dplyr`, `car`, `MASS`, `ggfortify`, `boot`, `geosphere`, `maps`, `gridExtra`

## Repository Structure

```
.
├── AccidentInput.R         # Data loading & combination helpers
├── Train Data/             # FRA accident data files (by year)
├── analysis/               # Hypothesis analysis scripts
└── Train_accident_analysis.pdf  # Full report
```

> **Note:** Update paths in the data-loading section before running. The original script uses local paths (`traindir`, `sourcedir`) that should point to your `Train Data` directory and `AccidentInput.R`.

## Authors

- 최정우 (2022170841)
- 천세환 (2022170838)
- 곽준우 (2022170807)

*IMEN 415 — Multivariate Analysis · 2026-05-07*
