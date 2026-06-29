# Zepto Dark Store Warehouse — Architecture

## Status
Work in progress. Updated as each component is built.

## High Level Architecture

    SOURCE          INGESTION       STORAGE         SERVING         VISUALIZATION
    
    PostgreSQL  →   Debezium    →   MinIO           DuckDB      →   Power BI
    (OMS)           (CDC)           Bronze          (Warehouse)     (Dashboards)
                        ↓           Silver                      →   Grafana
                    Kafka           Gold                            (Monitoring)
                        ↓               ↓
                    Airflow         dbt Core
                    (Orchestration) (Transform)

## Components

### Source Layer
- PostgreSQL 15
- Tables: orders, riders, inventory, stores
- Simulated Zepto OMS

### Ingestion Layer
- Debezium reads PostgreSQL WAL
- Kafka topics: zepto_orders, zepto_riders, 
  zepto_inventory, zepto_stores, dlq_unprocessable

### Storage Layer
- MinIO Bronze → raw CDC events
- MinIO Silver → cleaned and typed
- MinIO Gold   → business models

### Transformation Layer
- dbt staging models
- dbt intermediate models  
- dbt mart models
- dbt snapshots for SCD2

### Serving Layer
- DuckDB queries Gold Parquet files directly

### Visualization Layer
- Power BI — CEO, Ops, Rider dashboards
- Grafana  — Pipeline monitoring

### Quality and Lineage
- Great Expectations — data quality enforcement
- OpenLineage + Marquez — end to end lineage

## Detailed Diagrams
Coming as each component is built and understood.