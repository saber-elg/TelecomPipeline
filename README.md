# Telecom CDR Processing Pipeline v0.1

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python](https://img.shields.io/badge/Python-3.8%2B-blue)](https://www.python.org/)
[![PySpark](https://img.shields.io/badge/PySpark-3.x-orange)](https://spark.apache.org/)
[![Kafka](https://img.shields.io/badge/Kafka-2.x-black)](https://kafka.apache.org/)

A comprehensive end-to-end data engineering pipeline for processing Call Detail Records (CDR) in a telecom environment. This system implements real-time data streaming, mediation, rating, billing, and reporting using modern big data technologies.

## 🎯 Project Overview

This project simulates a production-grade telecom billing system that processes millions of CDRs (Voice, SMS, Data) through multiple stages:

- **Data Generation**: Realistic CDR generation with Moroccan telecom patterns
- **Real-time Streaming**: Kafka-based event streaming pipeline
- **Mediation Layer**: PySpark-powered data cleansing and validation
- **Rating Engine**: Dynamic pricing based on product catalog
- **Billing System**: Monthly invoice generation with aggregations
- **Reporting**: Business intelligence and analytics dashboards

## 🏗️ Architecture

```
┌─────────────────┐
│  CDR Generator  │ ──► Kafka Topic (telecom)
└─────────────────┘
         │
         ▼
┌─────────────────┐
│ Mediation Layer │ ──► Cleaned & Partitioned Data
│   (PySpark)     │      (Voice, SMS, Data)
└─────────────────┘
         │
         ▼
┌─────────────────┐
│  Rating Engine  │ ──► Rated CDRs with Prices
│   (PySpark)     │      (Product Catalog Join)
└─────────────────┘
         │
         ▼
┌─────────────────┐
│ Billing System  │ ──► Customer Invoices
│   (PySpark)     │      (Monthly Aggregations)
└─────────────────┘
         │
         ▼
┌─────────────────┐
│    Reporting    │ ──► Business Analytics
│   (PySpark)     │      (CSV Reports)
└─────────────────┘
```

## 📁 Project Structure

```
TelelecomPipeline/
├── src/                        # Source code modules
│   ├── DataGeneration.py       # CDR generation logic
│   ├── Producer.py             # Kafka producer
│   └── Mediation.py            # Data cleansing & validation
├── notebooks/                  # Jupyter notebooks for analysis
│   ├── RatingEngine.ipynb      # Pricing logic implementation
│   ├── BillingSystem.ipynb     # Invoice generation
│   └── Reporting.ipynb         # Analytics & visualizations
├── scripts/                    # Utility scripts
│   └── InitDatabase.py         # PostgreSQL setup & seed data
├── config/                     # Configuration files
│   └── postgresql-42.7.6.jar   # JDBC driver for Spark
├── data/                       # Data storage (output)
│   ├── mediated_data/          # Cleaned CDRs (partitioned)
│   ├── rated_data/             # Priced CDRs
│   ├── invoice/                # Customer invoices
│   └── report/                 # Business reports
├── docs/                       # Documentation
├── requirements.txt            # Python dependencies
├── .gitignore                  # Git ignore patterns
└── README.md                   # This file
```

## 🚀 Technology Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Message Broker** | Apache Kafka | Real-time event streaming |
| **Processing Engine** | Apache Spark (PySpark) | Distributed data processing |
| **Database** | PostgreSQL | Reference data storage |
| **Language** | Python 3.8+ | Core application development |
| **Data Format** | Parquet | Efficient columnar storage |
| **Notebooks** | Jupyter | Interactive analysis |

## 📋 Prerequisites

- **Python**: 3.8 or higher
- **Apache Kafka**: 2.x (with Zookeeper)
- **Apache Spark**: 3.x
- **PostgreSQL**: 12 or higher
- **Java**: JDK 8 or 11 (for Spark)

## ⚙️ Installation & Setup

### 1. Clone the Repository

```bash
git clone https://github.com/saber-elg/TelecomPipeline.git
cd TelelecomPipeline
```

### 2. Install Python Dependencies

```bash
pip install -r requirements.txt
```

### 3. Configure PostgreSQL

Update database credentials in `scripts/InitDatabase.py`:

```python
DB_CONFIG = {
    'host': 'localhost',
    'port': 5432,
    'dbname': 'telecomdb',
    'user': 'postgres',
    'password': 'your_password'
}
```

Initialize the database:

```bash
python scripts/InitDatabase.py
```

This creates:
- **Product Catalog**: Voice, SMS, Data pricing
- **Customer Subscriptions**: 1,000 sample customers with plans
- **Plan Definitions**: Prepaid/Postpaid offerings

### 4. Start Kafka Infrastructure

```bash
# Start Zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

# Start Kafka Broker
bin/kafka-server-start.sh config/server.properties

# Create Topic
bin/kafka-topics.sh --create --topic telecom --bootstrap-server localhost:9092 --partitions 3 --replication-factor 1
```

### 5. Configure Spark

Ensure `postgresql-42.7.6.jar` is in the `config/` directory for Spark-PostgreSQL connectivity.

## 🎮 Usage

### Step 1: Generate & Stream CDRs

Start the Kafka producer to generate realistic CDRs:

```bash
python src/Producer.py
```

**Output**: Streams CDRs to `telecom` Kafka topic with configurable rate.

### Step 2: Mediation (Data Cleansing)

Process and clean incoming CDRs:

```bash
python src/Mediation.py
```

**Features**:
- Phone number validation (Moroccan & international)
- Null handling and default values
- Technology normalization (LTE→4G, NR→5G)
- Data partitioning by record type
- Duplicate detection

**Output**: `data/mediated_data/` (Parquet, partitioned by record_type)

### Step 3: Rating Engine

Apply pricing from the product catalog:

```bash
jupyter notebook notebooks/RatingEngine.ipynb
```

**Features**:
- Join with PostgreSQL product catalog
- International vs. national call detection
- Dynamic price calculation
- Discount application

**Output**: `data/rated_data/` (Parquet, partitioned by billing_period)

### Step 4: Billing System

Generate monthly customer invoices:

```bash
jupyter notebook notebooks/BillingSystem.ipynb
```

**Features**:
- Aggregation by customer and billing period
- Total charges calculation (Voice + SMS + Data)
- Tax computation
- Invoice generation

**Output**: `data/invoice/` (Parquet, partitioned by billing_period)

### Step 5: Reporting & Analytics

Generate business reports:

```bash
jupyter notebook notebooks/Reporting.ipynb
```

**Reports Generated**:
- Revenue by subscription plan
- Top customers by spending
- Regional plan distribution
- Product cost analysis

**Output**: `data/report/*.csv`

## 📊 Data Schema

### CDR Structure (Raw)

```json
{
  "record_id": "uuid",
  "record_type": "voice|sms|data",
  "caller_id": "212612345678",
  "callee_id": "212698765432",
  "timestamp": "2025-06-15T14:30:00",
  "duration_sec": 120,
  "data_mb": 100.5,
  "cell_id": "CELL_001",
  "technology": "4G",
  "region": "Casablanca"
}
```

### Invoice Structure (Final)

```
customer_id | billing_period | total_voice | total_sms | total_data | total_charges | tax | final_amount
```

## 🔍 Key Features

### Data Quality & Validation
- **Phone Number Validation**: Regex-based validation for Moroccan (212) and international formats
- **Null Handling**: Smart defaults for missing cell_id, technology
- **Data Type Enforcement**: Strict schema validation
- **Duplicate Detection**: Hash-based deduplication

### Performance Optimization
- **Partitioning**: Data partitioned by record_type and billing_period
- **Compression**: Snappy compression for Parquet files
- **Batch Processing**: Configurable batch sizes for Kafka consumption
- **Caching**: Strategic DataFrame caching in Spark

### Business Logic
- **Dynamic Pricing**: Product catalog-driven pricing
- **Roaming Detection**: International call identification
- **Tax Calculation**: Configurable tax rates
- **Plan Differentiation**: Prepaid vs. Postpaid handling

## 🛠️ Configuration

### Kafka Configuration (`src/Producer.py`)

```python
conf = {
    'bootstrap.servers': 'localhost:9092',
    'acks': 'all',
    'compression.type': 'snappy',
    'enable.idempotence': True
}
```

### Spark Configuration (Notebooks)

```python
spark = SparkSession.builder \
    .appName("TelecomPipeline") \
    .config("spark.jars", "config/postgresql-42.7.6.jar") \
    .config("spark.sql.shuffle.partitions", "200") \
    .getOrCreate()
```

## 📈 Sample Outputs

### Mediation Statistics
```
Total CDRs Processed: 1,245,678
Valid Voice Records: 856,432
Valid SMS Records: 245,123
Valid Data Records: 144,123
```

### Billing Summary
```
Total Invoices Generated: 1,000
Total Revenue: $124,567.89
Average Bill: $124.57
```

## 🧪 Testing

Run the complete pipeline end-to-end:

```bash
# 1. Start Kafka
# 2. Initialize DB
python scripts/InitDatabase.py

# 3. Generate data (run for 5 minutes)
python src/Producer.py

# 4. Process mediation
python src/Mediation.py

# 5. Run notebooks in order
# RatingEngine.ipynb → BillingSystem.ipynb → Reporting.ipynb
```

## 🐛 Troubleshooting

### Kafka Connection Issues
```bash
# Verify Kafka is running
bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```

### Spark Memory Issues
```bash
# Increase driver memory
export SPARK_DRIVER_MEMORY=4g
```

### PostgreSQL Connection Errors
- Verify credentials in `InitDatabase.py`
- Check PostgreSQL service status
- Ensure JDBC driver is in `config/` directory

## 🔮 Future Enhancements (v1.0 Roadmap)

- [ ] Data lake integration (S3/Azure Blob)
- [ ] Real-time dashboard with Grafana/Kibana
- [ ] Machine learning for fraud detection
- [ ] API endpoints for external integrations
- [ ] Containerization with Docker/Kubernetes
- [ ] CI/CD pipeline with GitHub Actions
- [ ] Stream processing with Spark Structured Streaming

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👤 Author

**Mohamed-Saber Elguelta**
- LinkedIn: [El Guelta Mohamed-Saber](https://linkedin.com/in/mohamed-saber-elguelta)
- GitHub: [@saber-elg](https://github.com/saber-elg)
- Email: medsaberelguelta@gmail.com  

## 🙏 Acknowledgments

- Apache Software Foundation for Kafka and Spark
- PostgreSQL Global Development Group
- Faker library for data generation
- Telecom industry standards for CDR formats

---

**Note**: This is version 0.1 - a proof-of-concept implementation. Not intended for production use without additional security, monitoring, and error handling.
