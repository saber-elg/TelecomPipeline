# System Architecture

## Overview

The Telecom CDR Processing Pipeline is designed as a multi-stage data engineering system that processes Call Detail Records through various transformation layers.

## Data Flow Diagram

```
┌──────────────────────────────────────────────────────────────────┐
│                      DATA GENERATION LAYER                        │
└──────────────────────────────────────────────────────────────────┘
                                 │
                     ┌───────────▼───────────┐
                     │  DataGeneration.py    │
                     │  - Generates CDRs     │
                     │  - Faker integration  │
                     │  - PostgreSQL lookup  │
                     └───────────┬───────────┘
                                 │
┌──────────────────────────────────────────────────────────────────┐
│                       STREAMING LAYER                             │
└──────────────────────────────────────────────────────────────────┘
                                 │
                     ┌───────────▼───────────┐
                     │    Producer.py        │
                     │  - Kafka Producer     │
                     │  - JSON serialization │
                     │  - Idempotent writes  │
                     └───────────┬───────────┘
                                 │
                     ┌───────────▼───────────┐
                     │  Kafka Topic: telecom │
                     │  - 3 Partitions       │
                     │  - Replication: 1     │
                     └───────────┬───────────┘
                                 │
┌──────────────────────────────────────────────────────────────────┐
│                      MEDIATION LAYER                              │
└──────────────────────────────────────────────────────────────────┘
                                 │
                     ┌───────────▼───────────┐
                     │    Mediation.py       │
                     │  - Kafka Consumer     │
                     │  - PySpark Processing │
                     │  - Data Validation    │
                     │  - Cleansing Rules    │
                     └───────────┬───────────┘
                                 │
                     ┌───────────▼───────────┐
                     │   mediated_data/      │
                     │  ├─ voice/            │
                     │  ├─ sms/              │
                     │  └─ data/             │
                     │  (Parquet, Partitioned)│
                     └───────────┬───────────┘
                                 │
┌──────────────────────────────────────────────────────────────────┐
│                       RATING LAYER                                │
└──────────────────────────────────────────────────────────────────┘
                                 │
                     ┌───────────▼───────────┐
                     │  RatingEngine.ipynb   │
                     │  - Load mediated data │
                     │  - Join catalog (JDBC)│
                     │  - Price calculation  │
                     │  - Discount logic     │
                     └───────────┬───────────┘
                                 │
                     ┌───────────▼───────────┐
                     │    rated_data/        │
                     │  (Parquet, by period) │
                     └───────────┬───────────┘
                                 │
┌──────────────────────────────────────────────────────────────────┐
│                       BILLING LAYER                               │
└──────────────────────────────────────────────────────────────────┘
                                 │
                     ┌───────────▼───────────┐
                     │ BillingSystem.ipynb   │
                     │  - Aggregate by cust. │
                     │  - Monthly totals     │
                     │  - Tax calculation    │
                     │  - Invoice generation │
                     └───────────┬───────────┘
                                 │
                     ┌───────────▼───────────┐
                     │      invoice/         │
                     │  (Parquet, by period) │
                     └───────────┬───────────┘
                                 │
┌──────────────────────────────────────────────────────────────────┐
│                      REPORTING LAYER                              │
└──────────────────────────────────────────────────────────────────┘
                                 │
                     ┌───────────▼───────────┐
                     │   Reporting.ipynb     │
                     │  - Revenue analysis   │
                     │  - Customer ranking   │
                     │  - Regional reports   │
                     │  - Product analytics  │
                     └───────────┬───────────┘
                                 │
                     ┌───────────▼───────────┐
                     │       report/         │
                     │  - CSV exports        │
                     │  - Business metrics   │
                     └───────────────────────┘
```

## Component Details

### 1. Data Generation Layer

**Purpose**: Generate realistic telecom CDRs for testing and simulation.

