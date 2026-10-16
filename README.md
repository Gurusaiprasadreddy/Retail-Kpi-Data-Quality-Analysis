# 📊 Retail Orders — KPI Dictionary & Data Quality Assessment

[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![Pandas](https://img.shields.io/badge/Pandas-2.0%2B-darkgreen.svg)](https://pandas.pydata.org/)
[![NumPy](https://img.shields.io/badge/NumPy-1.24%2B-lightblue.svg)](https://numpy.org/)
[![Matplotlib](https://img.shields.io/badge/Matplotlib-3.7%2B-orange.svg)](https://matplotlib.org/)
[![OpenPyXL](https://img.shields.io/badge/OpenPyXL-3.1%2B-teal.svg)](https://openpyxl.readthedocs.io/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-FF6F00.svg)](https://jupyter.org/)

> **Data Analytics & Business Intelligence Internship Project**  
> A structured end-to-end data profiling, quality validation, KPI definition, and business visualization workflow on retail orders data.

---

## 📌 Executive Summary

Data-driven decision making requires trustworthy datasets. In real-world enterprise retail environments, source data often arrives with null values, conflicting formatting, duplicated entries, and out-of-bounds metrics. Calculating executive KPIs directly on dirty data distorts revenue figures, skews demand forecasting, and impairs operational planning.

This project delivers:
1. **Automated Data Quality Profiling & Validation Engine:** Systematically screens raw transactional records across core quality dimensions (Completeness, Uniqueness, Validity, Consistency).
2. **Analysis-Ready Cleaned Dataset:** Applies explicit normalization and flags records eligible for financial and operational calculations.
3. **Formal KPI Dictionary:** Defines operational and commercial metrics with explicit calculation formulas, inclusion criteria, ownership, and refresh frequencies.
4. **Data Quality Contract:** Proposes SLAs, severity thresholds (Critical, High, Warning), and escalation workflows to govern upstream data providers.
5. **Business Dashboards & Visualizations:** Translates validated data into executive dashboards and diagnostic charts.

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| **Python (3.8+)** | Core programming language for data processing |
| **Pandas** | Tabular data manipulation, profiling, rule evaluation, and transformations |
| **NumPy** | Vectorized condition filtering and mathematical computations |
| **Matplotlib** | Creation of executive dashboards, heatmaps, bar charts, and distributions |
| **OpenPyXL** | Automated multi-tab Excel workbook generation (`retail_kpi_dictionary.xlsx`) |
| **Jupyter Notebook** | Interactive exploratory data analysis and reproducible analytics workflow |
| **Git / GitHub** | Version control, documentation, and open-source project publication |

---

## 📁 Repository Structure

```
Retail-Kpi-Data-Quality-Analysis/
├── README.md                              # Comprehensive project overview & documentation
├── requirements.txt                      # Python library dependencies
├── .gitignore                            # Standard Python & Jupyter exclusion rules
│
├── notebooks/
│   └── retail_data_quality_analysis.ipynb # End-to-end data quality & KPI analytics workflow
│
├── data/
│   ├── retail-orders-raw.csv             # Raw source orders dataset (12 records, 9 attributes)
│   └── retail-data-dictionary.csv        # Column specifications & business rules
│
├── outputs/
│   ├── initial_data_profile.csv          # Column-level data types, missing counts & completeness %
│   ├── missing_value_summary.csv         # Summary of null occurrences per column
│   ├── data_quality_issues.csv           # Granular audit log of detected rule violations
│   ├── quality_issue_summary.csv         # Aggregated violations grouped by column, rule & severity
│   ├── retail_orders_analysis_ready.csv  # Standardized dataset with eligibility flags
│   ├── preliminary_kpi_results.csv       # Executed baseline KPI calculations
│   ├── retail_kpi_dictionary.xlsx        # Multi-tab formal business KPI dictionary & quality rules
│   ├── project_summary.csv               # High-level pipeline metrics summary
│   └── charts/                           # Generated analytical charts & executive dashboards
│       ├── executive_kpi_dashboard.png
│       ├── category_performance_dashboard.png
│       ├── city_order_analysis.png
│       ├── data_completeness.png
│       ├── data_quality_heatmap.png
│       ├── data_quality_violations.png
│       ├── discount_vs_net_value.png
│       ├── monthly_order_trend.png
│       ├── net_order_value_by_category.png
│       ├── numeric_correlation_heatmap.png
│       ├── payment_status_analysis.png
│       └── payment_status_distribution.png
│
└── documentation/
    └── data_quality_contract.md          # Formal contract governing data quality standards & SLAs
```

---

## 📊 Dataset Description

The dataset simulates a multi-city direct-to-consumer educational and retail catalog.

* **Source File:** `data/retail-orders-raw.csv`
* **Raw Records:** 12 rows (including duplicates)
* **Fields:** 9 business attributes

| Column Name | Data Type | Description | Permitted Values / Constraints |
|---|---|---|---|
| `order_id` | string | Unique order transaction identifier | Non-null, unique identifier (e.g. `RT-1001`) |
| `order_date` | date | Date customer initiated the order | ISO-8601 (`YYYY-MM-DD`) between `2025-01-01` and present |
| `customer_segment` | category | Commercial customer classification | `Student`, `Fresher`, `Professional` |
| `city` | string | Customer billing and delivery city | Non-empty string |
| `category` | category | Product catalog group | `Learning Kit`, `Course Access`, `Mentor Session` |
| `quantity` | integer | Number of units purchased | Strictly positive integer (`quantity > 0`) |
| `unit_price` | decimal | Catalog price per unit before discounts | Non-negative numeric (`INR`) |
| `discount_pct` | decimal | Commercial promotional discount | Decimal between `0` and `100` inclusive |
| `payment_status` | category | Settlement state of the transaction | `Paid`, `Pending`, `Failed`, `Refunded` |

---

## 🔍 Data Quality Validation Workflow

Every incoming row is evaluated across four core dimensions of data hygiene:

```
Raw CSV Ingestion
       │
       ▼
[Dimension Screening]
 ├── Completeness ───► Check for null or empty string fields
 ├── Uniqueness   ───► Identify duplicate order_id records
 ├── Validity     ───► Validate dates, positive quantities, valid price & discounts (0-100)
 └── Consistency  ───► Validate categorical values against approved enumerations
       │
       ▼
Violation Audit Logging ──► outputs/data_quality_issues.csv
       │
       ▼
Eligibility Filtering  ──► Flag records eligible for financial analytics
       │
       ▼
KPI Computation ────────► outputs/preliminary_kpi_results.csv
```

### Detected Quality Violations in Raw Source

| Order ID | Detected Anomaly | Dimension | Severity | Handling & Impact |
|---|---|---|---|---|
| **RT-1002** | Date formatted as `03/01/2026` (DD/MM/YYYY) | Validity | Critical | Coerced to ISO datetime; flags invalid date handling |
| **RT-1003** | `customer_segment = 'student'` (lowercase) | Consistency | High | Standardized via title-casing normalization |
| **RT-1003** | `discount_pct` is blank / null | Completeness | Warning | Excluded from value analysis; not assumed zero without approval |
| **RT-1004** | Duplicate order row (identical record on lines 5 & 6) | Uniqueness | Critical | Flagged as duplicate; excluded from revenue aggregation |
| **RT-1005** | `city` is empty / null | Completeness | High | Excluded from regional analysis; flagged for provider review |
| **RT-1006** | Invalid date `2026-13-10` (month 13 out of bounds) | Validity | Critical | Date parsing fails (coerced to NaT); record quarantined |
| **RT-1006** | `quantity = -1` (negative value) | Validity | Critical | Excluded from volume and revenue totals |
| **RT-1007** | `discount_pct = 105` (> 100%) | Validity | Critical | Invalid discount; record excluded from net value analysis |
| **RT-1008** | `quantity = 'two'` (non-numeric string) | Validity | Critical | Numeric conversion fails (coerced to NaN); record quarantined |
| **RT-1011** | `order_date` is completely missing | Completeness | Critical | Missing required event timestamp; record quarantined |

---

## 📐 KPI Definitions & Business Purpose

> **⚠️ Governance & Accounting Notice:**  
> All monetary metrics calculated in this study represent **analytical estimates for operational insights**, **not audited accounting revenue**. Unresolved duplicate identifiers and sample limitations require business sign-off before financial reporting.

| KPI Name | Domain | Formula | Business Purpose |
|---|---|---|---|
| **Eligible Unique Orders** | Volume | `COUNT(order_id) WHERE eligible == True` | Measures total clean, valid transactions ready for analytics. |
| **Paid Orders** | Collections | `COUNT(order_id) WHERE status == 'Paid'` | Measures successfully settled transactions. |
| **Pending Orders** | Collections | `COUNT(order_id) WHERE status == 'Pending'` | Measures pipeline awaiting gateway settlement. |
| **Failed Orders** | Risk | `COUNT(order_id) WHERE status == 'Failed'` | Measures checkout drop-offs due to payment processing failures. |
| **Refunded Orders** | Retention | `COUNT(order_id) WHERE status == 'Refunded'` | Measures post-purchase dissatisfaction or returns. |
| **Failed Payment Rate** | Risk | `(Failed / Total Eligible) × 100` | Monitors payment gateway reliability and friction. |
| **Refunded Order Rate** | Experience | `(Refunded / Total Eligible) × 100` | Evaluates product quality and customer satisfaction. |
| **Estimated Gross Order Value** | Value | `SUM(quantity × unit_price)` | Estimated total merchandise value before discount deductions. |
| **Estimated Discount Amount** | Margin | `SUM(Gross × discount_pct / 100)` | Quantifies total promotional margin concessions. |
| **Estimated Net Order Value** | Value | `SUM(Gross − Discount Amount)` | Net estimated commercial throughput from valid sales. |
| **Average Net Order Value (AOV)** | Value | `MEAN(Net Order Value)` | Measures average spend per clean order transaction. |

---

## 📈 Visualizations & Dashboards

All charts are exported to `outputs/charts/` at high resolution (150+ DPI):

1. **Executive KPI Dashboard (`executive_kpi_dashboard.png`)** — High-level summary of total revenue estimates, order conversion, and category splits.
2. **Category Performance Dashboard (`category_performance_dashboard.png`)** — Comparative analysis of volume, discount depth, and net realization across product lines.
3. **Data Quality Heatmap (`data_quality_heatmap.png`)** — Matrix visualization displaying record-by-column rule failure distribution.
4. **Data Completeness Profile (`data_completeness.png`)** — Horizontal bar chart displaying fill rates across all 9 schema columns.
5. **Data Quality Violations (`data_quality_violations.png`)** — Frequency of rule breaches categorized by attribute.
6. **Payment Status Analysis (`payment_status_analysis.png`)** — Settlement breakdown and failure distribution.
7. **City-Level Order Analysis (`city_order_analysis.png`)** — Geographic distribution of customer orders.
8. **Net Order Value by Category (`net_order_value_by_category.png`)** — Contribution of each product family to net estimated turnover.

---

## 🚀 Getting Started

### 1. Clone the Repository
```bash
git clone https://github.com/Gurusaiprasadreddy/Retail-Kpi-Data-Quality-Analysis.git
cd Retail-Kpi-Data-Quality-Analysis
```

### 2. Set Up Virtual Environment (Recommended)
```bash
# Windows
python -m venv venv
.\venv\Scripts\activate

# Linux / macOS
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

### 4. Run the Jupyter Notebook
```bash
# Launch Jupyter
jupyter notebook notebooks/retail_data_quality_analysis.ipynb
```
Select **Run All Cells**. All tables, profiling CSVs, the Excel KPI dictionary, and chart figures will be systematically generated into `outputs/`.

---

## 📋 Project Deliverables & Audit Status

| Deliverable | Location | Status |
|---|---|---|
| **Raw Datasets** | `data/retail-orders-raw.csv`, `retail-data-dictionary.csv` | Verified |
| **Analysis Notebook** | `notebooks/retail_data_quality_analysis.ipynb` | Executed & Tested |
| **Data Quality Profile** | `outputs/initial_data_profile.csv` | Ready |
| **Issues Audit Log** | `outputs/data_quality_issues.csv` | Ready |
| **Issues Summary** | `outputs/quality_issue_summary.csv` | Ready |
| **Cleaned Dataset** | `outputs/retail_orders_analysis_ready.csv` | Ready |
| **Preliminary KPIs** | `outputs/preliminary_kpi_results.csv` | Ready |
| **Excel KPI Dictionary** | `outputs/retail_kpi_dictionary.xlsx` | Multi-tab Generated |
| **Project Summary** | `outputs/project_summary.csv` | Ready |
| **Data Quality Contract** | `documentation/data_quality_contract.md` | Formal Specification |
| **Dashboards & Charts** | `outputs/charts/*.png` (12 visual assets) | Generated |

---

## ⚠️ Limitations & Future Enhancements

* **Sample Size Constraints:** The current dataset consists of a 12-row sample intended for pipeline validation and contract demonstration. Statistical conclusions must be validated against production-scale data.
* **Grain & Duplicate Clarification:** The duplicate record for `RT-1004` appears identically on two rows. Business confirmation is required to determine whether this represents a true system duplicate or multiple line items under the same order header.
* **Automated CI/CD Quality Gates:** Future iterations will integrate Great Expectations or PyDeequ into a continuous deployment data pipeline to block corrupt ingestion at source.
* **Interactive BI Dashboard:** Planned integration with Microsoft Power BI and Tableau for real-time stakeholder monitoring and automated SLA alerting.

---

## 📜 Governance & Author

* **Author:** Bompalli Guru Sai Prasad Reddy
* **Internship:** RabTech Academy — Data Analytics & Business Intelligence Track
* **Repository:** [https://github.com/Gurusaiprasadreddy/Retail-Kpi-Data-Quality-Analysis](https://github.com/Gurusaiprasadreddy/Retail-Kpi-Data-Quality-Analysis)
