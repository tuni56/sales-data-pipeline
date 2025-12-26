# Core Lakehouse Data Pipeline for Multi-Source Sales Analytics

![Python](https://img.shields.io/badge/python-3.9+-blue)
![PySpark](https://img.shields.io/badge/pyspark-batch-orange)
![Airflow](https://img.shields.io/badge/airflow-orchestration-green)
![Delta Lake](https://img.shields.io/badge/delta-lakehouse-purple)
![Data Quality](https://img.shields.io/badge/data%20quality-great%20expectations-red)
![Status](https://img.shields.io/badge/status-portfolio--project-informational)

Production-grade **Data Engineering pipeline** designed to ingest, validate,
transform, and expose sales data from multiple sources using a **Lakehouse
architecture**.

This project focuses on **core Data Engineering fundamentals**, intentionally
decoupled from any specific cloud provider. It is designed to run locally while
demonstrating patterns and practices that translate directly to managed cloud
services.

---

## Scope and Focus

This repository intentionally focuses on foundational Data Engineering concepts:

- Spark-based batch processing
- Lakehouse architecture (Bronze / Silver / Gold)
- Idempotent and reprocessable pipelines
- Data quality enforcement as a gate between layers
- Clear separation of concerns and production-oriented structure

Cloud-specific implementations (e.g. AWS Glue, MWAA, IAM) are intentionally kept
out of this repository to avoid coupling core concepts with managed services.
A separate repository demonstrates the AWS Glue adaptation of this pipeline.

---

## Business Problem

E-commerce sales data arrives from multiple sources with different cadences and formats:

- Near real-time transactions via **REST APIs**
- Daily batch reports from **external partners (CSV files)**

Without a unified pipeline:

- Analysts spend excessive time cleaning and reconciling data  
- Business decisions are delayed or based on inconsistent metrics  
- Data quality issues propagate to dashboards and ML models  

This project provides a **scalable and reliable batch data pipeline** that
standardizes ingestion, enforces data quality, and delivers analytics-ready
datasets for BI and downstream machine learning use cases.

---

## Architecture Overview

The pipeline follows a **Lakehouse architecture** organized into three logical layers:

- **Bronze Layer**  
  Raw, immutable data ingestion from APIs and CSV files.  
  Append-only, schema preserved, ingestion metadata added.

- **Silver Layer**  
  Cleaned, standardized, and validated datasets.  
  Deduplication, schema enforcement, and data quality checks applied.

- **Gold Layer**  
  Business-oriented aggregations optimized for analytics and BI consumption.

**Design principles:**
- Idempotent batch processing  
- Schema evolution support  
- Explicit data quality gates between layers  

---

## Data Flow

Sources (API / CSV)
│
▼
Bronze Layer (Raw, Immutable)
│
▼
Silver Layer (Clean + Validated)
│
▼
Gold Layer (Analytics-Ready)


Data promotion between layers is blocked if **data quality validations fail**.

---

## Key Engineering Decisions

- **PySpark over Pandas**  
  Enables scalable processing and reflects industry-standard batch data workloads.

- **Delta Lake format**  
  Provides ACID guarantees, schema evolution, time travel, and safe reprocessing.

- **Configuration-driven pipelines**  
  No hardcoded paths, environments, or credentials.

- **Data quality as a first-class concern**  
  Validations are enforced before data is promoted to the next layer.

- **Cloud-agnostic design**  
  The architecture and code structure map cleanly to managed services without
  embedding provider-specific logic.

---

## Repository Structure

sales-data-pipeline/
├── src/
│ ├── ingestion/ # Bronze ingestion jobs (API / CSV)
│ ├── processing/ # Silver and Gold Spark jobs
│ ├── validation/ # Great Expectations suites
│ └── utils/ # Spark, config, logging utilities
├── dags/ # Airflow DAG definitions
├── config/ # Environment-specific YAML configs
├── tests/ # Unit and integration tests
├── infrastructure/ # Docker and local setup
└── docs/ # Architecture docs and runbooks


---

## Orchestration

Batch pipelines are orchestrated using **Apache Airflow** with:

- Daily scheduling  
- Explicit task dependencies (Bronze → Silver → Gold)  
- Retry policies and failure handling  
- Environment-based configuration  

Airflow is used locally to demonstrate orchestration concepts independently of
any managed service.

---

## Data Quality

Data quality checks are implemented using **Great Expectations** and include:

- Schema validation  
- Null and completeness checks  
- Business rule validations (ranges, IDs, referential integrity)  

Failures stop the pipeline and prevent downstream data contamination.

---

## Local Development

```bash
# Start local services
docker-compose up -d

# Setup local environment
make setup-local

# Run full batch pipeline
make run-pipeline ENV=local
