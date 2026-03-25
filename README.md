# Bina.az Real Estate Price Prediction

**Course Project:** CSCI4734 – Machine Learning  
**Type:** Kaggle Competition  
**Author:** Nərgiz Agayeva 

---

## Overview

This project aims to **predict apartment prices in Baku** using a combination of real estate features, geographic coordinates, and proximity to landmarks. The dataset was provided as part of a course Kaggle-style competition (data is confidential and cannot be shared).  

The goal is to build a robust regression model capable of generalizing to unseen properties.

---

## Dataset

- **Train data:** `binaaz_train.csv`  
- **Test data:** `binaaz_test.csv`  
- **Landmarks data:** `baku_landmarks_clean.csv`  

> **Note:** The original and cleaned datasets cannot be publicly shared.

**Key feature types:**
- Numerical: area, number of rooms, floor information, latitude/longitude
- Categorical: district, poster type
- Boolean: property documents (`Kupça`), mortgage availability
- Text: title, description
- Derived features: distance to landmarks, floor ratio, area per room

---

## Preprocessing & Feature Engineering

1. **Cleaning numerical and categorical data**
   - Extracted numeric values from messy string formats.
   - Converted categorical and boolean features to machine-readable formats.
   - Imputed missing coordinates using district medians.

2. **Feature transformations**
   - Log transformation for `price` and `area`.
   - Computed derived features:
     - `floor_ratio` (current floor / total floors)
     - `area_per_room` and its logarithm
     - Distance to Baku city center (Haversine)
     - Distances to nearest landmarks per category using BallTree
     - Interaction features like `floor_x_area` and `metro_x_area`
     - Text lengths of title and description

3. **Frequency and label encoding**
   - Encoded `district`, `poster_type`, and `seher` using both label encoding and frequency encoding.
  
### Notes
All coordinates outside of reasonable Baku bounds were removed to reduce noise.
Landmark distance features were only computed for categories appearing at least 8 times.
Log-transformations were applied to reduce skewness.
For categorical features with missing or unknown values, a fallback group (seher) was used.

---

## Models

Multiple regression models were trained and combined using **stacking**:

- **Random Forest Regressors** (3 variants)
- **XGBoost Regressor** (if available)
- **LightGBM Regressor** (if available)
- **Stacking:** Ridge regression on out-of-fold predictions for ensemble

**Model blending** with optimized weights using `scipy.optimize.minimize` was also implemented as an alternative.

---

## Validation

- **Cross-validation:** `GroupKFold` using district groups to prevent leakage.
- **Metric:** Mean Absolute Error (MAE) on log-transformed prices.
- **Out-of-fold predictions** were used to train the Ridge stacker and optimize ensemble weights.

---

## Prediction & Submission

1. Full models were retrained on the entire training set.
2. Predictions for test data were generated and transformed back to original price scale using exponentiation.
3. Predictions were clipped to remove outliers (1st and 99th percentiles).
4. Final submission file: `predictions3.csv` with columns:

## Results & Standing

The model was evaluated in the course Kaggle-style competition.  

| Metric / Info      | Value |
|-------------------|-------|
| My Score           | 30,668.07 |
| Rank               | 22 / 27 participants |
| Total Submissions  | 33 |
| Notes              | Consistently in the upper-mid range; strong baseline performance demonstrating robust feature engineering and ensemble modeling. |

>  Even with limited data sharing and a real-world messy dataset, the model achieved reliable predictions and meaningful rankings among the course participants.

```text
_id, price

