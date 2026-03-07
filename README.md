# Audit Analytics – Financial Anomaly Detection

## Overview

This project analyzes credit card transaction data to detect financial anomalies, compute audit KPIs, and support risk monitoring through an interactive Power BI dashboard. The pipeline covers data cleaning, exploratory analysis, fraud profiling, and statistical anomaly detection — with clean outputs exported for Power BI reporting.

---

## Project Structure

```
├── audit analysis.ipynb        # Full analysis pipeline (EDA → KPIs → Anomaly Detection → Export)
├── Audit_financiere.pbix       # Power BI dashboard for risk monitoring
└── data/
    ├── credit_card_transactions.csv   # Raw transaction dataset (not tracked in Git)
    ├── clean_transactions.csv         # Cleaned dataset (output)
    ├── monthly_summary.csv            # Monthly aggregated KPIs (output)
    └── anomalies.csv                  # Flagged anomalous transactions (output)
```

---

## Dataset

- **File:** `credit_card_transactions.csv`
- **Domain:** Credit card transactions with fraud labels
- **Key columns:**

| Column | Description |
|--------|-------------|
| `trans_date_trans_time` | Transaction timestamp |
| `amt` | Transaction amount |
| `category` | Merchant category |
| `state` | Geographic state of transaction |
| `is_fraud` | Fraud label (1 = fraud, 0 = legitimate) |
| `dob` | Customer date of birth |
| `merch_zipcode` | Merchant zip code |

---

## Pipeline

### 1. Data Loading & Inspection
- Loaded the raw CSV and performed a rapid inspection (`info()`, `describe()`).
- Dropped the unnecessary index column (`Unnamed: 0`).
- Parsed `trans_date_trans_time` and `dob` as datetime columns.

### 2. Data Cleaning
- Checked for missing values and duplicates.
- Handled missing `merch_zipcode` values by filling with `-1` and casting to integer.

### 3. Global KPI Computation
Computed the following audit-level KPIs:

| KPI | Description |
|-----|-------------|
| Total Transactions | Count of all transactions |
| Total Amount | Sum of all transaction amounts |
| Average Transaction Amount | Mean transaction value |
| Fraud Rate | % of transactions labeled as fraud |

### 4. Category & Geographic Analysis
- Grouped transactions by **merchant category** to identify high-volume and high-value segments.
- Grouped by **state** to flag geographic concentration of transactions.

### 5. Fraud Analysis
- Isolated fraudulent transactions (`is_fraud == 1`).
- Analyzed fraud distribution by merchant category: count, total amount, and average amount per category.

### 6. Anomaly Detection (Z-Score)
- Computed the **Z-score** of transaction amounts:
  ```
  z = (amount - mean) / std
  ```
- Flagged transactions with `|z| > 3` as statistical anomalies.
- Analyzed top anomalies by amount and breakdown by merchant category.

### 7. Power BI Export
Generated three clean CSV exports for Power BI consumption:

- **`clean_transactions.csv`** — fully cleaned transaction-level data
- **`monthly_summary.csv`** — monthly aggregations: total amount, average amount, fraud count, transaction count
- **`anomalies.csv`** — all flagged anomalous transactions with their Z-scores

---

## Power BI Dashboard (`Audit_financiere.pbix`)

The `.pbix` file connects to the exported CSVs and provides an interactive risk monitoring dashboard including:

- Monthly transaction volume and fraud trend
- Fraud rate by merchant category
- Geographic distribution of transactions by state
- Anomaly highlights with transaction-level drill-down

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Python 3 | Core analysis language |
| Pandas | Data manipulation, grouping, resampling |
| NumPy | Z-score computation |
| Power BI + DAX | Interactive risk monitoring dashboard |

---

## Setup

```bash
# Clone the repository
git clone https://github.com/Ayoub-Elkhaiari/Audit_Financial.git
cd Audit_Financial

# Install dependencies
pip install pandas numpy jupyter

# Run the notebook
jupyter notebook "audit analysis.ipynb"
```

> **Note:** The raw dataset `credit_card_transactions.csv` is not tracked in Git.  
> Place it in the `data/` folder before running the notebook. The three output CSVs will be generated automatically.
>
> To explore the dashboard, open `Audit_financiere.pbix` in **Power BI Desktop** and refresh the data source paths to point to your local `data/` folder.