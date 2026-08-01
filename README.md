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
