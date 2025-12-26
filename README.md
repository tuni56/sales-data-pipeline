# AWS-Ready Lakehouse Data Pipeline for Multi-Source Sales Analytics

![Python](https://img.shields.io/badge/python-3.9+-blue)
![PySpark](https://img.shields.io/badge/pyspark-batch-orange)
![Airflow](https://img.shields.io/badge/airflow-orchestration-green)
![AWS Ready](https://img.shields.io/badge/aws-ready-yellow)
![Delta Lake](https://img.shields.io/badge/delta-lakehouse-purple)
![Data Quality](https://img.shields.io/badge/data%20quality-great%20expectations-red)
![Status](https://img.shields.io/badge/status-portfolio--project-informational)

Production-grade **Data Engineering pipeline** designed to ingest, validate,
transform, and expose sales data from multiple sources using a **Lakehouse
architecture**.  
Built for local execution and **designed for seamless migration to AWS-managed services**.

---

## Business Problem

E-commerce sales data arrives from multiple sources with different cadences and formats:

- Near real-time transactions via **REST APIs**
- Daily batch reports from **external partners (CSV files)**

Without a unified pipeline:

- Analysts spend excessive time cleaning and reconciling data  
- Business decisions are delayed or based on inconsistent metrics  
- Data quality issues propagate to dashboards and ML models  

This project provides a **scalable, reliable, and AWS-ready data pipeline** that
standardizes ingestion, enforces data quality, and delivers analytics-ready datasets
for BI and downstream machine learning use cases.

---

## Architecture Overview

The pipeline follows a **Lakehouse architecture** implemented on object storage
and organized into three logical layers:

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
  Enables horizontal scalability and direct compatibility with AWS EMR and AWS Glue.

- **Delta Lake format**  
  Provides ACID guarantees, schema evolution, time travel, and safe reprocessing.

- **Configuration-driven pipelines**  
  No hardcoded paths, environments, or credentials.

- **Data quality as a first-class concern**  
  Validations are enforced before data is promoted to the next layer.

- **AWS-first design**  
  Every component maps directly to managed AWS services.

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
├── infrastructure/ # Docker and future IaC
└── docs/ # Architecture docs and runbooks


---

## Orchestration

Batch pipelines are orchestrated using **Apache Airflow** with:

- Daily scheduling  
- Explicit task dependencies (Bronze → Silver → Gold)  
- Retry policies and SLA definitions  
- Environment-based configuration  

The same DAGs are designed to run locally or on **Amazon MWAA**.

---

## Data Quality

Data quality checks are implemented using **Great Expectations** and include:

- Schema validation  
- Null and completeness checks  
- Business rule validations (ranges, IDs, referential integrity)  

Failures stop the pipeline and prevent downstream data contamination.

---

## AWS Deployment Mapping

| Layer / Concern | AWS Service |
|-----------------|------------|
| Storage         | Amazon S3 (Bronze / Silver / Gold zones) |
| Processing      | AWS Glue or Amazon EMR (Spark) |
| Orchestration   | Amazon MWAA (Airflow) |
| Query Layer     | Amazon Athena / Redshift Spectrum |
| Monitoring      | Amazon CloudWatch |
| Data Quality    | Great Expectations |
| Security        | IAM, KMS, Lake Formation |

---

## Local Development

```bash
# Start local services
docker-compose up -d

# Setup local environment
make setup-local

# Run full batch pipeline
make run-pipeline ENV=local
---

Failure Handling & Reprocessing

Source unavailability → retries with exponential backoff

Schema breaking changes → quarantine + validation failure

Late-arriving or duplicate data → handled via Delta Lake MERGE

Historical reprocessing → controlled backfills using partition-based reruns
---

Future Improvements

Event-driven ingestion using S3 events and EventBridge

Streaming ingestion via Kinesis or MSK

ML feature store integration

Infrastructure as Code using Terraform

Multi-account and multi-region deployment
