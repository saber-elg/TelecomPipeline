# Utility Scripts

This directory contains setup and utility scripts for the pipeline.

## Files

### InitDatabase.py
Initializes and seeds the PostgreSQL database with reference data.

**What it does**:
1. Creates database tables:
   - `product_catalog`: Pricing for Voice, SMS, Data products
   - `subscription_plans`: Prepaid and Postpaid plan definitions
   - `customer_subscriptions`: 1,000 sample customers with plans

2. Seeds realistic data:
   - Moroccan customer names
   - Diverse subscription plans
   - Pricing tiers (national vs international)
   - Regional distribution

**Usage**:
```bash
python scripts/InitDatabase.py
```

**Configuration**:
Update database credentials before running:
```python
DB_CONFIG = {
    'host': 'localhost',
    'port': 5432,
    'dbname': 'telecomdb',
    'user': 'postgres',
    'password': 'your_password'
}
```

**Prerequisites**:
- PostgreSQL server running
- Database `telecomdb` created
- Python packages: `psycopg2-binary`, `faker`

## Adding New Scripts

Future utility scripts can be added here:
- Data backup/restore
- Performance monitoring
- Database migrations
- Cleanup routines
