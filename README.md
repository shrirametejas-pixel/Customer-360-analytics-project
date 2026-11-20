BOTH DA-02 AND DA-03 INCLUDED IN THIS README.md
# Customer 360 Marketing Analytics - DA-02
A portfolio project demonstrating how ecommerce activity, customer records, and advertising data can support customer and campaign analysis. The project combines an exploration plan for Google’s public GA4 sample with a separate, end-to-end **synthetic** demo dataset.

> **Data and completion note:** Every CRM, identity, advertising, touchpoint, and order row in `data/` is synthetic. The public GA4 sample was not queried from this environment because the BigQuery console was unavailable. BigQuery/dbt SQL and Tableau designs are included as reproducible project work, but they have not been executed or validated against a live account. No business findings are claimed.

## Project architecture

```mermaid
flowchart LR
  GA4[Google public GA4 sample] --> Explore[BigQuery exploration SQL]
  Faker[Synthetic data generator] --> CSV[Customer, identity, spend, touchpoint and order CSVs]
  CSV --> BQ[BigQuery customer360 dataset]
  BQ --> DBT[dbt SQL models]
  DBT --> SM[Sessions, orders and customer profile]
  SM --> Metrics[RFM, illustrative CLV, attribution, ROI and CAC]
  Metrics --> Tables[Tableau-ready tables]
  Tables --> Dash[Tableau dashboard designs]
```

The GA4 sample exploration is intentionally separate from the synthetic funnel. No generated customer or order record is represented as a GA4 user or event.

## Features and deliverables

- **GA4 exploration:** BigQuery Standard SQL for discovering tables, event volumes, event names, traffic-source fields, purchase events, and event parameter keys.
- **Synthetic dataset:** 1,000 fictional customer profiles and identity links, monthly campaign spend, synthetic touchpoints, and synthetic orders. Each CSV has a `synthetic_record` indicator.
- **Identity mapping:** Demonstration bridge between generated `user_pseudo_id` and `customer_id` values.
- **Transformations:** dbt-style BigQuery SQL models for sessions, orders, and customer profiles.
- **Customer and marketing metrics:** RFM quintiles, an illustrative CLV proxy, first-touch, last-touch, linear, and time-decay attribution, campaign ROI, and CAC.
- **Visualization plan:** Tableau dashboard layouts, chart suggestions, filters, and metric definitions.

## Repository layout

```text
customer360/
├── README.md
├── data/                         # Generated synthetic CSVs
├── dbt/
│   ├── dbt_project.yml
│   ├── profiles.yml.example      # Example only; put real profile in your user config
│   └── models/
│       ├── sessions.sql
│       ├── orders.sql
│       └── customer_profile.sql
├── python/
│   ├── generate_synthetic_data.py
│   └── generate_synthetic_data.ps1
├── sql/
│   ├── 00_ga4_exploration.sql
│   ├── 01_bigquery_setup_and_load.sql
│   └── 02_customer_metrics.sql
└── tableau/
    └── dashboard_guide.md
```

## Dataset inventory

The included CSVs were generated with the PowerShell fallback in this environment:

| File | Rows | Description |
|---|---:|---|
| `data/crm_customers.csv` | 1,000 | Fictional customer profiles |
| `data/identity_map.csv` | 1,000 | Synthetic user-to-customer links |
| `data/ad_spend.csv` | 120 | Monthly synthetic spend by campaign |
| `data/touchpoints.csv` | 2,437 | Synthetic campaign interactions |
| `data/synthetic_orders.csv` | 2,394 | Synthetic orders and revenue |

## Setup and run instructions

### 1. Generate or regenerate the synthetic CSVs

On Windows, run from this project folder in PowerShell:

```powershell
powershell -ExecutionPolicy Bypass -File python/generate_synthetic_data.ps1
```

Alternatively, with Python installed, run:

```bash
python python/generate_synthetic_data.py
```

The Python generator uses only the standard library. Each generator is seeded for repeatability; their specific synthetic rows can differ from each other, but both use the same file names, columns, and synthetic labels.

