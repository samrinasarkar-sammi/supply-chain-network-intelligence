# Supply Chain Network Intelligence: Delay Risk, Structural Resilience & Demand Forecasting

A multi-method analysis of the [DataCo Smart Supply Chain dataset](https://data.mendeley.com/datasets/8gx2fvg2k6/5) — combining predictive risk modeling, network science, and time series forecasting to understand not just *what* is going wrong in delivery performance, but *where the structural leverage points are* and *what's likely to happen next*.

## Overview

This project investigates four questions:

1. **How reliable is delivery, and what drives late shipments?**
2. **Does lateness actually hurt profitability?**
3. **Can late-delivery risk be predicted before an order ships?**
4. **NEW — Where are the structurally critical points in the logistics network, and how resilient is it to disruption? What does near-term demand look like?**

**Key findings:**
- **54.7% of orders arrive late**, driven overwhelmingly by *shipping mode* (First Class: 100% late, Second Class: 79.8%, Standard: 39.8%) — not by region, segment, or season. Region and segment delay rates cluster tightly (54–59%), while shipping mode swings from 0% to 100%.
- Per-order profit stays roughly flat ($20–23) regardless of delay length — the business risk from lateness is reputational, not a direct margin hit.
- A Random Forest model predicts late-delivery risk at booking time with **0.825 ROC-AUC**.
- **Network analysis** identifies `DEPT: Fitness` as the most structurally central node (betweenness centrality 0.797) in the Region–Department–Shipping Mode network — but a disruption simulation shows the network remains fully connected even after removing it, indicating real structural redundancy rather than a single point of failure.
- **Demand forecasting** (Holt-Winters exponential smoothing) initially produced an implausible result — diagnosed as a genuine data-quality anomaly (order volume drops ~60% for 4 months starting Oct 2017, with unnaturally flat daily counts). After excluding this anomalous period, the corrected model achieves **5.36% holdout MAPE** and projects stable demand of ~$1.0M–$1.14M/month for that window — evidence the recorded drop is a data artifact, not a real business decline.

## Repository Contents

| File | Description |
|---|---|
| `Supply_Chain_Analysis_Project.ipynb` | Full analysis notebook: cleaning, EDA, profitability, bottleneck/root-cause detection, time-based patterns, ML risk modeling, network analysis, and forecasting |
| `DataCoSupplyChainDataset.csv` | Source dataset (see [Data](#data) below) |
| `Supply_Chain_Network_Intelligence_Report.docx` | Full written report: executive summary, KPIs, findings, network analysis, forecasting, dashboard, recommendations, and proposed next-phase research direction |
| `supply_chain_cleaned.csv` | Cleaned, feature-engineered dataset (dashboard source) |
| `network_centrality.csv` | Node-level degree/betweenness centrality results |
| `network_edges.csv` | Network edge list (Region–Department–Shipping Mode flows) |
| `network_graph.png` | Visualized logistics network, sized/colored by centrality |
| `sales_timeline.csv` | Combined actual + forecasted monthly sales |
| `dashboard.pbix` | Interactive Power BI dashboard |

## Data

Order-level supply chain data: shipping mode, order status, delivery dates, product category, customer segment, region, and profit per order. Download from [Mendeley Data](https://data.mendeley.com/datasets/8gx2fvg2k6/5); the CSV uses `latin-1` encoding.

## Analysis Pipeline

1. **Data cleaning** — drop redundant/PII columns, parse order/shipping dates
2. **Feature engineering** — `Order Processing Time`, `Delay`, `Is_Delayed`, `Profitability Flag`, calendar features
3. **EDA** — KPI summary, profitability distribution, delay distribution vs. profit
4. **Bottleneck detection** — delay % by region, segment, shipping mode, order status, type, department
5. **Root cause analysis** — factor drill-down within the worst-performing region
6. **Time-based analysis** — delay % by month, day of week, hour of day
7. **Machine learning** — predicting `Late_delivery_risk` (Logistic Regression, Decision Tree, Random Forest, Gradient Boosting, KNN — class-balanced with SMOTE)
8. **NEW — Network analysis** — tripartite graph (Region–Department–Shipping Mode) built with `networkx`; degree and betweenness centrality identify structurally critical nodes; a disruption simulation tests network resilience by removing the most central node
9. **NEW — Demand forecasting** — Holt-Winters exponential smoothing (damped trend, multiplicative seasonality) on monthly sales, validated on a 6-month holdout
10. **NEW — Interactive dashboard** — Power BI dashboard combining KPIs, delay/profit breakdowns, network centrality, and the sales forecast, with searchable slicers for Region, Department, Shipping Mode, and Customer Segment

## Requirements

```
pandas
numpy
matplotlib
seaborn
scikit-learn
imbalanced-learn
networkx
statsmodels
```

## Key Results

### Late-Delivery Risk Model

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|
| Decision Tree | 0.719 | 0.768 | 0.730 | 0.748 | 0.728 |
| **Random Forest** | 0.738 | 0.785 | 0.746 | 0.765 | **0.825** |
| KNN | 0.697 | 0.761 | 0.686 | 0.722 | 0.767 |
| Gradient Boosting | 0.725 | 0.893 | 0.590 | 0.710 | 0.778 |
| Logistic Regression | 0.697 | 0.833 | 0.590 | 0.690 | 0.709 |

### Network Centrality (Top 5 by Betweenness)

| Node | Degree Centrality | Betweenness Centrality |
|---|---|---|
| DEPT: Fitness | 0.730 | 0.797 |
| REGION: Central Asia | 0.162 | 0.255 |
| SHIP: Same Day | 0.297 | 0.200 |
| DEPT: Book Shop | 0.297 | 0.173 |
| DEPT: Health and Beauty | 0.216 | 0.112 |

### Demand Forecast

An initial forecast attempt produced an implausible result (MAPE 72.45%, declining toward $0) — investigated and traced to a genuine data-quality anomaly: order volume drops ~60% starting October 2017 and stays there for 4 months, with unnaturally flat daily counts suggesting a data-collection issue rather than a real demand collapse. After excluding this anomalous trailing period, the corrected model achieves 5.36% holdout MAPE and projects stable demand of ~$1.0M–$1.14M/month for the excluded window — reinforcing that the recorded drop is a data artifact, not a business decline. Full diagnostic process in the report, Section 9.

## Dashboard

<img width="1257" height="687" alt="Screenshot 2026-09-28 213727" src="https://github.com/user-attachments/assets/27689f29-7272-41a1-b397-ccfbf132ff61" />



## About Me

**Samrina Sarkar Sammi** — M2 Data Science & Network Intelligence student, Télécom SudParis

[LinkedIn](https://www.linkedin.com/in/samrina-sarkar-sammi-a8b716424/) · [GitHub](https://github.com/samrinasarkar-sammi) · samrinasarkar@gmail.com
