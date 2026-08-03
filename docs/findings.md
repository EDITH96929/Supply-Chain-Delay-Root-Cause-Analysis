# Findings: supply chain delay root cause analysis

## Summary

Analyzed 50,000 shipment records across 12 warehouses and 3 carriers to
identify the root cause of SLA breaches. Carrier B (Expedited) breaches
SLA at 84.7% — more than double the network average — and this holds
uniformly across every warehouse and every order type, with no
correlation to order size, discount rate, or profit ratio. The problem
has been stable since 2015, with no seasonal or trend pattern explaining
it away. The root cause is isolated to carrier performance.

## Method

1. Cleaned and loaded 50,000 records into SQLite; computed `delay_days`
   (actual − scheduled shipping days) and `sla_breach` (binary) per
   shipment.
2. Ran SQL aggregation to rank warehouses and carriers by breach rate.
3. Cross-tabulated warehouse × carrier to test whether the effect held
   across regions or was localized.
4. Ran correlation analysis between order-level features (discount rate,
   unit price, quantity, profit ratio) and breach risk, and compared
   those features' distributions across carriers, to test for
   confounding (i.e. "Carrier B just handles harder orders").
5. Ran STL time-series decomposition on the weekly breach rate, overall
   and for Carrier B specifically, to separate genuine trend from
   seasonal noise.

## Key findings, in order of discovery

**1. Overall breach rate: 54.9%** across all 50,000 shipments — high, and
the first sign something systemic is wrong.

**2. It's not the warehouses.** Breach rate by warehouse ranges narrowly;
no single region stood out as a driver.

**3. It's the carrier.**

| Carrier | Shipments | Breach rate | Avg delay (days) |
|---|---|---|---|
| Carrier B (Expedited) | 17,364 | **84.7%** | 1.55 |
| Carrier C (Premium) | 2,735 | 46.5% | 0.48 |
| Carrier A (Ground) | 29,901 | 38.3% | 0.00 |

**4. It's not a regional Carrier B problem — it's everywhere Carrier B
operates.** Breach rate for Carrier B by warehouse ranges from 82.9%
(Oceania) to 87.5% (Southern Europe) — a 4.6 percentage-point spread
across 12 warehouses. That's noise, not a pattern; if this were a
fulfillment/regional issue, we'd expect much larger swings.

**5. It's not the type of orders Carrier B handles.** Correlation between
`sla_breach` and order-level features (discount rate, price, quantity,
profit ratio) is ~0.00–0.02 across the board — none of them predict
breach risk. And the average discount rate, price, quantity, and profit
ratio are nearly identical across all three carriers (e.g. discount rate:
0.101–0.103). Carrier B isn't getting bigger or more complex orders.

**6. It's chronic, not worsening or seasonal.** STL decomposition of
Carrier B's weekly breach rate (Jan 2015 – Jan 2018) shows a trend
component that oscillates between 0.80–0.86 with no sustained upward or
downward drift, and a small seasonal component (±0.05) — meaning the
problem has been present at roughly this severity for the entire
3-year window, not a recent development and not something correcting
itself.

## Business impact

- Carrier B carries only ~35% of shipment volume (17,364 of 50,000) but
  accounts for **53.6% of all network SLA breaches** (14,715 of 27,442
  total breaches).
- Every alternative explanation (region, order mix, seasonality,
  worsening trend) has been ruled out — this is as close to an isolated,
  provable root cause as the data supports.

## Recommendation

Bring Carrier B's SLA performance data into contract renewal
discussions. The consistent 3-year pattern and lack of any confounding
explanation supports either a performance-improvement clause or
reallocating a share of Carrier B's volume to Carrier A, particularly for
non-time-sensitive shipments where the "expedited" service tier isn't
delivering its expected benefit.

## Limitations

- Public dataset (DataCo Smart Supply Chain), not live production data —
  the findings demonstrate methodology rather than describe an actual
  company's real operations.
- "Late" is defined by the dataset's `Late_delivery_risk` flag; the exact
  SLA/contract terms behind that flag aren't independently verifiable.
- Warehouse and carrier groupings (12 regions, 3 carrier tiers) were
  constructed by bucketing the raw dataset's 23 regions and 4 shipping
  modes — a modeling choice, documented in the notebook, not raw ground
  truth.