### 2. Explore the public GA4 dataset

Open `sql/00_ga4_exploration.sql` in BigQuery Studio and run its queries individually against:

```text
bigquery-public-data.ga4_obfuscated_sample_ecommerce.events_*
```

Use Google Cloud project `ace-mote-465005-k4` only if it is still available to you, and ensure the BigQuery API and permissions are set up. Review the query’s processed-byte estimate before running it. The public sample is obfuscated and covers November 1, 2020 through January 31, 2021; it is not the synthetic dataset in this repository.

### 3. Load synthetic CSVs into BigQuery

Create a BigQuery dataset named `customer360` in the **US** location, then upload each file from `data/` as a table with the same base name:

| CSV | BigQuery table |
|---|---|
| `crm_customers.csv` | `crm_customers` |
| `identity_map.csv` | `identity_map` |
| `ad_spend.csv` | `ad_spend` |
| `touchpoints.csv` | `touchpoints` |
| `synthetic_orders.csv` | `synthetic_orders` |

Enable header-row handling and schema autodetection for each upload. Then review and run the integrity-check section of `sql/01_bigquery_setup_and_load.sql`. The SQL currently uses project ID `ace-mote-465005-k4`; replace it with your own project ID if needed.

### 4. Run dbt models

Install dbt for BigQuery in your Python environment:

```bash
python -m pip install dbt-bigquery
```

Copy `dbt/profiles.yml.example` to your dbt user configuration directory as `profiles.yml` (on Windows, usually `%USERPROFILE%\.dbt\profiles.yml`). Confirm the project ID and dataset location match your BigQuery setup. Authenticate using your organization’s approved Google Cloud method, then run from the `dbt/` directory:

```bash
dbt debug
dbt build
```



After the dbt models build successfully, open `sql/02_customer_metrics.sql`. Confirm the project and dataset names, then run the statements in order. The resulting `rfm`, `attribution`, and `campaign_performance` tables are based on the synthetic funnel.

### 6. Build Tableau views

Connect Tableau to the BigQuery tables `customer360.rfm`, `customer360.attribution`, and `customer360.campaign_performance`, or export those tables to CSV. Follow `tableau/dashboard_guide.md` for customer value, campaign performance, and attribution-path dashboard designs. A Tableau workbook is not included.

## Metric definitions and assumptions

- **RFM:** Recency, frequency, and monetary value are divided into quintile scores. Recency is scored so more recent activity receives the better score.
- **Illustrative CLV:** Observed synthetic revenue over the generated 12-month period, expressed as a simple historical customer-value proxy. It is not a predictive model.
- **Attribution window:** Synthetic campaign touches during the 30 days before each order.
- **Time decay:** Exponential decay with a 7-day half-life.
- **ROI:** `(attributed revenue - ad spend) / ad spend`.
- **CAC:** `ad spend / distinct customers with attributed orders`.



## Implementation status

| Component | Status |
|---|---|
| Synthetic CSVs | Generated and included |
| GA4 exploration SQL | Prepared; not run in this environment |
| BigQuery upload and table checks | Instructions and SQL prepared; not run |
| dbt models | Prepared; not built or validated against BigQuery |
| Customer and campaign metrics | SQL prepared;|
| Tableau dashboard specification | Prepared;|


# DA-03 | Retail & Supply Chain Control Tower++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

**Data Analytics specialization · Project ------------------------------------------------------------------------------------

This project is a  explainable control tower prototype. It turns inventory, demand, shipment, and supplier feeds into a few practical questions: What could run out? Which deliveries are late? Is demand behaving unusually? Would a one-week delay leave enough stock?

The included feeds are **synthetic** and created for this portfolio demo. No live retailer, supplier, shipment, or customer records are used. The hourly Airflow schedule, PostgreSQL service, Prophet forecast, Superset dashboard, and alert delivery are implementation assets; they are not represented as deployed services.

## At a glance

