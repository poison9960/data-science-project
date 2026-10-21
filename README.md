# Macau Gross Gaming Revenue vs. Inbound Tourist Arrivals — A Regression Analysis

## Problem / Research Question
Macau's gaming industry is heavily driven by inbound tourism. This project asks:

> **To what extent can monthly inbound tourist arrivals — both overall and broken down by source market (Mainland China, Hong Kong, Other) — explain and predict Macau's monthly Gross Gaming Revenue (GGR, in million MOP)?**

We compare three ordinary least-squares regression models to see which tourist-related features best predict GGR.

## Dataset Source & Usage Permissions
- **`income.xlsx`** contains 60 months of official Macau monthly Gross Gaming Revenue records (year, month, GGR in million MOP, year-on-year change rate), adapted from public statistics used in the CISC7204 course at the University of Macau.
- **Tourist-arrival columns** (total tourists and the three source-market splits) are **synthetically simulated** inside the notebook with a fixed random seed (`np.random.seed(42)`), so that the whole pipeline is reproducible end-to-end without leaking any restricted third-party data. They are intended only to demonstrate the modelling workflow.
- The dataset is used for **academic / coursework demonstration only**. No personal data, passwords, API keys, or restricted datasets are included.

## Methods & Tools
- **Language / environment:** Python 3.10+, Jupyter Notebook
- **Stack:** `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`
- **Pipeline:**
  1. **Data import** — read the Excel file; construct tourist-arrival features.
  2. **Data cleaning** — map Chinese month names to numeric, build a monthly datetime index, inspect missing values and outliers, derive source-market share ratios.
  3. **Exploratory Data Analysis** — dual-axis trend line, seasonality by calendar month, source-market pie chart, correlation heatmap.
  4. **Model development** — three `LinearRegression` models:
     - *Model 1*: GGR ~ total tourists (simple linear).
     - *Model 2*: GGR ~ Mainland + Hong Kong + Other tourists (multiple linear).
     - *Model 3*: GGR ~ current-month tourists + previous-month (lagged) tourists.
  5. **Model evaluation** — train/test split 80/20 (no shuffle, time-ordered), compare R², MSE, RMSE; diagnostic plots (predicted-vs-actual, residual histogram, residuals-vs-fitted).

## Key Findings
- A 10,000-person increase in total monthly tourists is associated with about **+1.33 million MOP** of additional GGR (Model 1 coefficient).
- Among source markets, **Mainland Chinese tourists have the largest coefficient (1.39)**, ahead of Other regions (1.29) and Hong Kong (1.17) — i.e. Mainland visitors contribute the strongest marginal lift to gaming revenue in this sample.
- Adding the **previous-month tourist volume as a lagged feature barely improves the fit** (lag coefficient ≈ 0.02), suggesting the relationship is contemporaneous rather than carry-over in this synthetic sample.
- Visualisations: see [`visuals/eda_correlation_trends.png`](visuals/eda_correlation_trends.png) and [`visuals/model3_predicted_vs_actual.png`](visuals/model3_predicted_vs_actual.png).

### Limitation
The tourist-arrival features are **simulated, not real government statistics**. The strong correlations and R² values therefore partly reflect the generative assumptions baked into the simulation rather than true economic structure. In a production version we would replace them with the actual DSEC / MGTO monthly tourist series and add time-series baselines (ARIMA, seasonal-naive) for a fairer comparison.

## How to Reproduce
```bash
# 1. (Recommended) create a virtual environment
python -m venv .venv
# Windows:  .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate

# 2. Install dependencies
pip install -r requirements.txt

# 3. Launch the notebook
jupyter notebook macau_gaming_revenue_analysis.ipynb
```
Then *Run All* from the top. The notebook is fully self-contained: it reads `income.xlsx` from the same folder, simulates the tourist columns, and writes all plots to screen. No API keys or external data calls are needed.

## Project Status
**Completed** (coursework preliminary-project version). Possible next steps: plug in real DSEC tourist data, add seasonal-naive / ARIMA baselines, deploy an interactive Plotly Dash explorer.
