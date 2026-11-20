# Customer-360-analytics-project
[README.md](https://github.com/user-attachments/files/35118982/README.md)
# Customer 360 Marketing Analytics

[README.md](https://github.com/user-attachments/files/35119149/README.md)

# Customer 360 Marketing Analytics

A project that brings together public GA4 ecommerce data and a clearly labeled synthetic customer marketing dataset. The goal is to explore customer behavior, demonstrate identity mapping, calculate marketing metrics, and prepare data for Tableau. ~ Tejas shrirame

## What I did

1. Planned the data sources.** Selected Google’s public, obfuscated GA4 ecommerce sample and designed synthetic CRM, advertising, touchpoint, and order data.
2. Generated synthetic data.** Created 1,000 fictional customer profiles and identity links, 120 monthly campaign-spend rows, 2,437 touchpoints, and 2,394 orders. All generated records are labeled synthetic. They do not represent real people, customers, or company performance.
3. Prepared identity mapping.** Added a demonstration bridge from each synthetic web user ID to a synthetic CRM customer ID. This is a simple portfolio example, not production identity resolution.
4. Prepared dbt-style models.** Wrote SQL models for sessions, orders, and customer profiles under `dbt/models/`.
5. Prepared analytics SQL.** Added queries for RFM scoring, an illustrative CLV proxy, first-touch, last-touch, linear and time-decay attribution, campaign ROI, and CAC.
6. Prepared Tableau guidance.** Documented three dashboard layouts and metric definitions under `tableau/`.



## Project files

- `data/` — generated synthetic CSV files for customers, identity links, ad spend, touchpoints, and orders.
- `python/generate_synthetic_data.py` — Python generator (standard library only).
- `python/generate_synthetic_data.ps1` — Windows PowerShell fallback used to create the included CSVs in this environment.
- `sql/00_ga4_exploration.sql` — public GA4 sample exploration queries; not run from this environment.
- `sql/01_bigquery_setup_and_load.sql` — BigQuery dataset and table setup guidance.
- `dbt/` — dbt project configuration and sessions, orders, and customer profile models; not executed.
- `sql/02_customer_metrics.sql` — RFM, illustrative CLV, attribution, ROI, and CAC SQL; not executed.
- `tableau/dashboard_guide.md` — Tableau-ready dashboard design and definitions; no Tableau workbook is included.


## Metric assumptions

- **RFM:** recency, purchase frequency, and monetary value are ranked into quintiles.
- **Illustrative CLV:** a simple historical-value proxy, not a predictive forecast.
- **Attribution:** eligible synthetic touchpoints within 30 days before an order; time decay uses a 7-day half-life.
- **ROI:** (attributed revenue − ad spend) / ad spend.
- **CAC:** ad spend / unique customers with attributed orders.

These are teaching and demonstration definitions. Since the synthetic funnel is separate from the public GA4 sample, do not interpret its metrics as real business results.

## Official references

- [Google Analytics: BigQuery sample ecommerce dataset](https://developers.google.com/analytics/bigquery/web-ecommerce-demo-dataset)
- [Google Cloud: BigQuery public datasets](https://docs.cloud.google.com/bigquery/public-data)
- [Google Analytics: Basic BigQuery export queries](https://developers.google.com/analytics/bigquery/basic-queries)
