# London Smart Meters — Big Data Pipeline & Dashboard

An end-to-end Big Data project that processes **166 million electricity readings** from thousands of London households and serves interactive analytics through a Streamlit dashboard.

## Overview

Raw half-hourly smart meter data (~11 GB) from Kaggle is transformed into a partitioned Parquet Data Lake, aggregated into SQLite for fast queries, and visualized in an interactive dashboard.

**Key analyses:**
- Daily consumption profiles by hour
- Weekday vs. weekend usage patterns
- Correlation with weather (temperature, wind, visibility)
- Heavy consumer identification
- Demographics breakdown by Acorn category

## Architecture

```
raw_data/ (CSV, ~11 GB)
    │
    ▼
main.py  ──►  DuckDB UNPIVOT + Parquet partitioned by year/month
                  │
                  ▼  (166,523,410 rows, 0 NULLs)
              london_smart_meters_full.ducklake/
                  │
                  ▼
Part_e.py ──►  dashboard.db  (SQLite — aggregated tables)
                  │
                  ▼
dashboard.py ──►  Streamlit app
```

## Tech Stack

| Tool | Purpose |
|------|---------|
| **DuckDB** | UNPIVOT, Parquet write, partitioned queries |
| **Parquet** | Columnar storage, partitioned by `year/month` |
| **SQLite** | Pre-aggregated tables for dashboard |
| **Streamlit** | Interactive web dashboard |
| **pandas** | Data manipulation |
| **matplotlib / seaborn** | Visualizations |

## Data Source

[Smart meters in London](https://www.kaggle.com/datasets/jeanmidev/smart-meters-in-london) — Kaggle  
~5,500 households · 2011–2014 · 30-minute intervals

## Getting Started

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

### 2. Download the data

Download the dataset from [Kaggle](https://www.kaggle.com/datasets/jeanmidev/smart-meters-in-london) and place the CSV files in a `raw_data/` folder.

### 3. Build the Data Lake

```bash
python main.py
```

### 4. Run quality checks (optional)

```bash
python Part_C.py
```

### 5. Build the dashboard database

```bash
python Part_e.py
```

### 6. Launch the dashboard

```bash
streamlit run dashboard.py
```

## Data Schema

| Column | Type | Description |
|--------|------|-------------|
| `LCLid` | VARCHAR | Anonymized household ID |
| `timestamp` | TIMESTAMP | Exact reading time (computed from day + half-hour slot) |
| `energy` | DOUBLE | Electricity consumption in kWh |
| `year` | INTEGER | Partition key |
| `month` | INTEGER | Partition key |

## Project Structure

```
├── main.py          # Builds the Data Lake (CSV → Parquet)
├── Part_C.py        # Data quality checks & statistics
├── Part_e.py        # ETL from Data Lake → SQLite for dashboard
├── dashboard.py     # Streamlit dashboard
├── chaks_col.py     # Helper: inspect raw CSV schema
├── requirements.txt
└── analysis_report.md
```
