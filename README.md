# ISA 444 — Hotel Demand Forecasting Project

**Kenny Biggs & Max Buksbaum**

## Overview
This project forecasts daily room demand for 17 hotel properties over a
28-day horizon. We compare 7 models across statistical, machine learning,
and neural forecasting families using 5-fold time-series cross-validation.

**Forecast horizon:** h = 28 days  
**Number of series:** 17 hotels  
**Cross-validation:** 5-fold, non-overlapping, step_size = 28  
**Evaluation metrics:** ME (bias), MAE, RMSE, MAPE

---

## Models Compared

| Category | Model |
|---|---|
| Baseline | Naive, Seasonal Naive |
| Statistical | AutoETS, AutoARIMA |
| Machine Learning | LightGBM (via MLForecast) |
| Neural | NBEATS, NHITS (via NeuralForecast) |

---

## Cross-Validation Results (5-fold)

LightGBM was the dominant model during cross-validation, winning the most
series across all four metrics.

| Metric | 1st | 2nd | 3rd |
|---|---|---|---|
| Bias | LightGBM (95) | NHITS (52) | NBEATS (43) |
| MAE | LightGBM (95) | NHITS (48) | NBEATS (47) |
| RMSE | LightGBM (95) | NHITS (49) | NBEATS (46) |
| MAPE | LightGBM (86) | NBEATS (50) | NHITS (36) |

---

## Final Test Results (28-day holdout)

On the final test set, results were more competitive. Naive and AutoARIMA
performed more strongly, suggesting LightGBM may have overfit to the
cross-validation windows.

| Metric | 1st | 2nd | 3rd |
|---|---|---|---|
| Bias | AutoETS (6) | AutoARIMA (4) | SeasonalNaive (3) |
| MAE | Naive (6) | NBEATS (4) | AutoARIMA (3) |
| RMSE | LightGBM (5) | Naive (4) | AutoARIMA (3) |
| MAPE | Naive (6) | LightGBM (4) | AutoETS (3) |

---

## Key Findings

- **LightGBM dominated cross-validation** across all metrics but did not
  translate as cleanly to the final test set, suggesting some overfitting
  to the CV windows.
- **Naive was surprisingly competitive on the test set**, winning the most
  series on MAE and MAPE. This indicates that for some hotels, recent demand
  levels are the best predictor of near-future demand.
- **NBEATS and NHITS were consistently 2nd or 3rd** across both CV and
  test evaluation, showing neural models add value over pure statistical
  approaches.
- **hotel_28 and hotel_77 are near-zero demand series.** MAPE is not
  reported for these hotels as it is undefined at near-zero values. All
  models performed poorly on these series due to lack of signal in
  the training data.
- **MAPE is unreliable** for low-demand hotels. We rely on MAE and RMSE
  as primary metrics for those series.

---

## Repository Structure
