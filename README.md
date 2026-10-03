# Demand Prediction & Replenishment Support Dashboard

A professional **Machine Learning–based Demand Prediction and Replenishment Decision-Support Dashboard** designed to help businesses forecast weekly product demand, monitor inventory health, identify stockout risks, and generate replenishment recommendations.

> **Academic Project:** Machine Learning / Data Science
> **Primary Model:** XGBoost Regressor
> **Forecasting Granularity:** Product × Location × Week
> **Dashboard:** HTML, CSS & JavaScript
> **Demo Data:** Synthetic

---

## 📌 Project Overview

Inventory-related decisions are often difficult when businesses have multiple products, locations, changing demand, supplier lead times, and limited stock visibility.

This project demonstrates an end-to-end approach that combines **demand forecasting with inventory decision support**.

The system is designed to:

* Predict future product demand.
* Monitor current inventory levels.
* Identify potential stockout situations.
* Detect products approaching their reorder point.
* Calculate safety-stock requirements.
* Recommend replenishment quantities.
* Present the results through an interactive management dashboard.

The goal is not simply to predict sales, but to transform the prediction into an actionable **inventory replenishment recommendation**.

---

## 🎯 Business Problem

Businesses can experience:

* Stockouts caused by unexpected increases in demand.
* Overstock caused by inaccurate demand estimates.
* Excess inventory holding costs.
* Poor replenishment timing.
* Difficulty managing multiple products and locations.
* Uncertainty caused by supplier lead times.
* Inefficient manual inventory monitoring.

The proposed system addresses these issues through a combination of:

**Demand Forecasting → Inventory Analysis → Replenishment Decision Support**

---

## 🧠 Machine Learning Approach

The project uses a supervised machine-learning approach for weekly demand forecasting.

### Primary Algorithm

**XGBoost Regressor**

XGBoost is used as the primary forecasting model because it can effectively model nonlinear relationships between demand and variables such as:

* Historical demand
* Lagged demand
* Rolling averages
* Seasonality
* Promotions
* Price changes
* Inventory conditions
* Product characteristics
* Location
* Calendar features

### Models Considered

The broader project design considers:

| Model                  | Role                    |
| ---------------------- | ----------------------- |
| Naive / Moving Average | Baseline                |
| Linear Regression      | Interpretable benchmark |
| Random Forest          | Nonlinear benchmark     |
| XGBoost                | Primary model           |
| LightGBM               | Optional comparison     |

---

## 📊 Forecasting Design

The dashboard is designed around **weekly demand forecasting**.

### Data Granularity

```text
Product × Location × Week
```

### Target Variable

```text
NEXT_WEEK_DEMAND_QTY
```

The model uses historical information available before the forecasting point to predict demand for the following week.

---

## 🔄 System Workflow

```text
Business Problem
       ↓
Data Collection
       ↓
Data Cleaning
       ↓
Exploratory Data Analysis
       ↓
Feature Engineering
       ↓
Time-Based Train / Validation / Test Split
       ↓
Baseline Models
       ↓
ML Model Training
       ↓
Model Evaluation
       ↓
Weekly Demand Forecast
       ↓
Inventory Analysis
       ↓
Safety Stock Calculation
       ↓
Reorder Point Calculation
       ↓
Recommended Order Quantity
       ↓
Dashboard
```

---

## 📦 Dataset Structure

The intended dataset contains information from several business areas.

### Product

* Product_ID
* Product_Category
* Product_Name

### Sales / Demand

* Sales_Qty
* Historical_Demand
* Previous-week demand
* Historical demand variability

### Inventory

* Opening_Inventory
* Closing_Inventory
* Current_Inventory
* Incoming_Inventory
* Backorder_Qty
* Stockout_Flag

### Pricing & Promotions

* Selling_Price
* Price_Change
* Promotion_Flag
* Discount

### Supplier

* Supplier_ID
* Lead_Time_Days
* Minimum_Order_Quantity

### Location

* Location_ID
* Location_Name
* Region

### Calendar / Time

* Date
* Week
* Month
* Quarter
* Year
* Week_of_Year
* Holiday_Flag
* Seasonal indicators

---

## ⚙️ Feature Engineering

Important features planned for the ML pipeline include:

### Lag Features

```text
Lag_1
Lag_2
Lag_4
Lag_8
Lag_12
Lag_52
```

