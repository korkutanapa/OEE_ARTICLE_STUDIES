# TS-TDA-PREDICTION

This repository contains a 4-step pipeline for time series forecasting using **Topological Data Analysis (TDA)** and **SARIMAX**.  
Each step is implemented as a Jupyter notebook.

---

## 1. Decomposition Step  
**Notebook:** `TS_TDA_PREDICTION_DECOMPOSITION_STEP.ipynb`

**Purpose:**  
Takes the original time series as input and decomposes it into components (e.g. trend, seasonal, residual).

**Main actions:**
- Reads the raw time series.
- Performs time series decomposition.
- Saves the residual component into an Excel file (e.g. `tda_ready.xlsx`) that will be used in the next step.

**Typical outputs:**
- Plots of original vs. decomposed components.
- A file containing the residuals for TDA (e.g. `tda_ready.xlsx`).

---

## 2. TDA Feature Extraction Step  
**Notebook:** `TS_TDA_PREDICTION_TDA_STEP.ipynb`

**Purpose:**  
Extracts topological features from the **residual** component of the time series.

**Main actions:**
- Loads `tda_ready.xlsx` (residual component from Step 1).
- Applies TDA (e.g. sliding windows, persistence, etc.) to the residual series.
- Computes a rich set of TDA-based features.
- Includes many **Heat Kernel** features and computes a **PCA** representation of them.

**Typical outputs:**
- `tda_features_from_residuals_selected_scaled.xlsx`  
  (main TDA feature set, scaled and ready for feature selection).
- An additional file with PCA results of Heat Kernel features (e.g. PCA scores of HK features).

---

## 3. Feature Selection Step  
**Notebook:** `TS_TDA_FEATURE_SELECTION_STEP.ipynb`

**Purpose:**  
Selects the most informative features using a combination of **RFE** and **PSO**.

**Main actions:**
- Loads the TDA feature dataset from Step 2.
- Applies **Recursive Feature Elimination (RFE)** based on statistical significance (e.g. p-values).
- Applies **Particle Swarm Optimization (PSO)** guided by an information criterion (e.g. BIC).
- Combines these methods to obtain a final subset of selected features.

**Typical outputs:**
- An Excel/CSV file containing only the selected features (ready for SARIMAX).

---

## 4. SARIMAX Prediction Step  
**Notebook:** `TS_TDA_PREDICTION_SARIMAX_CODES.ipynb`

**Purpose:**  
Builds and evaluates **SARIMAX** models using the selected TDA features as exogenous variables.

**Main actions:**
- Loads the selected feature set from Step 3.
- Fits SARIMAX models to the original time series with exogenous TDA features.
- Calculates forecasting performance metrics (e.g. MAE, RMSE, MAPE, SMAPE, MASE).
- Produces plots and tables summarizing the forecasting results.

**Typical outputs:**
- Forecast vs. actual plots.
- A table of regression / forecasting metrics.
- Optional export of results to Excel/CSV.

---

## Recommended Running Order

1. Run `TS_TDA_PREDICTION_DECOMPOSITION_STEP.ipynb`  
2. Run `TS_TDA_PREDICTION_TDA_STEP.ipynb`  
3. Run `TS_TDA_FEATURE_SELECTION_STEP.ipynb`  
4. Run `TS_TDA_PREDICTION_SARIMAX_CODES.ipynb`

Each step uses the output files from the previous step, so keep the filenames and paths consistent.

---

## Requirements (general)

- Python 3.x  
- Jupyter Notebook / JupyterLab  
- Typical packages (examples):
  - `numpy`, `pandas`
  - `matplotlib`, `seaborn` (optional)
  - `statsmodels` (for SARIMAX)
  - TDA-related libraries (e.g. `giotto-tda`, `ripser`, or others used in the notebooks)
  - `scikit-learn` (for RFE, scaling, etc.)
  - PSO library (or custom PSO implementation used in the notebook)

Please check each notebook’s first cell for the exact list of imports and install any missing packages before running.

