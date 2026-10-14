# Hanoi PM2.5 Forecasting — Final Project

**Fundamentals of Data Science** · Data Collection → Data Cleaning & Feature Engineering → Exploratory Data Analysis → Modeling & Evaluation

This repository documents a **6-hour-ahead PM2.5 forecasting** workflow for Hanoi using hourly air-quality and meteorological data accessed through **Open-Meteo**. It contains the final-project notebooks, including baseline forecasting, temporal model selection, feature-group comparisons, sequence experiments, and out-of-time evaluation.

## Data and study scope

- **Study period:** 1 September 2022–31 August 2026 (inclusive), hourly resolution.
- **Location:** Hanoi; requested coordinate **21.0052° N, 105.8418° E**.
- **Air-quality source:** [Open-Meteo Air Quality API](https://open-meteo.com/en/docs/air-quality-api), configured with the `cams_global` domain for PM2.5, PM10, and nitrogen dioxide. These are **gridded air-quality estimates**, not direct measurements from a local monitoring station.
- **Meteorological source:** [Open-Meteo Historical Weather API](https://open-meteo.com/en/docs/historical-weather-api), including temperature, relative humidity, precipitation, surface pressure, wind speed, and wind direction.
- **Time handling:** hourly timestamps in `Asia/Ho_Chi_Minh`, with UTC timestamps retained for alignment and checks. Notebook 1 aligns both sources to a complete local hourly timeline.

## Notebook workflow

| Notebook | Purpose |
| --- | --- |
| [`01_data_collection.ipynb`](notebooks/01_data_collection.ipynb) | Retrieve air-quality and historical weather series, inspect coverage and hourly continuity, align the sources, and save an integrated hourly dataset. |
| [`02_data_cleaning.ipynb`](notebooks/02_data_cleaning.ipynb) | Perform structural, gap, and validity checks; generate the forecast target, lagged pollutant history, cyclic time/direction variables, and model-ready feature sets. |
| [`03_eda.ipynb`](notebooks/03_eda.ipynb) | Analyze distributions, temporal/seasonal patterns, and predictor relationships with the 6-hour-ahead target. |
| [`04_modeling.ipynb`](notebooks/04_modeling.ipynb) | Compare forecasting baselines and tabular models, tune hyperparameters using chronological folds, test feature groups, evaluate sequence models, and analyze final test errors. |

## Modeling and evaluation

Notebook 4 uses an **expanding-window** development strategy with validation periods starting in September 2023 and September 2024. The **final held-out evaluation** begins on **1 September 2025**. The forecast target is `pm25_t_plus_6`.

The experiments include naive baselines; linear and regularized regression; Random Forest, Extra Trees, and XGBoost; feature-group ablation; LSTM and flattened-history XGBoost comparisons; and evaluation using **MAE, RMSE, and R²**. Additional diagnostics include error patterns by month and pollution level, permutation importance, and block-bootstrap model comparisons. The selected model and measured performance are determined by the executed notebook rather than asserted in this README.

## How to run

1. Use **Python 3.10 or later** and install the dependencies from the repository root:

   ```bash
   python -m pip install -r requirements.txt
   ```

2. Start Jupyter **from inside `notebooks/`**, so relative paths such as `../data/interim/` resolve correctly:

   ```bash
   cd notebooks
   jupyter lab
   ```

3. Run notebooks in order: **01 → 02 → 03 → 04**. Notebook 1 needs internet access to Open-Meteo. It writes intermediate data to `data/`; Notebook 2 creates the processed `.pkl` datasets required by Notebooks 3 and 4. Notebook 4 creates `outputs/openmeteo_4y_final_methodology/` automatically and writes its experiment artifacts there.

**Note on dependencies:** XGBoost and TensorFlow/Keras are imported conditionally by Notebook 4. They are listed in `requirements.txt` to enable the full experiment set. TensorFlow installation and hardware support can differ by operating system and Python version.

**Reproducibility:** This public repository intentionally excludes collected datasets and generated experiment files. Some executed notebook outputs may be retained for inspection. API source products can be revised, and model-training results may vary across environments; rerunning the entire pipeline is therefore not guaranteed to reproduce every saved number exactly.

## Repository layout

```text
hanoi-pm25-forecasting-final/
├── README.md
├── requirements.txt
├── .gitignore
└── notebooks/
    ├── 01_data_collection.ipynb
    ├── 02_data_cleaning.ipynb
    ├── 03_eda.ipynb
    └── 04_modeling.ipynb
```

`data/` and `outputs/` are generated locally and excluded from version control.

## Data attribution

Air-quality and weather data are accessed via [Open-Meteo](https://open-meteo.com/). The air-quality API uses CAMS-derived products; acknowledge the underlying data providers and consult the source-specific attribution and usage requirements before redistributing data. See the [Open-Meteo terms](https://open-meteo.com/en/terms), [air-quality API documentation](https://open-meteo.com/en/docs/air-quality-api), and [historical weather documentation](https://open-meteo.com/en/docs/historical-weather-api).
