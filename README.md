# Credit Risk Modeling — CreditMetrics Model

University project for the course **Credit and Operational Risk Methods**.  
Implementation of the **CreditMetrics** model (J.P. Morgan, 1997) to calculate **99.9% VaR** and **ES** for a portfolio of three zero-coupon government bonds.

## Overview

The project applies the CreditMetrics framework to measure credit risk on a 1-year horizon, accounting for both default risk and the risk of rating migrations (upgrades and downgrades). Two scenarios are compared:
- **Independent migrations** (no correlation between bonds)
- **Correlated migrations** (r₁₂ = 0.2, r₁₃ = 0.15, r₂₃ = 0.4)

## Portfolio

| # | Rating | Maturity | Face Value | Seniority         |
|---|--------|----------|------------|-------------------|
| 1 | BBB    | 3 years  | 100,000    | Subordinated      |
| 2 | B      | 5 years  | 50,000     | Senior Secured    |
| 3 | CCC    | 2 years  | 50,000     | Senior Unsecured  |

## Methodology

1. **Input data** — transition matrix, forward zero rates per rating class, recovery rates by seniority class
2. **Step I — Bond revaluation** — value each bond after 1 year under every possible end-of-year rating (including default)
3. **Step II — Thresholds on the N(0,1) axis** — for each bond, partition the standard normal axis into 8 intervals matching the rating transition probabilities
4. **Step III — Monte Carlo simulation** — 100,000 scenarios; map random draws to end ratings and aggregate portfolio values
5. **Correlated variant** — generate correlated normal vectors via Cholesky decomposition of the correlation matrix
6. **Risk measures** — 99.9% VaR and ES from the simulated portfolio value distribution

## Results

| Variant                      | 99.9% VaR | 99.9% ES |
|------------------------------|-----------|----------|
| No correlation               | 53,657    | 59,608   |
| With correlation             | 53,673    | 62,631   |

Positive correlations increase risk measures — most visibly the ES (+3,023) — because joint downgrades and defaults become more frequent in the tail. This illustrates the diversification effect: lower correlation between assets reduces aggregate portfolio risk without changing the expected value.

## Correlation Estimation Methods

The report also covers four approaches to estimating correlations in CreditMetrics, based on the technical documentation:
- Direct estimation from rating migration history
- Estimation via bond spreads
- Asset value model (Merton-based, standard approach)
- Asset correlations from country/industry equity indices

## Files

- `Projekt_CreditMetrics.Rmd` — main R Markdown source file
- `Projekt_CreditMetrics.html` — compiled report
- `dane_cm.xlsx` — input data (rating transition matrix)

## Requirements

R packages:
```r
install.packages(c("readxl", "rmarkdown", "knitr"))
```

## Notes

Bonds are treated as zero-coupon (only face value is paid at maturity, no coupons in between). Recovery rates are taken as constant averages per seniority class, the extended version of the model treating recovery as a Beta-distributed random variable is not implemented here.
