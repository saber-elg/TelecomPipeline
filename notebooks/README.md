# Jupyter Notebooks

This directory contains interactive Jupyter notebooks for data processing and analysis.

## Notebooks

### RatingEngine.ipynb
Applies pricing logic to mediated CDRs based on the product catalog.

**Steps**:
1. Load mediated data from Parquet
2. Connect to PostgreSQL product catalog
3. Join CDRs with pricing information
4. Detect international calls for differential pricing
5. Calculate charges with discounts
6. Write rated data partitioned by billing period

**Output**: `data/rated_data/`

### BillingSystem.ipynb
Generates monthly customer invoices by aggregating rated CDRs.

**Steps**:
1. Load rated data for billing period
2. Group by customer ID
3. Sum charges by service type (Voice, SMS, Data)
4. Calculate tax (20%)
5. Generate final invoice amounts
6. Write invoices partitioned by billing period

**Output**: `data/invoice/`

### Reporting.ipynb
Creates business intelligence reports and analytics.

**Reports Generated**:
- **Revenue by Plan**: Total revenue grouped by subscription plan
- **Top Customers**: Ranked by total spending
- **Regional Analysis**: Plan distribution by geographic region
- **Product Cost Analysis**: Average costs by product type

**Output**: `data/report/*.csv`

## Running Notebooks

```bash
# Start Jupyter
jupyter notebook

# Navigate to notebooks/ directory and open desired notebook
```

## Prerequisites

- Apache Spark configured with PostgreSQL JDBC driver
- Mediated data available in `data/mediated_data/`
- PostgreSQL database initialized with product catalog
