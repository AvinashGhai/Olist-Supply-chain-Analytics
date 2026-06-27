# Olist Supply Chain Risk Intelligence

An end-to-end supply chain analytics pipeline built on 110,000+ real e-commerce orders from Olist, Brazil's largest online marketplace. The project identifies delivery delays, flags high-risk routes and sellers, scores demand concentration risk, and delivers actionable intelligence through a live Tableau dashboard.

---

## Live Dashboard

[View on Tableau Public]
(https://public.tableau.com/app/profile/avinash.ghai/viz/SupplychainRiskintelligence/Dashboard1)

---

## Dashboard Preview

### Overview
(<img width="2560" height="1664" alt="image" src="https://github.com/user-attachments/assets/39f47249-cb59-4f77-9f1e-81f41c6d3e85" />
)

### Brazil State Map — Late Delivery Rate
(<img width="1432" height="1228" alt="image" src="https://github.com/user-attachments/assets/3a01c886-b2bc-43a3-a7a5-3e5d5990c261" />
)

### Monthly Order Volume vs Late Delivery Rate
(<img width="1220" height="1266" alt="image" src="https://github.com/user-attachments/assets/f2b321f3-a3b2-4a64-874d-c6ecfc6ea7b5" />
)

### Route Risk Analysis
(<img width="1508" height="1208" alt="image" src="https://github.com/user-attachments/assets/cde5052b-4758-40bc-a1a9-8fd2f582b0dd" />
)

### SLA Breakdown
(<img width="402" height="406" alt="image" src="https://github.com/user-attachments/assets/8a341735-5e79-46bc-93ed-635142f35232" />
)

## Problem Statement

Supply chain inefficiencies cost e-commerce businesses millions in lost revenue and customer trust. This project answers four key business questions:

- Where are deliveries failing, and which routes carry the highest risk?
- Can we predict delivery delays before an order is dispatched?
- Which sellers are unreliable, and how do we score them objectively?
- Which product categories face the highest demand concentration risk?

---

## Key Results

| Metric | Value |
|---|---|
| Total orders analyzed | 110,189 |
| Revenue at risk from late orders | R$ 1,368,509 |
| Delay prediction RMSE | 6.42 days |
| Delay prediction R-squared | 0.60 |
| Late delivery classifier ROC-AUC | 0.75 |
| High and critical risk routes identified | 3 |
| High-risk sellers flagged | 1 |
| Critical demand concentration categories | 2 |

Note: The classifier ROC-AUC of 0.75 reflects a leak-free model using only pre-delivery features. An earlier version using post-delivery features achieved 0.97 but was discarded due to data leakage.

---

## Tech Stack

| Layer | Tools |
|---|---|
| Data ingestion and cleaning | Python, Pandas |
| Cloud data warehouse | Google BigQuery |
| SQL analysis | BigQuery SQL |
| Machine learning | XGBoost, Scikit-learn |
| Visualization | Tableau Public |
| Version control | Git, GitHub |

---

## Project Structure

```
supply-chain-analytics/
│
├── data/
│   ├── charts/                          # 9 analysis charts (PNG)
│   ├── tableau/                         # 8 Tableau-ready CSVs
│   ├── model_predictions.csv
│   ├── seller_scorecard.csv
│   ├── route_risk.csv
│   ├── demand_concentration.csv
│   ├── priority_orders.csv
│   └── feature_importance_leakfree.png
│
├── notebooks/
│   ├── 01_data_ingestion.ipynb
│   ├── 02_sql_analysis.ipynb
│   ├── 03_ml_models.ipynb
│   ├── 04_risk_intelligence.ipynb
│   └── 05_dashboard_prep.ipynb
│
├── .gitignore
├── requirements.txt
└── README.md
```

---

## Pipeline

### Notebook 01 — Data Ingestion and Feature Engineering

- Downloaded the Olist Brazilian E-Commerce dataset consisting of 9 interlinked CSV files covering orders, products, sellers, customers, payments, and reviews
- Merged all 9 tables into a single master DataFrame using Pandas
- Engineered key supply chain features:
  - delivery_delay_days: difference between actual and estimated delivery date
  - is_late: binary flag for orders delivered after the estimated date
  - revenue_at_risk: payment value of late orders
  - demand_concentration_score: order velocity relative to seller count per product
- Uploaded the clean master table to Google BigQuery via pandas-gbq

### Notebook 02 — SQL Analysis in BigQuery

- Wrote analytical SQL queries directly in BigQuery covering:
  - Delivery delay by customer state and seller state
  - Monthly late delivery trend across 3 years
  - Revenue at risk aggregations by state and category
  - Demand concentration risk by product category
  - Year-over-year performance comparison
- Exported all results as CSVs and saved analysis tables back to BigQuery

### Notebook 03 — Machine Learning Models

Two models were trained using only pre-delivery features to avoid data leakage. Features such as shipping_duration_days, which are only known after delivery, were explicitly excluded.

**Delivery Delay Prediction (XGBoost Regression)**
- Target: delivery_delay_days
- Key features: seller historical late rate, route historical late rate, product dimensions, payment value, order timing
- Results: RMSE 6.42 days, MAE 4.63 days, R-squared 0.60

**Late Delivery Classifier (XGBoost Classifier)**
- Target: is_late (binary)
- Handled class imbalance via scale_pos_weight
- Results: ROC-AUC 0.75

The most important predictors were seller_late_rate and route_late_rate, confirming that historical seller and route behavior is the strongest signal for predicting future delays.

### Notebook 04 — Risk Intelligence

**Seller Reliability Scoring**

Each seller with 10 or more orders was scored using a composite formula:

```
reliability_score = 100 - late_rate_penalty - delay_penalty - cancellation_penalty
```

Sellers were classified into three tiers: Reliable, Watchlist, and High Risk.

**SLA Breach Categorization**

Orders were classified into five severity tiers:
- Early (3+ days ahead of schedule)
- On Time
- 1-3 Days Late
- 4-7 Days Late
- 8+ Days Late

**Route Risk Analysis**

Seller state to customer state pairs were analyzed for late delivery rate, average delay, and revenue at risk. Routes were classified as Critical, High, Medium, or Low risk based on their late delivery percentage.

**Revenue at Risk Priority Scoring**

Each order was assigned a priority score:

```
priority_score = late_probability x payment_value
```

Orders were assigned one of three intervention actions: Priority carrier escalation, Proactive customer update, or Standard monitoring.

**Demand Concentration Risk**

Product categories were scored based on order volume relative to seller count. High demand with low seller coverage indicates concentration risk. Categories were labeled Critical, High, Medium, or Low.

### Notebook 05 — Dashboard Preparation

- Pulled all final tables from BigQuery
- Exported 8 clean Tableau-ready CSVs with pre-calculated fields and correct data types
- Prepared primary flat file with SLA categories pre-computed for drag-and-drop use in Tableau

---

## Dashboard Views

| Sheet | Data Source | Insight |
|---|---|---|
| KPI Scorecards | kpi_summary | Total orders, late rate, avg delay, revenue at risk |
| Brazil State Map | state_summary | Late delivery rate choropleth by customer state |
| Monthly Trend | monthly_trend | Order volume vs late rate over 3 years (dual axis) |
| Demand Concentration | category_risk | Top categories by demand concentration risk |
| Route Risk | route_risk | Highest risk seller-to-customer state routes |
| SLA Breakdown | sla_summary | Order distribution across 5 delivery severity tiers |
| Seller Scorecard | seller_scorecard | Reliability tier breakdown with revenue at risk |

---

## Key Business Insights

- Seller historical late rate and route late rate are the strongest predictors of delivery delay, outweighing product weight, dimensions, and payment value
- The SP to RJ and SP to BA routes carry disproportionate revenue at risk relative to their order volumes
- Furniture and garden tools categories show the highest demand concentration risk, with high order volumes supported by very few sellers
- Late delivery rates improved significantly from 2017 to 2018, suggesting operational improvements over time
- One seller accounts for R$18,000 in revenue at risk and was flagged as High Risk, making them a clear candidate for account review

---

## How to Reproduce

1. Clone the repository:

```bash
git clone https://github.com/AvinashGhai/Olist-Supply-chain-Analytics.git
cd Olist-Supply-chain-Analytics
```

2. Install dependencies:

```bash
pip install -r requirements.txt
```

3. Download the Olist Brazilian E-Commerce dataset from Kaggle and place all CSV files in the data/ directory:
https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

4. Set up Google BigQuery:
   - Create a GCP project
   - Enable the BigQuery API
   - Create a service account with BigQuery Admin role
   - Download the JSON key and save it as bq_key.json in the project root

5. Run notebooks in order:
   - 01_data_ingestion.ipynb
   - 02_sql_analysis.ipynb
   - 03_ml_models.ipynb
   - 04_risk_intelligence.ipynb
   - 05_dashboard_prep.ipynb

---

## Requirements

```
pandas
numpy
pandas-gbq
google-cloud-bigquery
google-auth
scikit-learn
xgboost
matplotlib
seaborn
db-dtypes
```

---

## Author

Avinash Ghai

GitHub: https://github.com/AvinashGhai

