# ISA 444 — Hotel Demand Forecasting Project

**Kenny Biggs & Max Buksbaum**

---

## Overview

This project forecasts daily room demand for 17 hotel properties over a 28-day forecast horizon. We compare 7 forecasting models across baseline, statistical, machine learning, and neural forecasting families using 5-fold time-series cross-validation.

**Forecast horizon:** 28 days  
**Number of series:** 17 hotels  
**Cross-validation:** 5-fold, non-overlapping, step size = 28  
**Evaluation metrics:** ME/bias, MAE, RMSE, MAPE  

---

## Forecasting Task

The goal of this project is to forecast daily hotel demand for each property in the hotel dataset. Each hotel is treated as its own time series using the standard Nixtla format:

| Column | Meaning |
|---|---|
| `unique_id` | Hotel identifier |
| `ds` | Date |
| `y` | Room demand / occupancy target |

The final 28 days of each hotel series were held out as the test set. The remaining observations were used for training and cross-validation.

---

## Models Compared

| Category | Model |
|---|---|
| Baseline | Naive, Seasonal Naive |
| Statistical | AutoETS, AutoARIMA |
| Machine Learning | LightGBM via MLForecast |
| Neural | NBEATS, NHITS via NeuralForecast |

---

## Data Preparation

The dataset was sorted by hotel and date. The final 28 days of each hotel series were held out as a test set. Missing daily gaps were handled so models requiring regular daily spacing could run correctly.

Some hotels had very low or near-zero demand, which affected percentage-based metrics like MAPE. Because of this, MAE and RMSE were emphasized more heavily when interpreting final performance.

---

## Evaluation Method

Models were evaluated using 5-fold time-series cross-validation with a 28-day forecast horizon. The project reports performance using:

- **ME / Bias:** Measures whether forecasts tend to overpredict or underpredict
- **MAE:** Measures average absolute forecast error
- **RMSE:** Penalizes larger forecast errors more heavily
- **MAPE:** Measures percentage error, when appropriate

A model “win” means that the model achieved the lowest error for a specific metric on a specific hotel series.

---

## Cross-Validation Results

LightGBM was the dominant model during cross-validation, winning the most series across all four metrics.

| Metric | 1st | 2nd | 3rd |
|---|---|---|---|
| Bias | LightGBM (95) | NHITS (52) | NBEATS (43) |
| MAE | LightGBM (95) | NHITS (48) | NBEATS (47) |
| RMSE | LightGBM (95) | NHITS (49) | NBEATS (46) |
| MAPE | LightGBM (86) | NBEATS (50) | NHITS (36) |

These results suggest that LightGBM performed especially well during cross-validation, likely because it could learn flexible patterns from lagged demand behavior and calendar-based features.

---

## Final Test Results

On the final 28-day holdout set, results were more competitive. Naive and AutoARIMA performed more strongly on the final test set, suggesting that LightGBM may have overfit the cross-validation windows.

| Metric | 1st | 2nd | 3rd |
|---|---|---|---|
| Bias | AutoETS (6) | AutoARIMA (4) | SeasonalNaive (3) |
| MAE | Naive (6) | NBEATS (4) | AutoARIMA (3) |
| RMSE | LightGBM (5) | Naive (4) | AutoARIMA (3) |
| MAPE | Naive (6) | LightGBM (4) | AutoETS (3) |

The final test results show that simpler models can still be competitive, especially when recent demand levels are highly informative.

---

## Forecast Plot Examples

The forecast plots compare each hotel’s historical demand with the 28-day holdout forecasts. The black solid line represents the training data, the black dashed line represents the actual holdout period, and the colored lines represent forecasts from each model.

Across the hotel series, the plots show that some properties have stable, high demand while others are more volatile or low-demand. This supports the need for cross-validation because no single model performs equally well for every hotel.

Forecast plot files are included in the `plots/` folder.

---

## Key Findings

- LightGBM dominated cross-validation across all metrics but did not translate as cleanly to the final test set, suggesting some overfitting to the cross-validation windows.
- Naive was surprisingly competitive on the test set, winning the most series on MAE and MAPE. This indicates that for some hotels, recent demand levels are the best predictor of near-term demand.
- NBEATS and NHITS were consistently second or third across both cross-validation and test evaluation, showing that neural models added value over pure statistical approaches.
- `hotel_28` and `hotel_77` are near-zero demand series. MAPE is not reported for these hotels because it is undefined or misleading when actual values are zero or close to zero.
- MAPE is unreliable for low-demand hotels. MAE and RMSE were treated as the primary metrics for those series.
- Model performance varied by hotel, meaning the best forecasting method depends on each property’s demand pattern.

---

## Business Interpretation

Accurate 28-day hotel demand forecasts can help hotels make better short-term decisions around staffing, pricing, room allocation, and operational planning.

The results suggest that machine learning and neural models can be useful for capturing complex demand patterns. However, simpler models such as Naive and AutoARIMA remained competitive on the final holdout set. This shows that hotels should not automatically assume the most complex model will always perform best.

A practical forecasting strategy would use cross-validation to select the best model by property, rather than applying one universal model to every hotel.

---

## Limitations

Some hotel series contained near-zero or zero demand values, which made MAPE unreliable. In these cases, percentage errors can become undefined or misleadingly large.

Also, LightGBM performed extremely well during cross-validation but less consistently on the final test set. This suggests possible overfitting and highlights the importance of using both cross-validation and final holdout testing.

---

## Repository Guide

| File/Folder | Description |
|---|---|
| `ISA_444_Final_Project.ipynb` | Main notebook with data preparation, modeling, evaluation, and plots |
| `hotel_project_outputs/` | CSV files containing cross-validation, final forecasts, and test results |
| `plots/` | Forecast-vs-actual plots for hotel series |
| `evaluation_all_models.csv` | Combined model evaluation results |
| `model_win_counts.csv` | Summary of how often each model won by metric |
| `final_test_evaluation.csv` | Final 28-day holdout test results |
| `final_forecasts_all_models.csv` | Final forecasts from each model |

---

## Reproducibility

To reproduce this project, open the notebook and run the cells in order. The notebook loads the hotel dataset, prepares the data, runs time-series cross-validation, evaluates models, generates final forecasts, and saves the required CSV outputs.

Required packages include:

- `pandas`
- `statsforecast`
- `mlforecast`
- `neuralforecast`
- `utilsforecast`
- `lightgbm`

---

## Conclusion

Overall, LightGBM was the strongest model during cross-validation, while the final holdout results showed that simpler models such as Naive and AutoARIMA could still be competitive. The main takeaway is that hotel demand patterns vary across properties, so model selection should be based on validation results rather than assuming one forecasting method is always best.
