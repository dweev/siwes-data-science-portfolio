# SIWES Data Science Portfolio (DTS 299)

**Author:** Jeremy Oche Okoh (`U24DS1007`)  
**Programme:** B.Sc. Data Science — Philomath University, Maitama – Abuja  
**Attachment:** SBTS Group Nigeria (Development Department), Utako, Abuja  
**Duration:** 11th June, 2026 – 4th September, 2026 (12 Weeks)

---

## Overview

This repository compiles the three applied Data Science projects completed during my 12-week Students' Industrial Work Experience Scheme (SIWES / DTS 299). The workflow progresses from multi-file tabular data cleaning and exploratory analysis to regularized regression modelling, NoSQL database querying, and time-series forecasting.

---

## Repository Structure

```text
siwes-data-science-portfolio/
├── README.md
├── .gitignore
├── requirements.txt
├── 01-housing-in-mexico/
│   ├── data/
│   │   ├── mexico-real-estate-1.csv
│   │   ├── mexico-real-estate-2.csv
│   │   ├── mexico-real-estate-3.csv
│   │   └── mexico-real-estate-clean.csv
│   └── housing-in-mexico.ipynb
├── 02-housing-in-buenos-aires/
│   ├── data/
│   │   ├── buenos-aires-real-estate-1.csv ... 5.csv
│   │   └── buenos-aires-test-features.csv
│   └── housing-in-buenos-aires.ipynb
└── 03-air-quality-in-nairobi/
    └── air-quality-in-nairobi.ipynb
```

---

## Projects Summary

### 1. Housing in Mexico (`01-housing-in-mexico/`)
- **Objective:** Clean and harmonize three heterogeneous real-estate CSV files and evaluate how property size and state location influence housing prices.
- **Data Wrangling:** Cleaned 2,100 raw listings into **1,736 tidy records** by handling missing values, stripping string currency symbols, converting MXN to USD (`÷ 19`), splitting packed `lat-lon` strings, and parsing state names from pipe-delimited text.
- **Key Findings:**
  - Mean property area: `170.26 m²` (median `156.00 m²`); mean price: `$115,331.98`.
  - **Distrito Federal** commands the highest unit price at nearly **`$1,180/m²`**, compared to `$720–$765/m²` in second-tier states and `<$420/m²` in Durango and Colima.
  - National Pearson correlation between `area_m2` and `price_usd` is **`r = 0.5855`**, with widening dispersion above `150 m²`.

### 2. Housing in Buenos Aires (`02-housing-in-buenos-aires/`)
- **Objective:** Build a reusable ingestion and Ridge regression pipeline to predict apartment sale prices in Capital Federal, Buenos Aires.
- **Feature Engineering & Hygiene:** Automated ingestion of five CSV files via `glob` and a modular `wrangle()` function—filtering Capital Federal apartments `< $400,000`, trimming 10th–90th percentile covered area outliers, dropping `>50%` null columns, removing target-leaking price columns, and eliminating collinear size features—yielding **6,582 clean listings across 57 neighbourhoods**.
- **Model & Performance:**

| Model | Features Used | Mean Absolute Error (USD) | Improvement |
| :--- | :--- | :---: | :---: |
| Naive Mean Baseline | Constant training mean (`$132,383.84`) | `$44,860.11` | Benchmark |
| Ridge Regression Pipeline | `surface_covered_in_m2`, `lat`, `lon`, `neighborhood` (57 OHE levels) | **`$24,207.11`** | **46.04% reduction (`-$20,653.00`)** |

- **Interactive Tool:** Wrapped the fitted `OneHotEncoder → SimpleImputer → Ridge()` pipeline inside `make_prediction()` with `ipywidgets` controls (e.g., `110 m²` in Villa Crespo → `$250,775.11`).

### 3. Air Quality in Nairobi (`03-air-quality-in-nairobi/`)
- **Objective:** Query a document-oriented NoSQL MongoDB sensor database and forecast hourly PM2.5 (`P2`) concentrations.
- **NoSQL & Time-Series Wrangling:** Connected via `PyMongo`, audited **202,212 documents** across Site 6 (`70,360`) and Site 29 (`131,852`) using `$match`/`$group` aggregation pipelines, extracted **32,907 Site 29 PM2.5 readings** with server-side projection, localized timestamps (`UTC` → `Africa/Nairobi`), capped spikes (`< 500 µg/m³`), resampled to 1-hour means, and forward-filled short gaps.
- **Forecasting Results:**

| Model Specification | Split / Test Horizon | Baseline MAE | Train MAE | Test MAE |
| :--- | :--- | :---: | :---: | :---: |
| Lag-1 Linear Regression (`P2 = 3.36 + 0.64·P2.L1`) | 80/20 Chronological | 3.89 | 2.46 | **1.80** |
| Static Multi-Step AR(26) | 95/5 Chronological | 3.71 | 2.28 | **3.01** |
| Walk-Forward Validation AR(26) | 95/5 Expanding WFV | 3.71 | 2.28 | **1.40** |
| Walk-Forward Validation ARMA(8, 0, 1) | Oct 2018 / Nov 1 (24h) | 4.17 | 2.91 | **1.67** |

---

## Setup & Usage

```bash
# Clone the repository
git clone https://github.com/dweev/siwes-data-science-portfolio.git
cd siwes-data-science-portfolio

# Install dependencies
pip install -r requirements.txt

# Launch Jupyter Notebook
jupyter notebook
```

> **Note on Project 3:** `air-quality-in-nairobi.ipynb` preserves all executed outputs and visualizations. Re-running cells live requires access to a MongoDB instance hosting the `air-quality` database (`nairobi` collection).
