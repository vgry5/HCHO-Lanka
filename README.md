# 🌍 HCHO-Lanka: Spatial-Temporal Analysis & Forecasting of Tropospheric Formaldehyde in Sri Lanka

## 📋 Table of Contents

- [Overview](#overview)
- [Motivation](#motivation)
- [Study Regions](#study-regions)
- [Data Sources & Features](#data-sources--features)
- [Project Pipeline](#project-pipeline)
- [Key Techniques](#key-techniques)
- [Repository Structure](#repository-structure)
- [Results](#results)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [License](#license)
- [Author](#author)

---

## 🔬 Overview

This project conducts a comprehensive spatial-temporal analysis of tropospheric Formaldehyde (HCHO) gas concentrations across **seven key locations in Sri Lanka** over a **5-year period (2019–2023)**. Tropospheric HCHO is a critical indicator of air quality and a precursor to ground-level ozone, making its monitoring essential for environmental and public health studies.

The project encompasses end-to-end data engineering — from raw satellite data ingestion and cleaning through exploratory data analysis (EDA) and statistical modelling, to building **ARIMA/SARIMAX time series forecasting models** that predict future HCHO concentrations for each study region.

---

## 💡 Motivation

Formaldehyde (HCHO) in the troposphere is a volatile organic compound (VOC) that plays a significant role in atmospheric chemistry. It serves as:

- A **tracer for air pollution** from both natural (biogenic) and anthropogenic sources
- A **precursor to ground-level ozone** and other secondary pollutants
- An **indicator of biomass burning**, vehicular emissions, and industrial activity

Despite its importance, comprehensive HCHO monitoring and analysis in Sri Lanka has been limited. This project fills that gap by:

1. Understanding the importance of HCHO monitoring for air quality and climate studies
2. Developing insights into potential HCHO sources and spatial/temporal trends across diverse regions
3. Developing time series prediction algorithms to forecast future HCHO levels

---

## 📍 Study Regions

The analysis covers **seven geographically diverse locations** across Sri Lanka, selected to represent varying altitudes, population densities, and land-use patterns:

| Location | Population Density (persons/km²) | Altitude (ft) |
|:---|:---:|:---:|
| Colombo | 13,364 | 1 |
| Matara | 630 | 2 |
| Nuwara Eliya | 420 | 1,868 |
| Kandy | 710 | 500 |
| Monaragala | 80 | 151 |
| Kurunegala | 362 | 116 |
| Jaffna | 611 | 5 |

---

## 📊 Data Sources & Features

### Primary Data
- **HCHO Column Amounts** — Daily tropospheric HCHO measurements (in mol/cm²) sourced from satellite remote sensing data, covering 2019–2023

### Supplementary Features (Spatial-Temporal Analysis)
The dataset is enriched with the following contextual variables per location and time period:

| Feature | Description |
|:---|:---|
| `HCHO_Amount` | Daily tropospheric HCHO concentration |
| `Location` | Study region identifier |
| `Present_Date` / `Next_Date` | Temporal span of each observation |
| `Month` | Calendar month |
| `Year` | Calendar year |
| `Average Temperature (°C)` | Regional average temperature |
| `Unemployment Rate` | Regional unemployment rate (proxy for economic activity) |
| `Tree Cover Loss (ha)` | Annual deforestation metric (hectares) |
| `Population Density` | Persons per km² |
| `Altitude (ft)` | Elevation above sea level |

---

## ⚙️ Project Pipeline

```
┌──────────────────┐     ┌────────────────────┐     ┌───────────────────┐
│  Raw Satellite    │ ──▶ │ Data Preprocessing │ ──▶ │   Exploratory     │
│  HCHO Data        │     │ & Cleaning         │     │   Data Analysis   │
└──────────────────┘     └────────────────────┘     └───────────────────┘
                                                             │
                                                             ▼
┌──────────────────┐     ┌────────────────────┐     ┌───────────────────┐
│  HCHO Forecasts  │ ◀── │  ARIMA / SARIMAX   │ ◀── │ Feature           │
│  (Jan 2024)      │     │  Time Series Model │     │ Engineering       │
└──────────────────┘     └────────────────────┘     └───────────────────┘
```

### 1. Data Preprocessing (`solution.ipynb`)
- **Ingestion**: Read raw HCHO data from CSV files for multiple location groups
- **Standardization**: Rename locations to consistent identifiers (e.g., "Colombo Proper" → "Colombo")
- **Type Conversion**: Parse date columns into proper datetime formats
- **Negative Value Handling**: Replace negative HCHO readings with NaN (physically invalid measurements)
- **Missing Value Imputation**: Fill NaN values with column means
- **Outlier Removal**: Detect and remove outliers using the 3σ (three-sigma) rule
- **Statistical Analysis**: Compute mean, median, mode, and standard deviation for each location
- **Visualization**: Generate distribution plots and box plots for each study region
- **Export**: Produce cleaned per-location CSVs for downstream modelling

### 2. Spatial-Temporal Feature Engineering (`spatial_temp_ds_cleaned.ipynb`)
- Merge HCHO time series data with supplementary socioeconomic and environmental features
- Produce enriched datasets for multi-variate analysis (7,273+ observations)

### 3. Time Series Modelling (`model.ipynb`)
- **Auto ARIMA** (via `pmdarima`) performs stepwise search to find optimal (p, d, q) parameters minimizing AIC
- **SARIMAX** models are fitted independently for each of the 7 locations using an **80/20 train-test split**
- Forecasts are generated for **January 2024** (30-day horizon)
- Model diagnostics include AIC/BIC scores, Ljung-Box tests, and visual residual analysis

### 4. Prediction Aggregation (`prediction_combined.ipynb`)
- Combine per-location HCHO forecasts into a unified dataset
- Export combined predictions to `pred_combined_cleaned.csv`

---

## 🔑 Key Techniques

| Technique | Purpose |
|:---|:---|
| **3σ Outlier Detection** | Identify and remove anomalous HCHO readings |
| **Mean Imputation** | Handle missing satellite measurements |
| **Auto ARIMA (Stepwise AIC Minimization)** | Automatically select best ARIMA(p,d,q) parameters |
| **SARIMAX** | Fit location-specific time series models |
| **Box Plots & KDE Distribution Plots** | Visual EDA for each study region |
| **Statistical Profiling** | Mean, median, mode, standard deviation per location |

---

## 📁 Repository Structure

```
HCHO-Lanka/
│
├── solution.ipynb                    # Data preprocessing & EDA pipeline
├── model.ipynb                       # ARIMA/SARIMAX modelling for all 7 locations
├── spatial_temp_ds_cleaned.ipynb     # Spatial-temporal feature engineering
├── prediction_combined.ipynb         # Combine per-location forecasts
│
├── Spatial_Temporal_Analysis/
│   └── Altitude_Population.csv       # Altitude & population density by location
│
├── Final_HCHO_Pred/                  # Forecast outputs (Jan 2024)
│   ├── Colombo_HCHO_Pred.csv
│   ├── Jaffna_HCHO_Pred.csv
│   ├── Kandy_HCHO_Pred.csv
│   ├── Kurunegala_HCHO_Pred.csv
│   ├── Matara_HCHO_Pred.csv
│   ├── Monaragala_HCHO_Pred.csv
│   └── Nuwara_E_HCHO_Pred.csv
│
├── col_mat_nuw_output.csv            # Raw data: Colombo, Matara, Nuwara Eliya
├── mon_kur_jaf_output.csv            # Raw data: Monaragala, Kurunegala, Jaffna
├── kan_output.csv                    # Raw data: Kandy
│
├── colo_cleaned.csv                  # Cleaned: Colombo
├── mata_cleaned.csv                  # Cleaned: Matara
├── nuwara_eliya_df.csv               # Cleaned: Nuwara Eliya
├── kandy_cleaned.csv                 # Cleaned: Kandy
├── jaffna_cleaned.csv                # Cleaned: Jaffna
├── kurunegala_cleaned.csv            # Cleaned: Kurunegala
├── monaragala_cleaned.csv            # Cleaned: Monaragala
│
├── combined_cleaned.csv              # All locations combined (cleaned)
├── merged_cleaned.csv                # Merged with temporal features
├── f_merged_cleaned.csv              # Final merged with all features
├── final_merged_cleaned_spatial.csv  # Spatial-temporal analysis dataset
├── pred_combined_cleaned.csv         # Combined HCHO predictions
│
├── *_model.csv                       # Per-location model input files
│
├── LICENSE                           # MIT License
└── README.md                         # This file
```

---

## 📈 Results

- **Individual ARIMA models** were successfully fitted for all 7 study locations
- The auto-ARIMA stepwise search identified optimal parameters per location (e.g., ARIMA(0,1,4) for Colombo with AIC = -16,482)
- **30-day HCHO forecasts** (January 2024) were generated for each region
- The enriched spatial-temporal dataset (7,273 observations × 9 features) enables multi-variate analysis of HCHO drivers

---

## 🛠 Tech Stack

| Tool | Purpose |
|:---|:---|
| **Python 3.10** | Core programming language |
| **pandas** | Data manipulation and cleaning |
| **NumPy** | Numerical computations |
| **Matplotlib / Seaborn** | Data visualization |
| **statsmodels (SARIMAX)** | Time series modelling |
| **pmdarima (auto_arima)** | Automated ARIMA parameter selection |
| **Jupyter Notebook** | Interactive analysis environment |

---

## 🚀 Getting Started

### Prerequisites
```bash
pip install pandas numpy matplotlib seaborn statsmodels pmdarima
```

### Run the Pipeline

1. **Preprocess the data**:
   Open and run `solution.ipynb` to clean raw HCHO data and generate per-location CSVs.

2. **Feature engineering**:
   Run `spatial_temp_ds_cleaned.ipynb` to merge HCHO data with supplementary features.

3. **Build forecasting models**:
   Run `model.ipynb` to fit ARIMA/SARIMAX models and generate predictions.

4. **Combine predictions**:
   Run `prediction_combined.ipynb` to aggregate forecasts across all locations.

---

## 📜 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

**Yenuka Rajapaksha**

---

## 🙏 Acknowledgements

- Satellite HCHO data sourced from remote sensing platforms
- Sri Lanka Meteorological Department for temperature data
- Global Forest Watch for tree cover loss data
