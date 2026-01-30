# Quick Start Guide

## 🚀 Get Started in 5 Minutes

### Prerequisites Check
```bash
# Check Python version (need 3.8+)
python --version

# Check if Kafka is installed
kafka-topics --version

# Check PostgreSQL
psql --version
```

### Installation Steps

#### 1. Clone & Install
```bash
git clone https://github.com/yourusername/TelelecomPipeline.git
cd TelelecomPipeline
pip install -r requirements.txt
```

#### 2. Setup Database
```bash
# Create PostgreSQL database
createdb telecomdb

# Initialize tables and seed data
python scripts/InitDatabase.py
```

#### 3. Start Kafka
```bash
# Terminal 1: Start Zookeeper
zookeeper-server-start config/zookeeper.properties

# Terminal 2: Start Kafka
kafka-server-start config/server.properties

# Terminal 3: Create topic
kafka-topics --create --topic telecom \
  --bootstrap-server localhost:9092 \
  --partitions 3 --replication-factor 1
```

#### 4. Run the Pipeline

**Step 1: Generate Data**
```bash
python src/Producer.py
# Let it run for ~5 minutes to generate sample CDRs
```

**Step 2: Mediation**
```bash
python src/Mediation.py
# Processes and cleans the CDRs
```

**Step 3: Rating** (Open Jupyter)
```bash
jupyter notebook
# Navigate to notebooks/RatingEngine.ipynb
# Run all cells
```

**Step 4: Billing** (Continue in Jupyter)
```bash
# Navigate to notebooks/BillingSystem.ipynb
# Run all cells
```

**Step 5: Reporting** (Continue in Jupyter)
```bash
# Navigate to notebooks/Reporting.ipynb
# Run all cells
```

### Verify Results
```bash
# Check mediated data
ls data/mediated_data/

# Check invoices
ls data/invoice/

# Check reports
ls data/report/
```

## 🎯 Common Commands

### Check Kafka Topic
```bash
kafka-console-consumer --topic telecom \
  --bootstrap-server localhost:9092 \
  --from-beginning --max-messages 10
```

### Check Database
```bash
psql -d telecomdb -c "SELECT COUNT(*) FROM customer_subscriptions;"
psql -d telecomdb -c "SELECT * FROM product_catalog;"
```

### Monitor Spark Jobs
```bash
# Access Spark UI
# Open browser: http://localhost:4040
```

## 🐛 Troubleshooting

### "No module named 'pyspark'"
```bash
pip install pyspark
```

### "Connection refused" (Kafka)
```bash
# Verify Kafka is running
jps | grep Kafka
```

### "Connection refused" (PostgreSQL)
```bash
# Start PostgreSQL service
# Windows: Start PostgreSQL service from Services
# Linux/Mac: sudo service postgresql start
```

### Out of Memory (Spark)
```bash
export SPARK_DRIVER_MEMORY=4g
export SPARK_EXECUTOR_MEMORY=4g
```

## 📊 Expected Output Sizes

After running the full pipeline:
- **mediated_data**: ~500MB (for 1M CDRs)
- **rated_data**: ~600MB
- **invoice**: ~50MB
- **reports**: <1MB (CSV files)

## 🔄 Clean and Restart

```bash
# Remove all generated data
rm -rf data/mediated_data/*
rm -rf data/rated_data/*
rm -rf data/invoice/*
rm -rf data/report/*.csv

# Reset database
dropdb telecomdb
createdb telecomdb
python scripts/InitDatabase.py
```

## 📚 Next Steps

1. **Customize**: Modify CDR generation in `src/DataGeneration.py`
2. **Scale**: Increase Kafka partitions and Spark executors
3. **Visualize**: Add Grafana dashboards
4. **Deploy**: Containerize with Docker

## 💡 Tips

- Run Producer with nohup for background execution
- Use `screen` or `tmux` for long-running processes
- Monitor disk space when processing large datasets
- Adjust batch sizes in Producer for rate control

## 🆘 Need Help?

- Check `docs/ARCHITECTURE.md` for detailed design
- Review individual README files in each directory
- Open an issue on GitHub
- Read the main `README.md` for comprehensive docs

---

**Happy Data Engineering!** 🎉
