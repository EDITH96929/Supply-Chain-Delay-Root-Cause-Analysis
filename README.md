# Supply chain delay root cause analysis
 
Identifying why shipments breach SLA, and proving the cause with data —
built end to end from a public logistics dataset using SQL, Python, and
Tableau.
 
**Stack:** Python (Pandas, statsmodels, seaborn) · SQL (SQLite) · Tableau
 
---
## The question
 
Shipments were breaching SLA at a high rate. Is it a warehouse problem, a
carrier problem, or a symptom of what kind of orders get shipped? The
analysis rules out alternatives one at a time until only one explanation
survives.
 
## Dataset
 
[DataCo Smart Supply Chain for Big Data Analysis](https://www.kaggle.com/datasets/shashwatwork/dataco-smart-supply-chain-for-big-data-analysis)
(Kaggle, public). ~180K order line items; sampled to 50,000 shipment
records for this analysis. `Order Region` was collapsed into 12 warehouse
buckets, `Shipping Mode` into 3 carrier tiers, to match a realistic
network scale.

## Method
 
1. **Clean** — parsed dates, computed `delay_days` and `sla_breach`,
   bucketed 23 raw regions into 12 warehouses and 4 shipping modes into 3
   carriers.
2. **SQL** — aggregated breach rate by warehouse, by carrier, and by
   warehouse × carrier route to find where breaches concentrate.
3. **Correlation analysis** — checked order-level features (discount
   rate, price, quantity, profit ratio) against breach risk, and compared
   those features across carriers, to rule out "harder orders" as a
   confound.
4. **Time-series decomposition (STL)** — split the weekly breach-rate
   series into trend, seasonal, and residual components to check whether
   the problem was worsening, seasonal, or chronic.
5. **Tableau dashboard** — one-page interactive view (KPI cards, carrier
   comparison, warehouse × carrier heatmap, weekly trend) with carrier
   and warehouse filters, built for non-technical stakeholders.