- 12 fictional products across 4 fictional suppliers.
- 36 inventory rows across 3 warehouses in one snapshot.
- 1,080 daily demand rows across 90 days.
- 48 synthetic shipments with planned and actual delivery dates.
- PostgreSQL schema and KPI views, an hourly Airflow DAG, a demand anomaly check, an optional Prophet forecast, a 7-day delay scenario, and a Superset dashboard plan.
- A CSV validation run passed 9 checks. The generated demonstration snapshot has a 5.6% stock-out rate and 35.4% supplier on-time rate; these are synthetic scenario values, not real operational benchmarks.

## Architecture

```mermaid
flowchart LR
  GEN[Seeded synthetic feed generator] --> CSV[CSV source feeds]
  CSV --> PG[(PostgreSQL control_tower schema)]
  DAG[Airflow hourly DAG] -->|upsert refreshed CSV feed| PG
  PG --> KPI[Inventory, supplier and anomaly KPI views]
  DEM[Prophet 14-day forecast] --> COMP[Compare forecast with available stock]
  KPI --> COMP
  KPI --> SB[Superset dashboard]
  COMP --> SB
  KPI --> ALERT[Threshold and anomaly alerts]
```

For this demo, the feeds are files rather than live supplier APIs. Airflow shows how an hourly refresh would be scheduled; it re-reads and upserts the current synthetic CSVs. A live retailer would replace that input with authenticated source integrations and operational monitoring.

## What is included

| Folder | Contents |
|---|---|
| `data/` | Generated synthetic CSV feeds |
| `Retail_Supply_Chain_Control_Tower_Data.xlsx` | One workbook with an overview, the five data tables on filterable sheets, and a data dictionary |
| `python/` | PowerShell CSV generator, CSV KPI/anomaly summary, optional Prophet forecast, requirements |
| `sql/` | PostgreSQL tables, KPI views, anomaly view, 7-day delay what-if, CSV import script |
| `airflow/dags/` | Hourly refresh DAG using a PostgreSQL Airflow connection |
| `superset/` | Dashboard and connection setup guidance |
| `alerts/` | Console alert checker and optional Slack webhook behavior |
| `tests/` | CSV contract tests and the PowerShell validation used for the included data |
| `docs/` | Data dictionary, demo walkthrough, and reflection template |

## Data inventory

| File | Rows | Main fields |
|---|---:|---|
| `data/products.csv` | 12 | SKU, supplier, unit cost, reorder point |
| `data/suppliers.csv` | 4 | Supplier, region |
| `data/inventory_snapshot.csv` | 36 | Snapshot time, SKU, warehouse, on-hand, allocated |
| `data/daily_demand.csv` | 1,080 | Date, SKU, units sold |
| `data/shipments.csv` | 48 | Supplier, SKU, order/promised/actual dates, units |

Every CSV row includes `synthetic_record=true`. The generator uses a fixed seed, making the demo scenario reproducible. It deliberately seeds several low-stock and demand-spike cases so the control-tower checks have something to flag.

The Excel workbook is a convenient combined copy for review. The CSV files remain the source inputs used by the SQL and pipeline examples.

## Run it locally

 The commands below can be run from PowerShell or another terminal. Start in this project directory.

### 1. Generate the CSV feeds

Windows PowerShell requires no extra packages for generation:

```powershell
powershell -ExecutionPolicy Bypass -File python/generate_data.ps1
```

The generated files appear in `data/`. The same structure can be generated with a Python implementation in a later extension; the included reliable Windows generator is PowerShell.

### 2. Validate the generated data

```powershell
powershell -ExecutionPolicy Bypass -File tests/validate_csvs.ps1
```

Expected checks: 12 products, 4 suppliers, 36 inventory rows, 1,080 demand rows, 48 shipments, synthetic labels on every row, and valid product/supplier relationships. The checks run on the included CSV files.

### 3. Start PostgreSQL and load the CSVs

Install Docker Desktop, then copy the sample local settings file and change its password:

```powershell
Copy-Item .env.example .env
```

Start the database from this folder:

```powershell
docker compose up -d
```

