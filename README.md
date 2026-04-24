# predictive_paradox
**Core Objective:** High-precision hourly demand forecasting ($t+1$) via the PGCB dataset by synthesizing human activity, environmental, and historical vectors.

## 1. Data Integration & Cleaning
The pipeline consolidates three primary streams:
* **Operational (Grid):** Demand, generation, and fuel-mix variables.
* **Environmental (Weather):** Temperature, humidity, and cloud cover metrics.
* **Macro (Economic):** GDP, population, and inflation (CPI) trends.

**Pre-processing Protocol:**
* **Continuity:** Hourly reindexing with forward-filling ($<2h$) and linear interpolation for weather ($<6h$).
* **Outlier Mitigation:** Rolling IQR filter (72-hour window) targeting spikes outside $[Q1 - 4k, Q3 + 4k]$ where $k=4.0$.

## 2. Feature Engineering
Performance is optimized through a multi-dimensional feature space:
* **Temporal:** Cyclical Sine/Cosine encoding of time-cycles and binary markers for weekends/Fridays.
* **Historical Lags:** Targeted demand extraction at $t-\{1, 2, 3, 6, 12, 24, 48, 168, 336\}$ hours.
* **Rolling Aggregates:** Mean, Std, and Min/Max windows across multi-hour intervals.

## 3. Modeling & Evaluation
The system utilizes a strict **chronological split** (Train: Pre-2023 | Test: 2023) to prevent data leakage.

| Algorithm | Primary Tuning | Purpose |
| :--- | :--- | :--- |
| **LightGBM** | 1000 estimators, 63 leaves, 0.04 LR | Speed & feature capacity |
| **XGBoost** | 800 estimators, depth 7, 0.05 LR | Benchmarking |

**Key Performance Indicators (KPIs):**
Forecasts are strictly validated via **MAPE**, **MAE**, and **RMSE**, with automated generation of feature importance and hourly error distributions to identify specific peak-hour volatility.
