# Retail Lakehouse Analytics (PySpark)

This project is my attempt to build a realistic data engineering pipeline using PySpark.
The goal was to simulate how retail data is handled in production systems — from raw ingestion to business-ready datasets.

---

## What this project does

* Ingests raw retail transaction data (CSV/JSON)
* Builds a layered pipeline (Bronze → Silver → Gold)
* Cleans and joins data from multiple sources (orders, customers, products)
* Generates a few business-facing tables like daily revenue and product rankings

I tried to keep the transformations close to how things are actually written in jobs rather than notebooks.

---

## Dataset

Using a public retail dataset (~1M+ rows).

It includes:

* Orders
* Customers
* Products
* Transaction timestamps

Not perfectly clean — which helped in handling real-world issues like:

* duplicate records
* null values
* inconsistent schema
---

## Project Structure

```text
pyspark-retail-lakehouse/
│
├── README.md
├── requirements.txt
├── setup.py
├── .gitignore
├── docker-compose.yml
├── Makefile
│
├── configs/
│   ├── app_config.yaml
│   ├── logging.yaml
│   └── schema/
│       ├── orders_schema.json
│       ├── customers_schema.json
│       └── products_schema.json
│
├── data/
│   ├── raw/
│   ├── bronze/
│   ├── silver/
│   └── gold/
│
├── notebooks/
│   ├── eda.ipynb
│   ├── validation.ipynb
│   └── performance_tuning.ipynb
│
├── src/
│   ├── main.py
│   ├── ingestion/
│   │   ├── batch_loader.py
│   │   └── stream_loader.py
│   │
│   ├── transformations/
│   │   ├── bronze_to_silver.py
│   │   ├── silver_to_gold.py
│   │   └── aggregations.py
│   │
│   ├── utils/
│   │   ├── spark_session.py
│   │   ├── logger.py
│   │   └── validators.py
│   │
│   └── jobs/
│       ├── daily_sales_job.py
│       ├── churn_job.py
│       └── inventory_job.py
│
├── tests/
│   ├── test_transformations.py
│   ├── test_validations.py
│   └── test_jobs.py
│
├── dashboards/
│   └── powerbi_kpi_dashboard.pbix
│
└── docs/
    ├── architecture.md
    ├── optimization.md
    └── business_usecases.md
```
---

## Pipeline Overview

### Bronze

* Raw data ingestion
* Stored as-is (no transformations)
* Partitioned by date

### Silver

* Removed duplicates
* Handled null values
* Joined datasets (orders + customers + products)
* Basic validations

### Gold

* Aggregated tables for reporting:

  * daily revenue
  * top-selling products
  * customer activity
---

## Optimized Areas:

### 1. Joins & performance
Used broadcast joins where it made sense (small dimension tables like products).

### 2. Window functions
Used for ranking products within categories.

### 3. Incremental loads
Didn’t reprocess full data — filtered based on new dates.

### 4. Partitioning
Partitioned output tables by date to avoid full scans.
---

## Things that didn’t go perfectly

* Initial joins caused skew (fixed partially with repartition)
* Overused `.cache()` at first — had to remove it
* Some transformations could be optimized further
  
Leaving these here intentionally since they reflect real issues.
---

## How to run

```bash
pip install -r requirements.txt
python src/main.py
```

(Assumes local Spark setup)

---

## Why I built this

Most tutorials focus on small examples.
I wanted to build something slightly closer to what data pipelines look like in real projects.

---

## Next improvements (if I continue this)

* Add streaming ingestion (Kafka)
* Introduce Delta Lake instead of plain Parquet
* Add data quality checks
* Improve job orchestration

---

## Final note

This is not a production system, but it helped me understand:

* how to structure PySpark code
* how joins and partitions impact performance
* how messy real data can be
