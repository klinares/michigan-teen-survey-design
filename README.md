# michigan-teen-survey-design

> *"We have computed the roh. We have linked the undersized schools. We have achieved epsem. There is nothing left to fear."*

![](images/clipboard-4054165354.png)

A reproducible two-stage stratified cluster sampling design for the Michigan Department of Education's (MDE) teen smoking and drug use survey. Developed as the team project for **SURVMETH/SURV 625: Applied Sampling** at the University of Maryland.

**Team Cochran:** Kevin Linares · Jianing Zou · Weishan Jiang · Xiaoqing Liu

------------------------------------------------------------------------

## Overview

The MDE needs statewide estimates of teenage smoking and drug use to assess compliance with tobacco industry settlements. This project designs a probability sample of grades 7–12 students across Michigan that meets client-specified precision targets (CV ≤ 0.05) within a \$500,000 data collection budget.

The design proceeds in two stages:

1.  **Stage 1 — Schools (PSUs):** Stratified probability-proportional-to-size (PPeS) systematic selection of schools across nine education regions.
2.  **Stage 2 — Students (SSUs):** Systematic random sampling of students within each selected school, maintaining equal probability of selection (epsem) across all strata.

------------------------------------------------------------------------

## Target Variables

| Outcome                       | Type       | Expected Value | Desired CV |
|-------------------------------|------------|----------------|------------|
| Ever smoked a cigarette       | Proportion | 0.25           | 0.05       |
| Ever smoked marijuana         | Proportion | 0.15           | 0.05       |
| Age first approached to smoke | Mean       | 12 (SD = 1)    | 0.05       |

------------------------------------------------------------------------

## Key Design Parameters

| Parameter | Value |
|----|----|
| Target population | 830,138 students across 2,443 schools |
| Explicit strata | 9 education regions |
| Optimal cluster size (m_opt) | 53 students/school |
| Schools to contact (adjusted for nonresponse) | \~293 |
| Schools expected to participate | \~88 |
| Students expected to complete | \~4,721 |
| Overall sampling fraction | 0.0057 |
| Total budget | \$500,000 |
| Cost per school | \$3,000 |
| Cost per student | \$50 |
| School response rate assumption | 30% |
| Student response rate assumption | 70% |

Design effects (from pilot study of n = 7,500 students across 150 schools) and derived intraclass correlation coefficients (roh):

| Outcome        | DEFF (pilot) | roh    |
|----------------|--------------|--------|
| smoked_cig     | 2.5          | 0.0306 |
| smoked_mj      | 2.0          | 0.0204 |
| age_approached | 1.7          | 0.0143 |

**Selected design: Option 2** (m_opt = 53, n_opt = 88, total n = 4,721, cost = \$497,200) — the only option in which all three variables pass the sampling variance check against the client's precision requirements.

------------------------------------------------------------------------

## Repository Structure

```         
michigan-teen-survey-design/
│
├── data/
│   ├── MI_school_frame_head_counts.xls   # MDE school frame with 2024 headcounts
│   ├── sample_selected.xlsx              # Selected schools after PPeS selection
│   └── schoolframe.csv                   # Student roster for one sample school (Region 7)
│
├── project_code.qmd                      # Weekly working notes (Weeks 4–13)
├── sampling_design_group_cochran.qmd     # Final report source (JASA format)
├── sampling_design_group_cochran.tex     # LaTeX output
│
└── README.md
```

------------------------------------------------------------------------

## Reproducing the Analysis

### Prerequisites

R 4.x with the following packages (loaded via `pacman`):

``` r
pacman::p_load(
  readxl, writexl,
  MESS,          # school linking
  kableExtra, knitr,
  sampling,
  viridis, ggthemes,
  tidyverse
)
```

### Data path

Update `repo_path` at the top of each `.qmd` to point to your local `data/` directory:

``` r
repo_path <- "path/to/your/data/"
```

### Render

The final report uses the JASA Quarto extension. To render to PDF:

``` bash
quarto render sampling_design_group_cochran.qmd --to jasa-pdf
```

To render the weekly working notes:

``` bash
quarto render project_code.qmd --to pdf
```

------------------------------------------------------------------------

## Design Summary by Week

`project_code.qmd` documents the incremental design decisions made across the semester:

| Week | Task |
|----|----|
| 4 | Element variances, desired sampling variances, SRS sample sizes |
| 5 | Proportionate allocation across nine strata |
| 6 | roh estimation from pilot study DEFFs; budget and cost parameters introduced |
| 7 | Optimum subsample sizes (m_opt, n_opt); alternative cluster design comparison |
| 8 | Stratified PPeS school selection; implicit stratification by grade composition; zone sizes; random starts |
| 10 | EPSEM verification; m_h_star per stratum; school linking for undersized PSUs |
| 11 | Within-school systematic sampling demonstrated on Region 7 middle school roster |
| 13 | Variance estimation model; pseudo-strata and SECU formation; degrees of freedom; subclass estimation for 20% subgroup |

------------------------------------------------------------------------

## Estimation Plan

Variance estimation uses a **paired selection model** (Taylor Series Linearization via the ratio estimator):

$$\widehat{\text{var}}(r) \approx \frac{1}{\hat{t}_x^2} \left[ \sum_h \text{var}(\hat{t}_{h,y}) + r^2 \sum_h \text{var}(\hat{t}_{h,x}) - 2r \sum_h \text{cov}(\hat{t}_{h,y}, \hat{t}_{h,x}) \right]$$

Regions 1–3 are collapsed into pseudo-stratum 1, regions 4–7 into pseudo-stratum 2, and regions 8–9 are each their own pseudo-stratum, yielding **4 pseudo-strata** and **318 SECUs** (314 degrees of freedom).

Although the design is epsem in theory, **weights are necessary in practice** to account for the 30% school and 70% student nonresponse adjustments applied during the contact phase.

------------------------------------------------------------------------

## Course Context

Completed as the team sampling project for **SURVMETH/SURV 625: Applied Sampling**, University of Maryland Joint Program in Survey Methodology (JPSM), Winter/Spring 2025. The project prompt was authored by the course instructor.

------------------------------------------------------------------------

## Authors

-   Kevin Linares (University of Maryland / Michigan ISR)
-   Jianing Zou (University of Maryland)
-   Weishan Jiang (University of Maryland)
-   Xiaoqing Liu (University of Maryland)
