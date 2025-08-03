# TODO.md

This document tracks development milestones for the **Mesh-terious Warehouse** project, organized by functional area.

---

## 🔧 Infrastructure Setup

* [ ] Create `docker-compose.yml` for full stack:

  * [ ] MinIO (Iceberg storage backend)
  * [ ] Airflow (DAG orchestration)
  * [ ] OpenMetadata (metadata layer)
  * [ ] Superset (BI dashboarding)
  * [ ] RabbitMQ (ingestion messaging)
  * [ ] DuckDB (analytics engine)
* [x] Create `.env` file for all services
* [ ] Provision shared network and volumes for persistence

---

## 📦 Ingestion Layer (Mock Producers)

* [x] Implement base generator class with reproducible seeded randomness
* [ ] For each warehouse (`north`, `south`, `east`, `west`):

  * Orders generators:
    * [x] `produce_orders_north.py`
    * [x] `produce_orders_south.py`
    * [x] `produce_orders_east.py`
    * [x] `produce_orders_west.py`
  * [x] `produce_returns_<region>.py`
  * [x] `produce_inventory_<region>.py`
  * [x] `produce_restocks_<region>.py`
  * [x] `produce_dispatch_logs_<region>.py`
  * [x] `produce_errors_<region>.py`
* [x] For `logistics_core` domain:

  * [x] `produce_vehicle_tracking.py`
  * [x] `produce_route_updates.py`
  * [x] `produce_delivery_sla.py`
* [x] For `ml_insights` domain:

  * [x] `produce_demand_forecast.py`
  * [x] `produce_sla_risk.py`
  * [x] `produce_stockout_risk.py`

---

## 🗂️ Data Modeling

* [ ] Create Iceberg-compatible schemas for all `fact_` and `dim_` tables (match `AGENTS.md`)
  * [x] `fact_orders`
  * [x] `fact_returns`
  * [x] `fact_inventory_movements`
  * [x] `fact_dispatch_logs`
  * [x] `fact_vehicle_routes`
  * [x] `fact_order_errors`
  * [x] `fact_forecast_demand`
  * [x] `fact_stockout_risks`
  * [x] `dim_warehouse`
  * [x] `dim_vehicle`
  * [x] `dim_product`
  * [x] `dim_route`
  * [x] `dim_employee`
  * [x] `dim_date`
  * [x] `dim_error_code`
* [x] Partitioning strategy: use `event_date` for all `fact_` tables
* [ ] Create Iceberg DDLs for DuckDB queries
  * [x] `fact_orders`
  * [x] `fact_returns`
  * [x] `fact_inventory_movements`
  * [x] `fact_dispatch_logs`
  * [x] `fact_vehicle_routes`
  * [x] `fact_order_errors`
  * [x] `fact_forecast_demand`
  * [x] `fact_stockout_risks`
  * [x] `dim_warehouse`
  * [x] `dim_vehicle`
  * [x] `dim_product`
  * [x] `dim_route`
  * [x] `dim_employee`
  * [x] `dim_date`
  * [x] `dim_error_code`
* [ ] Create YAML specs for dbt models
  * [x] `fact_returns`
  * [x] `fact_inventory_movements`
  * [x] `fact_dispatch_logs`

---

## 🔄 DAG Development (Airflow)

* [x] Create DAG bootstrap template (base class)
* [ ] DAGs per domain:

  * Ingestion DAGs:
    * [x] `ingest_orders_north.py` → from RabbitMQ to Iceberg
    * [x] `ingest_orders_south.py` → from RabbitMQ to Iceberg
    * [x] `ingest_orders_east.py` → from RabbitMQ to Iceberg
    * [x] `ingest_orders_west.py` → from RabbitMQ to Iceberg
    * [x] `ingest_returns_north.py` → from RabbitMQ to Iceberg
    * [x] `ingest_returns_south.py` → from RabbitMQ to Iceberg
    * [x] `ingest_returns_east.py` → from RabbitMQ to Iceberg
    * [x] `ingest_returns_west.py` → from RabbitMQ to Iceberg
    * [x] `ingest_dispatch_logs_north.py` → from RabbitMQ to Iceberg
    * [x] `ingest_dispatch_logs_south.py` → from RabbitMQ to Iceberg
    * [x] `ingest_dispatch_logs_east.py` → from RabbitMQ to Iceberg
    * [x] `ingest_dispatch_logs_west.py` → from RabbitMQ to Iceberg
    * [x] `ingest_inventory_<region>.py` → from RabbitMQ to Iceberg
  * [ ] `stg_<entity>.py` → transform raw to staging (via dbt)
    * [x] `stg_orders.py`
  * [ ] `fact_<entity>.py` → load final fact tables
    * [x] `fact_orders.py`
* [x] ML DAGs:

  * [x] `forecast_demand.py`
  * [x] `predict_stockout_risk.py`
* [ ] Register DAGs with SLAs + OpenMetadata integration

---

## 🧠 ML Workflow Integration

* [ ] Create Jupyter notebook for demand forecasting model (e.g., XGBoost, Prophet)
* [ ] Create Airflow task to run inference notebook or Python script
* [ ] Write outputs to `fact_stockout_risks`
* [ ] Validate predictions using Iceberg → DuckDB → Superset

---

## 📊 Superset Integration

* [ ] Add Superset service to Docker Compose
* [ ] Connect Superset to DuckDB SQL Engine
* [ ] Create starter dashboard for:

  * [ ] `fact_orders`, `fact_returns`
  * [ ] SLA heatmap from `fact_dispatch_logs`
  * [ ] Forecast risk summary from `fact_stockout_risks`

---

## 📚 Metadata and Governance (OpenMetadata)

* [ ] Bootstrap OpenMetadata with default glossary
* [ ] Ingest metadata from:

  * [ ] Iceberg tables
  * [ ] dbt models
  * [ ] Airflow DAGs
* [ ] Apply:

  * [ ] Ownership metadata
  * [ ] Domain tags
  * [ ] Glossary terms
  * [ ] Sensitivity levels (e.g., PII flags)
* [ ] Enable lineage from ingestion → DAG → dbt → Iceberg → Superset

---

## 🧪 Testing & Validation

* [ ] Add CI test for:

  * [ ] Data generator schema match
  * [ ] DAG syntax and dry-run
  * [ ] Iceberg schema compliance
* [ ] Add logging to all generator and DAG processes
* [ ] Implement unit test suite for DAG logic and data contracts

---

## 🧩 Optional Extensions

* [ ] Add Kafka-to-RabbitMQ bridge for Kafka simulation
* [ ] Add real-time anomaly detector for dispatch failures
* [ ] Add API gateway for mock orders or live interaction
* [ ] Add Airflow SLA monitoring DAG
* [ ] Support push to external Lakehouse formats (e.g., Delta, Hudi)
