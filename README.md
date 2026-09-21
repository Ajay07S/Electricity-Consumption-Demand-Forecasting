# Electricity Consumption & Demand Forecasting: EDA, Visualization and Executive Story

An end-to-end Data Analytics project (B.Tech Data Analytics academic project) covering data cleaning, exploratory data analysis, statistical hypothesis testing, machine learning, time-series forecasting, SQL analysis, and Power BI dashboarding — applied to a synthetic electricity consumption dataset.

> **Note:** The dataset used in this project is **synthetic**, generated for academic/practice purposes. It is **not** official EIA (U.S. Energy Information Administration) data.

---

## Overview

This project analyzes electricity consumption, generation, peak demand, pricing, and renewable share data to understand peak demand behavior and build a foundation for short-term demand forecasting and executive-level monitoring.

## Business Problem

Electricity utilities must balance supply and demand while managing peak load events, renewable integration, and price volatility. This project explores how historical consumption, generation, demand, price, and renewable share data can be analyzed to understand peak demand drivers and support capacity planning.

## Objectives

- Audit and clean the raw electricity dataset
- Explore consumption, demand, generation, price, and renewable patterns
- Statistically test peak vs non-peak consumption differences
- Build a machine learning model to estimate Peak Demand
- Forecast daily average peak demand using time-series methods
- Perform SQL-based KPI analysis
- Build an executive Power BI dashboard
- Document findings, assumptions, and limitations transparently

## Dataset

| Attribute | Value |
|---|---|
| Type | Synthetic (not official EIA data) |
| Original size | 5,045 rows × 11 columns |
| Final cleaned size | 4,991 rows × 11 columns |

**Columns:** `Record_ID`, `Date_Time`, `State`, `Sector`, `Consumption_MWh`, `Peak_Demand_MW`, `Generation_MWh`, `Electricity_Price_USD_per_MWh`, `Renewable_Share_Pct`, `Weather_Index`, `Peak_Flag`

## Technologies

- **Python** (pandas, numpy, matplotlib/seaborn, scikit-learn, statsmodels)
- **SQL** (MySQL)
- **Power BI**
- **Jupyter Notebooks**

## Workflow

1. Data Cleaning & Preprocessing
2. Exploratory Data Analysis (EDA)
3. Statistical Analysis (hypothesis testing)
4. Machine Learning (Random Forest regression)
5. Time-Series Forecasting (ARIMA)
6. SQL Analysis
7. Power BI Dashboarding

## Data Cleaning

Key steps: missing value audit, duplicate removal (25 rows), state/sector standardization, date conversion (8 invalid dates handled), negative value validation for consumption/generation/price (8 each), renewable share validation (5 invalid), and IQR-based outlier analysis (not auto-removed). Negative electricity prices were **retained** as they are a real phenomenon in electricity markets.

## Exploratory Data Analysis

Includes dataset shape/types, descriptive statistics, state/sector/peak-flag distributions, monthly/daily/hourly/day-of-week analysis, correlation analysis, histograms, scatter plots, top-N records, generation/consumption ratio, 7-day moving average, and seasonal decomposition.

**Correlation with Peak_Demand_MW:**

| Variable | Correlation |
|---|---|
| Consumption_MWh | 0.650441 |
| Generation_MWh | 0.644786 |
| Electricity_Price_USD_per_MWh | 0.188429 |
| Weather_Index | 0.005092 |
| Record_ID | -0.005924 |
| Renewable_Share_Pct | -0.011491 |

## Statistical Analysis

**Research question:** Do Peak Flag = Yes records have significantly higher electricity consumption than Peak Flag = No records?

- **Test:** Welch's independent two-sample t-test
- **Non-Peak mean:** 907.41 MWh | **Peak mean:** 1,392.15 MWh
- **t-statistic:** 63.68 | **p-value:** p < 0.001
- **95% CI of difference:** 469.80 to 499.66 MWh
- **Conclusion:** Reject H0 — peak periods show significantly higher consumption.

> Peak_Flag may be derived from Peak_Demand_MW, so this result should be interpreted carefully. Correlation does not imply causation.

## Machine Learning

