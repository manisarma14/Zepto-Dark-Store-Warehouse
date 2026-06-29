# Zepto Dark Store Warehouse

End-to-end CDC data warehouse for Zepto dark store operations.
Tracks SLA breaches, rider performance, and inventory health
across 300+ dark stores in real time.

---

## Business Problem

Zepto promises grocery delivery in 10 minutes.
When that promise breaks, the ops team has no visibility into which dark store failed, at which stage (picking, dispatch, or rider travel), and why it keeps happening.

This warehouse solves that by capturing every order event the moment it happens and making it available for analysis within minutes.

---

## Architecture

    PostgreSQL → Debezium → Kafka → Airflow → MinIO → dbt → DuckDB → Power BI
                                                                  ↓
                                                              Grafana

---

## Tech Stack

| Layer          | Tool                  | Why                                   |
|----------------|-----------------------|---------------------------------------|
| Source         | PostgreSQL 15         | Simulated Zepto OMS                   |
| CDC            | Debezium              | Log based CDC, zero source impact     |
| Streaming      | Apache Kafka          | Reliable event streaming              |
| Orchestration  | Apache Airflow        | DAG based pipeline orchestration      |
| Storage        | MinIO                 | Free S3 compatible local storage      |
| Transformation | dbt Core              | Version controlled SQL transformations|
| Warehouse      | DuckDB                | Free, sub second analytical queries   |
| BI             | Power BI              | CEO, Ops, Rider dashboards            |
| Monitoring     | Grafana + Prometheus  | Pipeline health monitoring            |
| Data Quality   | Great Expectations    | DQ as code, Slack alerts              |
| Lineage        | OpenLineage + Marquez | End to end data lineage               |
| CI/CD          | GitHub Actions        | Automated testing on every PR         |

---

## Quick Start

    git clone https://github.com/manisarma14/Zepto-Dark-Store-Warehouse.git
    cd Zepto-Dark-Store-Warehouse
    cp .env.example .env
    docker-compose up

---

## Project Structure

    postgres/            Source database setup and seed data
    debezium/            CDC connector configurations
    kafka/               Topic configurations
    airflow/             DAGs and orchestration logic
    minio/               Bronze, Silver, Gold storage layers
    dbt/                 Staging, intermediate, and mart models
    grafana/             Monitoring dashboards
    great_expectations/  Data quality suites
    marquez/             Data lineage configuration
    powerbi/             Dashboard screenshots
    docs/                Architecture diagrams and decisions

---

## Data Model

| Table                   | Type                  | Grain                                   |
|-------------------------|-----------------------|-----------------------------------------|
| fact_orders             | Transactional         | One row per order                       |
| fact_order_sla_timeline | Accumulating Snapshot | One row per order updated at each stage |
| fact_inventory_snapshot | Periodic Snapshot     | One row per SKU per store per 15 min    |
| fact_rider_assignments  | Transactional         | One row per rider per order             |
| dim_dark_stores         | SCD2                  | One row per store version               |
| dim_riders              | SCD2                  | One row per rider version               |
| dim_skus                | Type 1                | One row per product                     |
| dim_date                | Static                | One row per date                        |

---

## Failure Scenarios