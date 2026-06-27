# 🚚 Olist Supply Chain Analytics

An end-to-end supply chain analytics pipeline built on 110,000+ real e-commerce orders from Olist, Brazil's largest online marketplace. The project identifies delivery delays, flags stockout risk, and delivers actionable insights through a live Tableau dashboard.

---

## 📊 Live Dashboard

🔗 [View on Tableau Public](https://public.tableau.com/app/profile/avinash.ghai/viz/OlistSupplyChainAnalytics/Dashboard1)

---

## 🔍 Problem Statement

Supply chain inefficiencies cost e-commerce businesses millions in lost revenue and customer trust. This project answers three key business questions:

1. **Where are deliveries failing?** — Which states and product categories have the highest late delivery rates?
2. **Can we predict delays before they happen?** — Build a model to forecast delivery delay in days.
3. **Which products are at stockout risk?** — Identify products with high demand but low seller coverage.

---

## 📈 Key Results

| Metric | Value |
|--------|-------|
| Total orders analyzed | 110,189 |
| Revenue at risk (late orders) | R$ 1,368,508 |
| Delay prediction RMSE | 6.42 days |
| Delay prediction R² | 0.60 |
| Late delivery classifier ROC-AUC | 0.97 |
| Critical stockout-risk products | 2 |
| High stockout-risk products | 10 |

---

## 🛠️ Tech Stack

| Layer | Tools |
|-------|-------|
| Data ingestion & cleaning | Python, Pandas |
| Cloud data warehouse | Google BigQuery |
| SQL analysis | BigQuery SQL |
| Machine learning | XGBoost, Scikit-learn |
| Visualization | Tableau Public |
| Version control | Git, GitHub |

---

## 🗂️ Project Structure

```
supply-chain-analytics/
│
├── data/
│   ├── delay_by_customer_state.csv
│   ├── delay_by_seller_state.csv
│   ├── monthly_trend.csv
│   ├── stockout_scored.csv
│   ├── delay_by_category.csv
│   ├── feature_importance_delay.png
│   └── confusion_matrix.png
│
├── notebooks/
│   └── phase1_ingestion.ipynb 
├── .gitignore
└── README.md
```

---

## ⚙️ Pipeline

### Phase 1 — Data Ingestion & Feature Engineering
- Downloaded Olist Brazilian E-Commerce dataset (9 CSVs, 100k+ orders)
- Merged all tables using Pandas across orders, products, sellers, customers, payments, and reviews
- Engineered key features:
  - `delivery_delay_days` — actual vs estimated delivery delta
  - `is_late` — binary flag for late orders
  - `stockout_risk_score` — order velocity / seller count ratio
  - `revenue_at_risk` — payment value of late orders
- Uploaded clean master table to **Google BigQuery** via `pandas-gbq`

### Phase 2 — SQL Analysis in BigQuery
- Wrote analytical SQL queries directly in BigQuery:
  - Delivery delay by customer state and seller state
  - Monthly late delivery trend over 3 years
  - Top 20 at-risk products by stockout score
  - Delay rate by product category
  - Revenue at risk aggregations
- Exported results as CSVs for Tableau and saved analysis tables back to BigQuery

### Phase 3 — Machine Learning Models

**Model 1 — Delivery Delay Prediction (XGBoost Regression)**
- Predicted exact delivery delay in days
- Features: shipping duration, product weight/dimensions, seller state, customer state, payment type, category
- Results: RMSE 6.42 days | MAE 4.63 days | R² 0.60

**Model 2 — Late Delivery Classifier (Random Forest)**
- Binary classification: on-time vs late
- Used `class_weight="balanced"` to handle class imbalance
- Results: ROC-AUC 0.97 | Strong precision and recall on late class

**Stockout Risk Scoring**
- Rule-based scoring: order velocity / seller count
- Tiered labels: Critical / High / Medium / Low
- 2 Critical and 10 High risk product categories identified

### Phase 4 — Tableau Dashboard
- Connected BigQuery analysis tables to Tableau Public
- Built 4 views:
  - **Brazil heatmap** — late delivery rate by customer state
  - **Stockout risk bar chart** — top 20 products colored by risk tier
  - **Monthly trend** — order volume vs late % over time (dual axis)
  - **KPI tiles** — total orders, avg delay, late %, revenue at risk
- Published live dashboard to Tableau Public

---

## 📊 Dashboard Preview

| View | Insight |
|------|---------|
| Brazil Heatmap | Northern states show highest late delivery rates |
| Stockout Risk | furniture_decor and garden_tools are Critical risk |
| Monthly Trend | Late % peaks in late 2017, improves through 2018 |
| KPI Tiles | R$ 1.37M revenue at risk from delayed orders |

---

## 🚀 How to Run

1. Clone the repo:
```bash
git clone https://github.com/AvinashGhai/Olist-Supply-chain-Analytics.git
cd Olist-Supply-chain-Analytics
```

2. Install dependencies:
```bash
pip install pandas numpy pandas-gbq google-cloud-bigquery scikit-learn xgboost matplotlib seaborn db-dtypes
```

3. Download the dataset from [Kaggle — Brazilian E-Commerce](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) and place CSVs in `/data`

4. Set up Google BigQuery:
   - Create a GCP project
   - Enable BigQuery API
   - Download service account JSON key → save as `bq_key.json` in project root

5. Run notebooks in order:
   - `phase1_ingestion.ipynb`
   - `phase2_sql_analysis.ipynb`
   - `phase3_ml_models.ipynb`

---

## 📌 Business Insights

- **São Paulo** handles the most orders but also carries the highest absolute revenue at risk
- **Shipping duration** is the strongest predictor of delivery delay — outweighs product and location features
- **furniture_decor** is the highest stockout risk category — high order volume with very few sellers
- Late delivery rate **improved significantly** from 2017 to 2018 suggesting operational improvements over time

---

## 👤 Author

**Avinash Ghai**
[GitHub](https://github.com/AvinashGhai) | [Tableau Public](https://public.tableau.com/app/profile/avinash.ghai)
