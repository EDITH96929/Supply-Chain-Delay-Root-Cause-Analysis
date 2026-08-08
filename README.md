# Supply Chain Delay Root Cause Analysis

A complete end-to-end data analytics project that identifies **why shipments breach Service Level Agreements (SLAs)** by systematically testing competing hypotheses using **SQL, Python, statistical analysis, and Tableau**.

Instead of simply showing where delays occur, this project answers the more important business question:

> **Are shipment delays caused by warehouse operations, carrier performance, or the type of orders being shipped?**

Using statistical evidence and exploratory analysis, the project rules out alternative explanations until the primary root cause is isolated.

---

## Project Overview

Late deliveries increase logistics costs, reduce customer satisfaction, and directly impact business performance. Operations teams often struggle to determine whether delays originate from fulfillment centers, transportation providers, or differences in order complexity.

This project performs a structured root cause analysis on a public logistics dataset to identify the primary driver behind SLA breaches.

The analysis follows an evidence-based approach:

1. Clean and prepare shipment data.
2. Measure SLA breach rates across warehouses and carriers.
3. Test whether delayed shipments are associated with different order characteristics.
4. Analyze long-term trends to determine whether delays are seasonal or persistent.
5. Build an interactive Tableau dashboard for operational decision-making.

---

# Business Question

The project investigates three competing hypotheses.

### Hypothesis 1
**Warehouse operations are responsible for late shipments.**

If true, certain warehouses should consistently perform worse than others.

---

### Hypothesis 2
**Carrier performance is responsible for late shipments.**

If true, one carrier should consistently exhibit significantly higher breach rates regardless of warehouse.

---

### Hypothesis 3
**Certain carriers simply receive more difficult orders.**

If true, delayed carriers should handle shipments with significantly different characteristics such as:

- larger quantities
- higher discounts
- more expensive products
- lower profit margins

The analysis tests each hypothesis individually until only one explanation remains supported by the data.

---

# Dataset

**Source**

DataCo Smart Supply Chain for Big Data Analysis

https://www.kaggle.com/datasets/shashwatwork/dataco-smart-supply-chain-for-big-data-analysis

### Dataset Size

- Original dataset: ~180,000 order line items
- Sample used: **50,000 shipment records**
- Public logistics dataset

For analysis, several variables were simplified to better resemble a realistic logistics network.

- 23 geographic regions → **12 warehouse groups**
- 4 shipping modes → **3 carrier tiers**

---

# Tech Stack

| Tool | Purpose |
|-------|----------|
| Python | Data cleaning & statistical analysis |
| Pandas | Data manipulation |
| NumPy | Numerical operations |
| SQLite | SQL analysis |
| Statsmodels | STL time-series decomposition |
| Matplotlib | Visualizations |
| Seaborn | Statistical plots |
| Tableau | Interactive dashboard |

---

# Analysis Workflow

## 1. Data Cleaning

The raw dataset was cleaned using Pandas.

Major preprocessing steps included:

- parsing shipment dates
- removing invalid records
- calculating shipment delay
- creating SLA breach flags
- grouping regions into warehouse buckets
- grouping shipping modes into carrier categories

Two important derived variables were created:

### Delay Days

```
delay_days = actual_delivery_date - scheduled_delivery_date
```

### SLA Breach

```
sla_breach = 1 if delay_days > 0 else 0
```

---

## 2. SQL Analysis

SQLite was used to aggregate shipment performance across different operational dimensions.

The analysis calculated:

- overall SLA breach rate
- warehouse-level breach rate
- carrier-level breach rate
- warehouse × carrier breach matrix
- shipment volumes
- contribution of each carrier to network delays

These summaries helped identify where delays were concentrated.

---

## 3. Correlation & Confounding Analysis

A higher breach rate alone does not necessarily imply poor carrier performance.

A carrier may simply receive more difficult shipments.

To test this possibility, the project compared the following variables across carriers:

- Discount Rate
- Product Price
- Quantity Ordered
- Profit Ratio

Statistical comparisons showed these variables were nearly identical across carriers.

This ruled out order complexity as a meaningful explanation for Carrier B's poor performance.

---

## 4. Time-Series Analysis

Weekly SLA breach rates were aggregated into a time series.

STL (Seasonal-Trend decomposition using LOESS) was used to separate the series into:

- Trend
- Seasonal component
- Residual noise

The objective was to determine whether delays were:

- seasonal,
- gradually worsening,
- or consistently present over time.

---

## 5. Dashboard Development

A one-page Tableau dashboard was created for non-technical stakeholders.

The dashboard includes:

- Executive KPI cards
- Carrier comparison
- Warehouse performance
- Warehouse × Carrier heatmap
- Weekly breach trend
- Interactive filters

Selecting any warehouse or carrier dynamically updates every visualization.

---

# Dashboard

## Overview

*(Insert Tableau dashboard screenshot here)*

Published Tableau Dashboard:

