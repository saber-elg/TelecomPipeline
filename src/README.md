# Source Code Modules

This directory contains the core Python modules for the CDR processing pipeline.

## Files

### DataGeneration.py
Generates realistic Call Detail Records (CDRs) for testing and simulation.

**Features**:
- Moroccan phone number patterns
- International number generation
- Random data quality issues (nulls, malformed data)
- Integration with PostgreSQL customer database

### Producer.py
Kafka producer that streams generated CDRs to the message broker.

**Features**:
- High-throughput message production
- Idempotent writes
- Snappy compression
- Delivery confirmations

### Mediation.py
PySpark-based mediation layer for data cleansing and validation.

**Features**:
- Kafka consumer integration
- Phone number validation
- Null handling and data normalization
- Partitioning by record type
- Technology code standardization (LTE→4G, NR→5G)

## Usage

```bash
# Generate and stream CDRs
python src/Producer.py

# Process and clean CDRs
python src/Mediation.py
```

## Dependencies

See `requirements.txt` for all required Python packages.