These represent historical demand from previous weeks.

### Rolling Features

```text
Rolling_Mean_4
Rolling_Mean_8
Rolling_Mean_12
Rolling_STD_4
Rolling_STD_8
Rolling_STD_12
```

These capture recent demand levels and variability.

### Additional Features

* Demand trend
* Inventory coverage
* Price change
* Promotion effects
* Seasonality
* Month
* Quarter
* Week of year
* Holiday indicators
* Stockout indicators
* Supplier lead time

---

## ⚠️ Data Leakage Prevention

Time-series forecasting requires special attention to data leakage.

The model must only use information that would have been available **at the time the forecast was generated**.

For example, future sales must never be used to construct a feature for an earlier forecast.

Rolling features should therefore be shifted appropriately.

Example:

```python
df["rolling_mean_4"] = (
    df.groupby("Product_ID")["Sales_Qty"]
      .shift(1)
      .rolling(4)
      .mean()
)
```

The project also avoids using a random train/test split as the primary evaluation strategy.

Instead, chronological splitting is used.

---

## 📈 Model Evaluation

The project evaluates forecasting performance using:

### MAE

Mean Absolute Error measures the average absolute forecasting error.

### RMSE

Root Mean Squared Error gives greater weight to larger forecasting errors.

### WAPE

Weighted Absolute Percentage Error provides a business-oriented percentage-based error measure.

### Forecast Bias

Bias helps determine whether the model systematically over-forecasts or under-forecasts.

Example dashboard metrics:

```text
MAE       → 148.6 units
RMSE      → 221.4 units
WAPE      → 8.7%
Bias      → +1.9%
```

> The values shown in the dashboard are synthetic demonstration values.

---

# 🔄 Replenishment Decision Layer

Forecasting alone does not tell an inventory manager how much to order.

Therefore, this project adds a separate replenishment decision layer.

### Reorder Point

A simplified formulation is:

```text
Reorder Point =
Lead-Time Demand + Safety Stock
```

### Safety Stock

A common approximation is:

```text
Safety Stock = z × σD × √L
```

Where:

* `z` = desired service-level factor
* `σD` = demand standard deviation
* `L` = lead time

### Inventory Position

```text
Inventory Position =
Current Inventory
+ Incoming Inventory
− Backorders
```

### Recommended Order

A simplified recommendation is:

```text
Recommended Order =
Target Inventory − Inventory Position
```

Business constraints such as:

* Minimum Order Quantity
* Supplier constraints
* Storage capacity
* Order frequency

can be incorporated into the final implementation.

---

## 📊 Dashboard

The HTML dashboard provides a management-oriented view of the ML system.

### Executive KPIs

The dashboard displays:

* Forecast Demand
* Inventory Position
* Stockout Risk
* Recommended Orders
* Service Level

### Demand Forecast

Displays:

* Historical demand
* Forecast demand
* Weekly demand trend

### Model Performance

Displays:

* MAE
* RMSE
* WAPE
* Forecast Bias

### Inventory Health

Products are categorized into:

🟢 **Healthy Stock**

🟡 **Reorder Soon**

🔴 **Stockout Risk**

### Replenishment Recommendations

The dashboard provides:

| Product | Location | Current Stock | Forecast | Safety Stock | Reorder Point | Recommended Order |
| ------- | -------- | ------------: | -------: | -----------: | ------------: | ----------------: |
| P014    | Multan   |           180 |      620 |          140 |           510 |               470 |
| P027    | Lahore   |           310 |      540 |          120 |           440 |               250 |
| P008    | Multan   |           420 |      690 |          150 |           580 |               310 |

These values are **synthetic demonstration data**.

---

# 🖥️ Running the Dashboard Locally

The current dashboard is a standalone HTML application.

### 1. Download or clone the repository

```bash
git clone https://github.com/YOUR-USERNAME/demand-replenishment-dashboard.git
```

### 2. Open the dashboard

Navigate into the project folder:

```bash
cd demand-replenishment-dashboard
```

Then open:

```text
demand_replenishment_dashboard.html
```

in Google Chrome, Microsoft Edge, or another modern browser.

No backend server is required for the current prototype.

---

# 🌐 Deploying with GitHub Pages

The dashboard can be deployed as a static website using **GitHub Pages**.

### Step 1 — Create a repository

Create a GitHub repository such as:

