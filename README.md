# NYC Yellow Taxi End-to-End Data Pipeline (Medallion Architecture)

An end-to-end distributed data engineering pipeline built using **PySpark** and **Databricks** on **NYC TLC Yellow Taxi Trip Record Data (Jan–Mar 2025)** following the Medallion Architecture pattern.

---

## Architecture & Flow

1. **Storage Layer (Unity Catalog Volume):**
   - Source data stored as Parquet files under `taxi_catalog.bronze.raw_files`.
2. **Bronze Layer (Raw Ingestion):**
   - Schema contract validation.
   - Enriched with ingestion metadata (`_metadata.file_name`, `ingestion_timestamp`).
   - Persisted as Delta Lake table: `taxi_catalog.bronze.yellow_taxi_raw`.
3. **Silver Layer (Standardization & Cleaning):**
   - Filtered null pickup/drop-off timestamps, non-negative trip distances, and valid fares.
   - Derived analytical features: `trip_duration_minutes` (using `unix_timestamp`), `pickup_date`, `pickup_year`, `pickup_month`, and `pickup_hour`.
   - Persisted as Delta Lake table: `taxi_catalog.silver.yellow_taxi_clean`.
4. **Gold Layer (Business Aggregations):**
   - `taxi_catalog.gold.monthly_taxi_metrics`: Aggregated monthly trips, total revenue, average distances, and durations.
   - `taxi_catalog.gold.payment_metrics`: Revenue and trip distribution by payment types.
   - `taxi_catalog.gold.top_pickup_locations`: Top 10 high-demand pickup zones.

---

## Orchestration & CI/CD
- **Databricks Workflows:** Linear DAG pipeline orchestrating tasks (`01_Bronze_Ingestion` → `02_Silver_Standardization` → `03_Gold_Aggregation`) executed via Serverless compute.
- **Git Integration:** Managed with Git version control directly linked to this repository.

---

## Tech Stack
- **Platform:** Databricks (Serverless Compute, Unity Catalog)
- **Engine:** Apache Spark / PySpark
- **Storage & Formats:** Delta Lake, Parquet
- **Orchestration:** Databricks Workflows (Jobs)
- **Language:** Python, SQL