On the first start, the container creates the schema and imports the mounted CSV files. The example is local-only. `.env` is ignored by Git; never commit a real password. To inspect the database:

```powershell
docker compose exec postgres psql -U controltower -d controltower -c "SELECT * FROM control_tower.v_supplier_kpis;"
docker compose exec postgres psql -U controltower -d controltower -c "SELECT * FROM control_tower.v_inventory_kpis LIMIT 10;"
```

PostgreSQL runs on port 5432 by default. The init scripts run only when the database volume is first created. For a changed CSV snapshot, use a safe upsert refresh such as the Airflow DAG or re-import into a fresh disposable local database.

### 4. Review KPIs and anomalies without a database

If Python is installed, run:

```powershell
python python/analyze.py
python alerts/check_alerts.py
```

The analysis reports the synthetic snapshot’s stock-out and on-time delivery rates, then flags daily demand values whose absolute z-score is at least 2.5. The alert checker prints low-stock and demand-anomaly messages. It sends nothing unless `SLACK_WEBHOOK_URL` is explicitly configured in the environment.

### 5. Optional demand forecast

Install the packages from `python/requirements.txt` in an isolated Python environment, then run:

```powershell
python python/forecast_prophet.py
```

The script holds out the latest 14 historical days for per-SKU Prophet MAE/WAPE comparison with a simple mean-demand baseline, then refits on the full history and forecasts the next 14 days. It writes `data/forecast_holdout_metrics.csv` and `data/demand_forecast_14d.csv`, including a simple comparison of forecast demand with current available inventory. Forecast output is synthetic and should not be treated as a validated purchasing recommendation. The Prophet script was not run in the authoring environment because Python and its dependencies were unavailable.

### 6. Airflow hourly refresh

`airflow/dags/control_tower_hourly.py` defines a DAG scheduled at the top of every hour. To run it in Airflow, install the matching Apache Airflow Postgres provider, make the project `data/` files available at the DAG’s expected relative path, and configure an Airflow connection with ID `control_tower_pg` for the local PostgreSQL database. The DAG upserts the CSV feed into the schema. Airflow itself was not installed or deployed for this portfolio build.

### 7. Superset dashboard and what-if

Connect Apache Superset to the PostgreSQL database and use `superset/dashboard_guide.md` to build the KPI cards, warehouse stock chart, supplier performance table, demand-anomaly chart, and 7-day delay what-if view. The `control_tower.v_delay_7d_whatif` view subtracts seven days of average demand from available units and flags projected stock-outs. No Superset instance or dashboard export is included.

## KPI definitions

- **Stock-out rate:** inventory SKU/warehouse rows with available units at or below zero divided by all snapshot rows. Available units = `max(on_hand − allocated, 0)`.
- **On-time delivery:** delivered shipments where actual delivery date is on or before the promised date, divided by delivered shipments.
- **Supplier lead time:** average calendar days from order date to actual delivery date.
- **Days of inventory:** available units divided by average daily demand from the recent 30-day window. Null where demand is zero.
- **Demand anomaly:** absolute z-score of daily SKU demand at least 2.5.
- **7-day delay scenario:** current available units minus seven times the average daily demand; flags when the result is at or below zero.

These definitions are intentionally simple and should be reviewed with business owners before use in a real operation.

## Validation results

The included `tests/validate_csvs.ps1` was run against the generated CSVs:

| Check | Result |
|---|---|
| Expected row counts across all five CSVs | PASS |
| Every source row marked synthetic | PASS |
| Product-to-supplier references | PASS |
| Inventory and demand SKU references | PASS |
| Shipment SKU and supplier references | PASS |
| Snapshot stock-out rate | 5.6% (synthetic) |
| Shipment on-time rate | 35.4% (synthetic) |




REFERENCES
- [Apache Airflow DAGs](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/dags.html)
- [PostgreSQL documentation](https://www.postgresql.org/docs/)
- [Prophet documentation](https://facebook.github.io/prophet/)
- [Apache Superset documentation](https://superset.apache.org/docs/intro)
