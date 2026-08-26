# SMAP-L4 & ERA5-Land Soil Moisture Analysis Across Iowa

### Pixel-wise comparison, drought diagnostics, soil-moisture persistence, hydroclimate regimes, and next-month predictability

![Status](https://img.shields.io/badge/status-analysis%20complete-brightgreen)
![SMAP](https://img.shields.io/badge/data-SMAP%20L4-blue)
![ERA5-Land](https://img.shields.io/badge/data-ERA5--Land-0b7285)
![Period](https://img.shields.io/badge/period-2020--2025-orange)
![Model](https://img.shields.io/badge/ML-Random%20Forest-purple)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

A reproducible remote-sensing and reanalysis workflow for evaluating how **NASA SMAP Level-4** and **ERA5-Land** represent surface soil-moisture variability across Iowa, with additional analyses of drought behavior, temporal persistence, hydroclimate regimes, and one-month-ahead soil-moisture predictability.

---

## Graphical workflow

<p align="center">
  <img src="workflow_verified.png" width="100%">
</p>

---

## Overview

This repository contains the analysis workflow and selected outputs from a statewide comparison of **SMAP Level-4 surface soil moisture** and **ERA5-Land surface-layer soil moisture** across Iowa.

The analysis is designed to answer several related questions:

- How closely do SMAP and ERA5-Land agree spatially and temporally?
- How much of their disagreement is associated with systematic bias versus random variability?
- Does their agreement change by season and during the May–September growing season?
- How consistently do the two products identify soil-moisture drought?
- How persistent are soil-moisture anomalies in each dataset?
- Are there spatially distinct hydroclimate regimes of dataset agreement?
- How predictable is next-month SMAP soil-moisture anomaly using SMAP, ERA5-Land, or combined predictors?

The workflow uses both **statewide domain-average** and **pixel-wise** analyses rather than relying on a single summary statistic.

> **Important:** This is a SMAP–ERA5-Land intercomparison. No independent in-situ soil-moisture dataset is used as ground truth, so differences should be interpreted as **dataset disagreement**, not strictly as ERA5-Land error relative to truth.

---

## Project status

| Component | Status |
|---|---|
| SMAP monthly processing | Complete |
| ERA5-Land monthly processing | Complete |
| Spatial harmonization and Iowa clipping | Complete |
| Domain-average statistical comparison | Complete |
| Pixel-wise agreement analysis | Complete |
| Trend and change-point analysis | Complete |
| Seasonal and growing-season analysis | Complete |
| Soil-moisture drought diagnostics | Complete |
| Atmospheric-control analysis | Complete |
| Soil-moisture memory and persistence | Complete |
| Hydroclimate regime classification | Complete |
| Random Forest predictability analysis | Complete |

---

## Study design

| Element | Specification |
|---|---|
| **Study area** | Iowa, USA |
| **Study period** | 2020–2025 |
| **Pixel-wise time steps** | 72 monthly time steps |
| **Domain comparison** | 71 common monthly observations |
| **Valid pixels** | 1,807 |
| **SMAP product** | SPL4SMGP v008 |
| **SMAP variable** | Surface soil moisture, approximately 0–5 cm |
| **SMAP processing** | 3-hourly granules aggregated to monthly means |
| **ERA5-Land variable** | `swvl1`, approximately 0–7 cm |
| **ERA5-Land processing** | Daily data aggregated to monthly values |
| **Target grid** | SMAP pixel locations |
| **Drought threshold** | Standardized soil-moisture anomaly `z ≤ −1` |
| **Forecast target** | Next-month SMAP surface soil-moisture anomaly |
| **Machine-learning model** | Random Forest regression |

---

## Analytical workflow

### 1. Spatial and statistical comparison

SMAP and ERA5-Land were harmonized to a common spatial framework, clipped to the Iowa boundary, and compared at both statewide and individual-pixel scales.

Performance statistics include:

- Pearson correlation coefficient (`r`)
- Mean bias
- RMSE
- unbiased RMSE (`ubRMSE`)
- Kling–Gupta Efficiency (`KGE`)
- Moran's I for spatial autocorrelation of the bias field

For the statewide raw monthly comparison:

| Metric | Value |
|---|---:|
| Pearson `r` | 0.8861 |
| Bias | +0.0481 m³/m³ |
| RMSE | 0.0535 m³/m³ |
| ubRMSE | 0.0236 m³/m³ |
| KGE | 0.7716 |

---

### 2. Trend and change-point analysis

Temporal behavior was examined independently for SMAP and ERA5-Land using:

- Mann–Kendall trend test
- Sen's slope
- Pettitt change-point test
- Spatial comparison of trend direction and change-point timing

---

### 3. Seasonal and growing-season analysis

Agreement statistics were recomputed for:

- DJF — winter
- MAM — spring
- JJA — summer
- SON — autumn
- May–September agricultural growing season

This allows dataset disagreement to be evaluated separately from the strong annual soil-moisture cycle.

---

### 4. Soil-moisture drought diagnostics

Monthly standardized anomalies were calculated independently for each calendar month.

A drought month is defined as:

\[
z \leq -1
\]

Consecutive drought months were grouped into events.

Calculated diagnostics include:

- number of drought events
- number of drought months
- mean and maximum event duration
- mean and maximum severity
- SMAP–ERA5 drought agreement
- probability of detection (`POD`)
- false alarm ratio (`FAR`)

---

### 5. Atmospheric controls of dataset disagreement

The signed monthly bias,

\[
\mathrm{Bias} = \mathrm{ERA5-Land} - \mathrm{SMAP},
\]

was evaluated against ERA5-Land atmospheric variables:

- precipitation
- 2-m air temperature
- vapor pressure deficit (`VPD`)
- net radiation
- wind speed

The notebook computes driver-specific correlations/regressions and contrasts high- and low-disagreement periods.

---

### 6. Soil-moisture memory and persistence

Temporal persistence was quantified from the autocorrelation function (`ACF`) of standardized monthly soil-moisture anomalies.

Memory timescale was defined as the first lag where:

\[
ACF \leq \frac{1}{e}
\]

Executed notebook results:

| Dataset | Mean memory |
|---|---:|
| SMAP L4 | 2.870 months |
| ERA5-Land | 1.592 months |
| ERA5 − SMAP | −1.278 months |

Mean lag-1 autocorrelation was approximately:

- **SMAP:** 0.630
- **ERA5-Land:** 0.382

Drought persistence was also calculated as the conditional probability that a drought month is followed by another drought month.

---

### 7. Hydroclimate regime classification

K-means clustering integrated **17 diagnostic features** derived from the preceding analyses:

1. bias
2. RMSE
3. ubRMSE
4. KGE
5. Pearson `r`
6. ERA5−SMAP drought-event-frequency difference
7. drought detection agreement
8. probability of detection
9. false alarm ratio
10. drought-severity difference
11. soil-moisture-memory difference
12. drought-persistence difference
13. bias–precipitation correlation
14. bias–VPD correlation
15. bias–temperature correlation
16. bias–radiation correlation
17. bias–wind correlation

Candidate solutions from **k = 2 to 7** were evaluated.

The executed notebook selected:

- **Best k = 2**
- **Silhouette score = 0.203**
- **Regime 1:** 777 pixels
- **Regime 2:** 1,030 pixels

Regime names are intentionally kept neutral because the notebook's automatic semantic-labeling step assigned the same descriptive label to both clusters.

---

## Random Forest predictability analysis

The forecast target is **next-month SMAP surface soil-moisture anomaly**.

Three statewide monthly predictor configurations were evaluated:

1. ERA5-only
2. SMAP-only
3. Combined ERA5 + SMAP

Predictors include current and lagged anomalies, rolling means, seasonal encoding, and atmospheric variables where applicable.

### Statewide monthly RF results

The modeling table contains **68 monthly samples** spanning March 2020 through November 2025, with **58 training samples and 10 testing samples**.

| Model | R² | RMSE | MAE | Bias |
|---|---:|---:|---:|---:|
| ERA5-only | −0.241 | 0.0255 | 0.0221 | −0.0123 |
| **SMAP-only** | **0.180** | **0.0207** | **0.0180** | −0.0104 |
| Combined ERA5 + SMAP | −0.040 | 0.0233 | 0.0204 | −0.0122 |

For this statewide monthly experiment, **SMAP-only produced the highest test R²**.

### Combined pixel-wise RF

A separate combined pixel-wise Random Forest experiment used:

- **1,807 pixels**
- **113,841 total samples**
- **101,192 training samples**
- **12,649 testing samples**

Performance:

| Metric | Value |
|---|---:|
| R² | **0.433** |
| RMSE | **0.685 z-units** |
| Bias | **−0.201** |

The example forecast mapped in the notebook is:

**July 2025 → August 2025**

The statewide monthly and pixel-wise experiments are separate analyses and their performance metrics should not be interpreted interchangeably.

---

# Selected figures

## Pixel-wise mean bias

<p align="center">
  <img src="fig02_bias.png" width="80%">
</p>

**Figure 2.** Pixel-wise mean bias between ERA5-Land and SMAP surface soil moisture.

---

## Seasonal and growing-season bias

<p align="center">
  <img src="fig16_seasonal_bias.png" width="90%">
</p>

**Figure 16.** Seasonal ERA5-Land − SMAP bias for DJF, MAM, JJA, SON, and the May–September growing season.

---

## Soil-moisture drought diagnostics

<p align="center">
  <img src="fig27_drought_diagnostics.png" width="90%">
</p>

**Figure 27.** SMAP–ERA5-Land drought diagnostics including drought occurrence, dataset differences, agreement, POD, and FAR.

---

## Hydroclimate regime selection

<p align="center">
  <img src="fig44_kmeans_selection.png" width="80%">
</p>

**Figure 44.** K-means cluster-selection diagnostics. The executed notebook evaluates `k = 2–7` and selects **k = 2** using the maximum silhouette score.

---

## Statewide monthly Random Forest comparison

<p align="center">
  <img src="fig49_monthly_rf.png" width="90%">
</p>

**Figure 49.** Comparison of R², RMSE, and MAE for ERA5-only, SMAP-only, and combined monthly Random Forest models. **SMAP-only provides the highest test R² in the executed notebook.**

---

## Combined pixel-wise prediction

<p align="center">
  <img src="fig56_combined_forecast.png" width="80%">
</p>

**Figure 56.** Combined ERA5-Land + SMAP Random Forest prediction of next-month SMAP anomaly for August 2025.

---

## Repository structure

```text
SMAP-L4_ERA5_Soil-Moisture_Iowa/
│
├── README.md
├── LICENSE
├── notebooks/
│   └── SEES_3500_SMAP_ERA5_Iowa.ipynb
│
├── data/
│   ├── README.md
│   ├── smap_monthly/
│   └── era5/
│
├── outputs/
│   └── README.md
│
├── workflow_verified.png
├── fig02_bias.png
├── fig16_seasonal_bias.png
├── fig27_drought_diagnostics.png
├── fig44_kmeans_selection.png
├── fig49_monthly_rf.png
└── fig56_combined_forecast.png
