# Data Directory

This directory contains all processed data outputs from the pipeline.

## Structure

```
data/
├── mediated_data/     # Cleaned and validated CDRs (Parquet, partitioned by record_type)
├── rated_data/        # Priced CDRs with product catalog info (Parquet, partitioned by billing_period)
├── invoice/           # Customer invoices (Parquet, partitioned by billing_period)
└── report/            # Business analytics reports (CSV files)
```

## Storage Format

All intermediate data is stored in **Apache Parquet** format for:
- Efficient columnar storage
- Built-in compression (Snappy)
- Schema evolution support
- Fast analytical queries

## Data Retention

- **Mediated Data**: 90 days
- **Rated Data**: 1 year
- **Invoices**: 7 years (regulatory compliance)
- **Reports**: Permanent

## Note

These directories are populated during pipeline execution. They are excluded from version control via `.gitignore`.
