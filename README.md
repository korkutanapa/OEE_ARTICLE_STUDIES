
This is the details of 000_Master_Copy_GM2 file that is the base code for the article "A Novel Algorithmic Framework for Time-Series Prediction via Topological Data Analysis". TRAIAL_SET_10_GM2 is the related dataset for the code. 

---

# 000_Master_Copy_GM2 – GM2 Time Series Forecasting Pipeline

This notebook implements the **complete forecasting pipeline for the GM2 dataset**, integrating **decomposition, statistical and topological feature extraction, feature selection, and SARIMAX-based forecasting**. It provides a full experimental framework to compare **benchmarks** with **TDA-enhanced exogenous SARIMAX models**.

---

## Notebook Objectives

* Decompose the GM2 time series using **MSTL**
* Extract:

  * **Statistical features** via `tsfresh`
  * **Topological features** via `giotto-tda`
* Apply **feature selection** using:
  * Recursive Feature Elimination (RFE)
  * Particle Swarm Optimization (PSO)
* Train and evaluate multiple **SARIMAX forecasting models**
* Compare **baseline, decomposition-only, statistical-feature-based, and TDA-feature-based models**

---

## Input Dataset

* **Primary time series**

  * `TRIAL_SET_10_GM2ts.xlsx`
  * Target column: `Operation`

---

## Environment & Dependencies

The notebook fixes library versions for reproducibility:

* `numpy == 1.23`
* `pandas == 1.5.3`
* `scipy == 1.7.3`
* `tsfresh == 0.20.3`
* `giotto-tda`
* `statsmodels`
* `pyswarm`
* `scikit-learn`
* `matplotlib`

---

## Pipeline Overview

### 0. Data Loading & Exploratory Analysis

* Loads GM2 `Operation` time series
* Performs:
  * Histogram analysis
  * Skewness & kurtosis
  * KPSS stationarity test
  * ACF / PACF visualization
* Initializes a global results table `main_results` for:
  * MAE
  * MAPE
  * Runtime (“Cost”)

---

### 1. MODEL 0 – Baseline SARIMA (No Decomposition)

* Fits SARIMA directly on raw `Operation`
* Orders determined via KPSS & auto-arima logic
* Test set: **last 4 time steps**
* Metrics: **MAE, MAPE**
* Stored as:

  ```
  BASELINE
  ```

---

### 2. PART I – MSTL Decomposition & ETS Forecasting

* Decomposes `Operation` using:
  ```
  MSTL with periods = (8, 24, 168)
  ```
* Extracted components:
  * Trend
  * Seasonal_8
  * Seasonal_24
  * Seasonal_168
  * Residual
* Each component is forecasted individually using:
  * `ExponentialSmoothing`
* Final forecast is obtained by:
  ```
  summed_up_array = Trend + Seasonal_8 + Seasonal_24 + Seasonal_168
  ```
* Saves:
  * `reference.xlsx` → Ground truth
  * `summed_up.xlsx` → Decomposition-based forecast

---

### 3. PART II – Feature Extraction

#### 3.1 Statistical Features (TSFRESH)

* Applied on:
  ```
  MSTL residual of Operation
  ```
* Sliding windows:
  ```
  window_length = 24
  stride = 1
  ```
* Automated feature extraction using `tsfresh`
* Standardization & cleaning
* Output:
  ```
  statistical_data.xlsx
  ```
---

#### 3.2 Topological Features (TDA – Giotto-TDA)

**Phase Space Reconstruction**

* Sliding Window + Takens Embedding:

  ```
  window_size = 24
  time_delay = 8
  embedding_dimension = 3
  stride = 1
  ```

**Persistent Homology**

* Vietoris–Rips persistence
* Computed for:

  * H0
  * H1

**Extracted Feature Groups**

* Persistence Entropy

* Amplitudes:

  * Bottleneck
  * Wasserstein
  * Landscape

* Betti Curves

* Persistence Landscapes

* Silhouettes

* Heat Kernel Features:

  ```
  sigma = 0.1
  n_bins = 100
  ```

* Carlsson-type summary statistics (f1–f5)

* All features are concatenated, scaled and saved as:

  ```
  topological_data.xlsx
  ```

---

### 4. PART III – Forecasting with SARIMAX

All forecasts use:

* Test size: **4 steps ahead**
* Evaluation metrics:

  * MAE
  * MAPE
* All results appended to:

  ```
  main_results
  ```

---

### 4.1 MODEL 1 – Decomposition Only (No Features)

* SARIMAX fitted using only MSTL-summed series
* No exogenous inputs
* Stored as:

  ```
  SARIMA by DECOMPOSITION W NO FEATURE
  ```

---

### 4.2 TDA-Based SARIMAX Models

Exogenous inputs: `topological_data.xlsx`

| Model     | Description                                      |
| --------- | ------------------------------------------------ |
| MODEL 2   | SARIMAX with **all TDA features**                |
| MODEL 3-1 | SARIMAX with **RFE-selected TDA features**       |
| MODEL 4   | SARIMAX with **RFE + PSO-selected TDA features** |
| MODEL 4-1 | **Best optimized TDA feature subset**            |

---

### 4.3 Statistical Feature-Based SARIMAX Models

Exogenous inputs: `statistical_data.xlsx`

Same structure as the TDA models:

| Model     | Description                       |
| --------- | --------------------------------- |
| MODEL 2   | All statistical features          |
| MODEL 3-1 | RFE-selected statistical features |
| MODEL 4   | RFE + PSO statistical features    |
| MODEL 4-1 | Best optimized statistical subset |

---

## Outputs & Saved Artifacts

* **Excel Files**

  * `reference.xlsx`
  * `summed_up.xlsx`
  * `statistical_data.xlsx`
  * `topological_data.xlsx`

* **Figures**

  * GM2 distribution plots
  * MSTL decomposition plots
  * Forecast vs Actual plots for each model

* **Final Model Comparison**

  * Stored in `main_results`
  * Includes MAE, MAPE, and runtime for all models

---

## Summary

This notebook provides a **fully automated experimental framework** for evaluating how:

* Classical decomposition,
* Statistical features,
* Topological features,
* And intelligent feature selection (RFE + PSO),

affect **SARIMAX forecasting performance** on the GM2 manufacturing time series.

It enables:

* Direct comparison of traditional vs TDA-enhanced forecasting
* Controlled ablation of feature groups
* Performance benchmarking under identical test conditions

---
