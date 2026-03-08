# Data Cleaning Pipeline — Python & SQL
### End-to-end data cleaning of a 969,000-row e-commerce orders dataset

---

## Overview

This project demonstrates a production-grade data cleaning pipeline built in both **Python (pandas)** and **universal SQL**, applied to a realistic dirty e-commerce orders dataset with 969,000 rows and 14 distinct types of data quality issues.

The pipeline covers the full data cleaning lifecycle:
- Identifying and resolving data quality issues
- Preserving raw data untouched
- Producing an upload-ready clean dataset
- Generating a complete audit trail of every change made
- Flagging records requiring human review in a separate issues report

---

## Dataset

| | Raw Dataset | After Cleaning |
|---|---|---|
| **Total Rows** | 969,000 | 950,000 |
| **Duplicates** | 19,000 | 0 |
| **Columns** | 10 | 10 |
| **Date Range** | 2020–2024 | 2020–2024 |

**Columns:** `order_id`, `customer_name`, `email`, `age`, `country`, `order_date`, `delivery_date`, `product`, `quantity`, `price`


> 
> **Full dataset, cleaned output + audit report:** [Download from Google Drive](https://drive.google.com/drive/folders/1evXv2FKWOjpYJGl8g0VSvlokG_NGGJjz?usp=sharing)

A 15-row sample is available in `/data/` for quick review without downloading the full dataset.

---

## Data Quality Issues Found & Resolved

| Issue Type | Count | Action Taken |
|---|---|---|
| Exact duplicate records | ~19,000 | Removed |
| Inconsistent country names (USA, U.S.A., UNITED STATES) | ~76,000 | Standardized |
| Mixed date formats (5 different formats) | ~47,500 | Standardized to YYYY-MM-DD |
| Incorrect email casing | ~14,250 | Lowercased |
| Malformed emails (double @@, missing domain) | ~17,100 | Set to NULL |
| Missing emails | ~4,834 | Set to NULL |
| Impossible ages (999, 310, negative) | ~19,950 | Set to NULL |
| Placeholder ages (N/A) | ~9,500 | Set to NULL |
| Missing ages | ~4,851 | Set to NULL |
| Incorrect name casing (ALL CAPS, all lower) | ~23,750 | Title Case applied |
| Missing customer names | ~2,897 | Set to NULL |
| Missing delivery dates | ~19,325 | Set to NULL |
| Delivery date before order date | ~4,750 | Set to NULL |
| Zero/negative/non-numeric quantities | ~21,850 | Set to NULL |
| Negative/symbol-prefixed prices ($999.99) | ~12,350 | Set to NULL |
| Missing prices | ~4,824 | Set to NULL |
| Product name casing inconsistencies | ~28,500 | Title Case applied |

---

## Project Structure

```
data-cleaning-python-sql/
│
├── README.md
│
├── data/
│   ├── sample_dirty_dataset.csv       # 15-row dirty sample for quick preview
│   └── sample_cleaned_dataset.csv     # 15-row cleaned output
│
├── scripts/
│   ├── clean_data.py                  # Python cleaning pipeline (pandas)
│   └── data_cleaning.sql              # Universal SQL cleaning script
│
└── output/
    └── cleaning_summary_report.txt    # Summary report from Python run
```

---

## Scripts

### Python — `clean_data.py`

Built with **pandas** and **numpy**. Designed to scale to millions of rows.

```bash
# Requirements
pip install pandas numpy

# Run
python scripts/clean_data.py
```

**Outputs generated:**
- `cleaned_dataset.csv` — upload-ready clean data, duplicates removed
- `raw_data_preserved.csv` — original data untouched
- `issues_report.csv` — records with NULLs requiring human review
- `changelog.csv` — every single change logged with original and corrected value
- `cleaning_summary_report.txt` — full summary of issues found and resolved

### SQL — `data_cleaning.sql`

Universal SQL script with clearly commented alternatives for each engine.

| Engine | Status |
|---|---|
| Snowflake | ✅ Active (default) |
| PostgreSQL | ✅ Commented alternative provided |
| SQL Server | ✅ Commented alternative provided |
| MySQL 8+ | ✅ Compatible |

**Key design decisions:**
- All columns loaded as `TEXT/VARCHAR` to safely handle mixed dirty types
- Date columns set to `NULL` on failure — string flags never injected into date fields
- Issues report generated as a separate table — clean dataset stays clean
- Duplicates removed using `MIN(order_id)` subquery — works on all engines

---

## Methodology

### Step-by-step cleaning process

1. **Preserve raw data** — working copy created before any changes
2. **Strip whitespace** — all fields trimmed
3. **Standardize names** — Title Case applied, blanks set to NULL
4. **Validate emails** — lowercased, malformed set to NULL
5. **Validate ages** — placeholders and impossible values (>120) set to NULL
6. **Standardize countries** — 8+ variants mapped to standard names
7. **Standardize dates** — 5 formats normalized to YYYY-MM-DD
8. **Logical consistency check** — delivery dates before order dates set to NULL
9. **Standardize products** — Title Case applied
10. **Validate quantities** — zero/negative/non-numeric set to NULL
11. **Validate prices** — negative/symbol-prefixed/missing set to NULL
12. **Remove duplicates** — exact duplicates removed, first occurrence kept
13. **Generate issues report** — all records with remaining NULLs isolated
14. **Generate changelog** — every change documented with original value

### Design principles

- **Raw data is never modified** — always preserved separately
- **Data type consistency** — flag columns never corrupt actual data fields
- **Audit trail first** — every change is logged and explainable
- **Human review over automation** — ambiguous records are flagged, not guessed

---

## Sample — Before & After

**Raw (dirty):**
```
order_id | customer_name  | email           | age | country | order_date  
1002     | jane doe       | JANEDOE@EMAIL   | 27  | USA     | 15/01/2024  
1005     |   Alice Brown  | alice@email.com | 29  | U.S.A.  | Feb 5 2024  
1006     | Bob            | bob@@email.com  | 310 | UK      | 2024-02-07  
```

**Cleaned:**
```
order_id | customer_name | email           | age  | country        | order_date  
1002     | Jane Doe      | janedoe@email   | 27   | United States  | 2024-01-15  
1005     | Alice Brown   | alice@email.com | 29   | United States  | 2024-02-05  
1006     | Bob           | NULL            | NULL | United Kingdom | 2024-02-07  
```

---

## Tools & Technologies

![Python](https://img.shields.io/badge/Python-3.10+-blue)
![pandas](https://img.shields.io/badge/pandas-2.0+-green)
![SQL](https://img.shields.io/badge/SQL-Universal-orange)
![Snowflake](https://img.shields.io/badge/Snowflake-compatible-lightblue)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-compatible-blue)
![SQL Server](https://img.shields.io/badge/SQL_Server-compatible-red)

---

## Author

**Christian Aguila**  
Data Analyst | SQL · Python · Power BI · Snowflake  
[LinkedIn](https://linkedin.com/in/christianrhomel) · [Upwork](https://www.upwork.com/freelancers/~0189d5934456df82f1?mp_source=share)