**Components**:
- `DataGeneration.py`: Core generation logic
- PostgreSQL: Customer subscription data source

**Features**:
- Moroccan phone number patterns (212 prefix)
- International number generation
- Random data quality issues (nulls, invalid formats)
- Realistic call durations, data usage

### 2. Streaming Layer

**Purpose**: Real-time event streaming and buffering.

**Components**:
- `Producer.py`: Kafka producer application
- Kafka Broker: Message queue

**Configuration**:
- Topic: `telecom`
- Partitions: 3
- Compression: Snappy
- Idempotence: Enabled

### 3. Mediation Layer

**Purpose**: Data cleansing, validation, and standardization.

**Components**:
- `Mediation.py`: PySpark streaming consumer

**Processing Steps**:
1. **Consume** from Kafka
2. **Validate** phone numbers
3. **Clean** null values
4. **Normalize** technology codes
5. **Partition** by record type
6. **Write** to Parquet

**Data Quality Rules**:
- Caller ID must be valid
- Duration > 0 for voice
- Technology mapping (LTE→4G)
- International number detection

### 4. Rating Layer

**Purpose**: Apply pricing based on product catalog.

**Components**:
- `RatingEngine.ipynb`: Jupyter notebook
- PostgreSQL: Product catalog

**Logic**:
1. Read mediated data
2. Join with product catalog (JDBC)
3. Detect international calls
4. Calculate base price
5. Apply discounts
6. Assign billing period

### 5. Billing Layer

**Purpose**: Generate customer invoices.

**Components**:
- `BillingSystem.ipynb`: Jupyter notebook

**Aggregations**:
- Group by customer + billing period
- Sum charges by type (voice/sms/data)
- Calculate tax (20%)
- Generate final invoice amount

### 6. Reporting Layer

**Purpose**: Business analytics and insights.

**Components**:
- `Reporting.ipynb`: Jupyter notebook

**Reports**:
- Revenue by subscription plan
- Top customers by spending
- Regional distribution
- Product cost analysis

## Technology Justification

| Technology | Reason |
|------------|---------|
| **Kafka** | High-throughput streaming, fault-tolerant, scalable |
| **PySpark** | Distributed processing, handles large datasets, DataFrame API |
| **PostgreSQL** | ACID compliance for reference data, JDBC integration |
| **Parquet** | Columnar format, compression, schema evolution |
| **Python** | Rich ecosystem, data science libraries, readability |

## Scalability Considerations

### Current Limitations (v0.1)
- Single Kafka broker
- No replication
- Local file system storage
- No monitoring/alerting

### Production Enhancements
- Multi-broker Kafka cluster
- Replication factor: 3
- Distributed storage (HDFS/S3)
- Spark cluster (YARN/K8s)
- Prometheus/Grafana monitoring
- ELK stack for logging

## Data Retention

| Layer | Retention | Format |
|-------|-----------|--------|
| Raw CDR (Kafka) | 7 days | JSON |
| Mediated | 90 days | Parquet |
| Rated | 1 year | Parquet |
| Invoices | 7 years | Parquet |
| Reports | Permanent | CSV |

## Security Considerations

- Database credentials should be externalized (env vars)
- Kafka ACLs for topic access control
- Encryption in transit (SSL/TLS)
- Encryption at rest for sensitive data
- Data masking for PII in reports

## Performance Tuning

### Spark Configuration
```python
spark.sql.shuffle.partitions = 200
spark.executor.memory = 4g
spark.driver.memory = 2g
spark.default.parallelism = 100
```

### Kafka Configuration
```python
batch.size = 32768
linger.ms = 5
compression.type = snappy
```

## Error Handling Strategy

1. **Validation Errors**: Quarantine invalid records
2. **Processing Errors**: Retry with exponential backoff
3. **System Errors**: Alert and fail-safe
4. **Data Quality**: Log anomalies for review

---

Last Updated: January 2025
Version: 0.1