- **Target:** `Peak_Demand_MW`
- **Features:** `Consumption_MWh`, `Generation_MWh`, `Electricity_Price_USD_per_MWh`, `Renewable_Share_Pct`, `Weather_Index` (`Peak_Flag` excluded — leakage risk)
- **Split:** Chronological 80/20 (shuffle=False)
- **Baseline:** DummyRegressor (mean strategy)
- **Model:** RandomForestRegressor (`n_estimators=100`, `random_state=42`)
- **Metrics:** MAE, RMSE, R² — *[TO BE ADDED from notebook output]*

## Time-Series Forecasting

- **Series:** Daily average Peak Demand
- **Model:** ARIMA(1,1,1)
- **Validation:** Chronological train/test split
- **Metrics:** MAE, RMSE vs baseline — *[TO BE ADDED from notebook output]*

## SQL Analysis

Database: `electricity_project` | Table: `energy_consumption` (4,991 records imported)

Queries: overall KPIs, state/sector-wise averages, peak vs non-peak comparison, monthly trends, top-N records, and price/renewable share by state. See [`sql/electricity_analysis.sql`](sql/electricity_analysis.sql).

## Power BI Dashboard

**Page 1 — Executive Energy Dashboard:** KPI cards (Total Consumption, Total Generation, Avg Peak Demand, Avg Renewable Share), slicers (State, Sector, Peak_Flag, Date), and charts covering state/sector comparisons, peak vs non-peak, renewable share, and pricing.

**Page 2 — Energy Analysis Report:** Daily consumption/peak demand trends, average peak demand by state, monthly generation trend, sector share, and a renewable share gauge.

## Key Insights

- Peak periods show substantially and statistically significantly higher consumption than non-peak periods.
- Consumption and generation are moderately positively correlated with peak demand.
- Electricity price shows a weaker positive relationship with peak demand.
- Weather index and renewable share show negligible linear correlation with peak demand in this dataset.

## Business Recommendations

Peak demand monitoring, capacity planning buffers, demand-side management exploration, continued renewable share monitoring, generation planning alignment, dashboard-based operational monitoring, and using the ARIMA forecast as a supplementary planning input. *(Recommendations — not proven outcomes; see full report for caveats.)*

## Limitations

- Synthetic dataset — findings may not generalize to real-world grids.
- Peak_Flag may be derived from Peak_Demand_MW.
- Limited time range/state-sector coverage.
- Correlational findings do not establish causation.
- ML/forecasting metrics pending final notebook execution.

## Repository Structure

```
Electricity-Consumption-Demand-Forecasting/
│
├── README.md
├── data/
│   ├── raw/
│   └── cleaned/
│
├── notebooks/
│   ├── Data_Cleaning.ipynb
│   ├── EDA.ipynb
│   ├── Statistical_Analysis.ipynb
│   ├── ML_Model.ipynb
│   └── Time_Series_Forecasting.ipynb
│
├── sql/
│   └── electricity_analysis.sql
│
├── powerbi/
│   └── Electricity_Dashboard.pbix
│
├── reports/
│   └── Project_Report.pdf
│
├── visuals/
│
└── requirements.txt
```

## How to Run the Project

1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd Electricity-Consumption-Demand-Forecasting
   ```
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Run notebooks in order: `Data_Cleaning.ipynb` → `EDA.ipynb` → `Statistical_Analysis.ipynb` → `ML_Model.ipynb` → `Time_Series_Forecasting.ipynb`
4. Import the cleaned dataset into MySQL and run `sql/electricity_analysis.sql` for KPI queries.
5. Open `powerbi/Electricity_Dashboard.pbix` in Power BI Desktop to explore the dashboard.

## Future Enhancements

- Incorporate real-world electricity market data for validation.
- Explore seasonal ARIMA (SARIMA) or machine-learning-based forecasting models.
- Add causal inference methods to investigate peak demand drivers more rigorously.
- Expand the dashboard with drill-through pages for state-level operational monitoring.
- Automate the SQL and Power BI refresh pipeline for near-real-time monitoring.

---

*This project is for academic purposes only. All data is synthetic and does not represent real utility or government data.*