> **Add Tableau Public link after publishing**

---

### Dashboard Features

- Overall SLA KPI
- Breach Rate by Carrier
- Warehouse Comparison
- Warehouse × Carrier Heatmap
- Weekly Trend
- Interactive Filters

---

# Key Findings

## Overall Network Performance

- **Overall SLA Breach Rate:** **54.9%**

More than half of all shipments failed to meet their delivery SLA.

---

## Carrier Performance

Carrier performance varied dramatically.

| Carrier | SLA Breach Rate |
|----------|----------------:|
| Carrier A | 38.3% |
| Carrier B | **84.7%** |
| Carrier C | 46.5% |

Carrier B breached SLA at more than **twice the rate** of Carrier A.

---

## Warehouse Analysis

Carrier B performed poorly in **every warehouse**.

Across all 12 warehouse groups:

- Lowest breach rate: **82.9%**
- Highest breach rate: **87.5%**

Spread:

```
Only 4.6 percentage points
```

Because the problem appears consistently across every warehouse, warehouse operations are unlikely to be the primary cause.

---

## Order Mix Analysis

Order characteristics were compared across carriers.

Variables examined:

- Discount Rate
- Product Price
- Quantity
- Profit Ratio

The distributions were statistically indistinguishable.

This indicates Carrier B was **not** assigned more difficult shipments.

---

## Network Impact

Although Carrier B handled approximately **35%** of shipment volume, it generated:

**53.6% of all SLA breaches**

This made Carrier B the largest contributor to network-wide delivery failures.

---

## Time-Series Findings

STL decomposition showed:

- no meaningful seasonality
- no upward trend
- no evidence of gradual operational deterioration

Instead, Carrier B consistently remained above the network average throughout the observation period.

The problem was chronic rather than temporary.

---

# Final Conclusion

The evidence systematically eliminates alternative explanations.

### Warehouse Operations

❌ Not supported

Carrier B underperformed in every warehouse.

---

### Order Characteristics

❌ Not supported

Shipment complexity remained consistent across carriers.

---

### Carrier Performance

✅ Strongly supported

Carrier B consistently exhibited significantly higher SLA breach rates across all warehouses and throughout the full time period.

The findings suggest that operational improvement efforts should focus on **carrier contract evaluation, SLA enforcement, or transportation provider replacement** rather than warehouse process optimization.

---

# Project Structure

```text
supply-chain-delay-analysis/
│
├── 01_clean_and_explore.ipynb
│
├── data/
│   ├── raw/
│   │   └── DataCoSupplyChainDataset.csv
│   │
│   └── processed/
│       ├── shipments.csv
│       ├── supply_chain.db
│       └── chart_exports/
│
├── sql/
│   └── analysis_queries.sql
│
├── docs/
│   ├── findings.md
│   └── images/
│       ├── dashboard_overview.png
│       ├── carrier_heatmap.png
│       └── weekly_trend.png
│
├── tableau/
│   └── supply_chain_dashboard.twbx
│
└── README.md
```

---

# How to Reproduce

## 1. Clone the repository

```bash
git clone https://github.com/yourusername/supply-chain-delay-analysis.git
```

---

## 2. Download the dataset

Download the dataset from Kaggle and place it inside:

```
data/raw/
```

---

## 3. Install dependencies

```bash
pip install pandas numpy matplotlib seaborn statsmodels sqlalchemy
```

---

## 4. Run the notebook

Open

```
01_clean_and_explore.ipynb
```

Run all cells sequentially.

---

## 5. Explore the Dashboard

Open

```
tableau/supply_chain_dashboard.twbx
```

or view the Tableau Public version after publishing.

---

# Limitations

- Uses a **public academic dataset** rather than production logistics data.
- Results demonstrate an analytical methodology rather than the performance of a real organization.
- Warehouse and carrier groupings were simplified for readability and visualization.
- Estimated financial impact (if included) uses proxy metrics rather than audited operational costs.

---

# Future Improvements

Possible extensions include:

- Predict SLA breaches using machine learning.
- Forecast future carrier performance.
- Add confidence intervals for breach rates.
- Perform statistical significance testing between carriers.
- Build an automated ETL pipeline.
- Deploy the dashboard using Tableau Server or Power BI Service.
- Integrate live shipment data from cloud databases.

---

# Skills Demonstrated

- Data Cleaning
- Exploratory Data Analysis (EDA)
- SQL Aggregation
- Root Cause Analysis
- Statistical Analysis
- Correlation Analysis
- Time-Series Decomposition (STL)
- Data Visualization
- Dashboard Design
- Business Intelligence
- Storytelling with Data
- End-to-End Analytics Workflow

---

## Author

**Your Name**

Data Analyst | SQL | Python | Tableau

GitHub: https://github.com/EDITH96929

LinkedIn: [https://linkedin.com/in/yourprofile](https://www.linkedin.com/in/sunil-kumar-swain-584660288/)