```text
demand-prediction-replenishment-dashboard
```

### Step 2 — Add the dashboard

Upload:

```text
demand_replenishment_dashboard.html
```

### Step 3 — Rename the file

For easier GitHub Pages deployment, rename it to:

```text
index.html
```

Your repository can then have:

```text
demand-prediction-replenishment-dashboard/
│
├── index.html
└── README.md
```

### Step 4 — Enable GitHub Pages

Go to:

```text
Repository → Settings → Pages
```

Select:

```text
Deploy from a branch
```

Choose:

```text
main
/
(root)
```

Then save.

GitHub will provide a public website URL for the dashboard.

---

# 📁 Recommended Repository Structure

As the project develops, the repository can be expanded into:

```text
demand-prediction-replenishment/
│
├── README.md
│
├── dashboard/
│   └── index.html
│
├── data/
│   ├── raw/
│   └── processed/
│
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_feature_engineering.ipynb
│   └── 03_model_training.ipynb
│
├── src/
│   ├── data_preprocessing.py
│   ├── feature_engineering.py
│   ├── train_model.py
│   ├── forecasting.py
│   └── replenishment.py
│
├── models/
│   └── xgboost_model.pkl
│
├── reports/
│   └── model_evaluation.md
│
└── requirements.txt
```

The current HTML dashboard is the **front-end prototype**. The Python ML pipeline can later be connected to it or replaced with a Streamlit-based application.

---

# 🛠️ Technology Stack

### Machine Learning

* Python
* pandas
* NumPy
* scikit-learn
* XGBoost
* Optional: LightGBM

### Data Visualization

* Matplotlib
* Seaborn
* HTML/CSS/JavaScript

### Dashboard

* HTML5
* CSS3
* JavaScript

### Deployment

* GitHub
* GitHub Pages

### Future Option

* Streamlit

---

# 🚀 Future Improvements

The current dashboard is a professional prototype. Future versions can connect the interface to a live ML pipeline.

Potential improvements include:

* Real business datasets
* Automated model retraining
* Live database connection
* Dynamic forecast generation
* Prediction intervals
* Product-level forecast drill-down
* Supplier management
* MOQ constraints
* Lead-time variability
* Scenario analysis
* SHAP-based model explainability
* Interactive demand charts
* Automated replenishment alerts
* Streamlit deployment
* Cloud deployment
* API-based ML prediction service

---

# 🎓 Academic Scope

The project is intentionally structured so that the core implementation remains appropriate for a university Machine Learning project.

### Essential

* Data preprocessing
* Exploratory data analysis
* Feature engineering
* Baseline forecasting
* Random Forest
* XGBoost
* Time-based validation
* Forecast evaluation
* Reorder point
* Safety stock
* Recommended order quantity
* Dashboard

### Recommended

* LightGBM comparison
* Feature importance / SHAP
* Stockout analysis
* Business metrics
* Interactive dashboard
* Scenario analysis

### Advanced

* Probabilistic forecasting
* Prediction intervals
* Multi-horizon forecasting
* LSTM comparison
* Optimization-based replenishment
* Automated retraining

---

# ⚠️ Data Disclaimer

The dashboard currently uses **synthetic demonstration data**.

It does not claim to represent proprietary sales, inventory, supplier, or operational data from any specific company.

For a real deployment, the model should be trained and validated using properly authorized business data.

---

# 👨‍💻 Project Purpose

This project demonstrates how Machine Learning can move beyond simple prediction toward **practical business decision support**.

The central concept is:

```text
Historical Data
      ↓
Demand Forecast
      ↓
Inventory Analysis
      ↓
Stockout / Overstock Risk
      ↓
Reorder Point
      ↓
Recommended Order Quantity
      ↓
Management Decision
```

---

## 📌 Project Title

**Demand Prediction & Replenishment Support Using Machine Learning**

### Domain

**Supply Chain & Inventory Management**

### Primary ML Algorithm

**XGBoost Regressor**

### Forecast Horizon

**Next Week**

### Granularity

**Product × Location × Week**

### Decision Output

**Forecast Demand + Inventory Risk + Recommended Replenishment Quantity**

---

## ⭐ Project Status

**Current Status:** Dashboard Prototype / Academic Project

**Next Development Stage:** Connect the dashboard to the actual Python ML forecasting and replenishment pipeline.
