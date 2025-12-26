
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
