# Configuration Files

This directory contains configuration files for the project.

## Files

### postgresql-42.7.6.jar
PostgreSQL JDBC driver for Apache Spark connectivity.

**Usage in Notebooks**:
```python
spark = SparkSession.builder \
    .appName("TelecomPipeline") \
    .config("spark.jars", "config/postgresql-42.7.6.jar") \
    .getOrCreate()
```

## Adding Configuration Files

Future configuration files should be placed here:
- Database connection properties
- Kafka configuration files
- Spark configuration templates
- Application settings (YAML/JSON)

## Security Note

**Never commit sensitive credentials to version control!**

Use environment variables or secret management tools:
```python
import os

DB_PASSWORD = os.getenv('DB_PASSWORD')
KAFKA_API_KEY = os.getenv('KAFKA_API_KEY')
```

## Example Configuration Structure

```
config/
├── postgresql-42.7.6.jar       # JDBC driver
├── application.yaml            # App settings (add to .gitignore)
├── kafka.properties            # Kafka config (add to .gitignore)
└── spark-defaults.conf         # Spark settings
```
