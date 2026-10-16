# Retail Orders — Data Quality Contract

**Project:** KPI Dictionary & Data Quality Assessment
**Dataset:** retail-orders-raw.csv
**Version:** 1.1
**Last Updated:** October 2026
**Status:** Proposed (pending stakeholder sign-off)

---

## 1. Purpose

This document defines the validation standards, acceptance thresholds, severity levels, and escalation procedures for the Retail KPI Dictionary and Data Quality Assessment internship project. It ensures that KPIs computed from `retail-orders-raw.csv` are reliable, reproducible, and fit for business decision-making.

---

## 2. Scope

| Item | Detail |
|---|---|
| **Dataset** | `Dataset/retail-orders-raw.csv` |
| **Data grain** | One row per order (RT-XXXX). Confirmed: RT-1004 appears twice — treated as a duplicate order, not an order line. |
| **Row count (raw)** | 13 rows (including 1 confirmed duplicate) |
| **Column count** | 9 |
| **Decision owner** | Retail Operations Manager (proposed) |
| **Technical owner** | Data Analyst / BI Analyst Intern (proposed) |
| **Analysis notebook** | `data_quality_profile.ipynb` |

---

## 3. Column-Level Quality Rules and Thresholds

| Column | Data Type | Business Definition | Validation Rule | Acceptance Threshold | Failure Action |
|---|---|---|---|---|---|
| `order_id` | string | Unique order identifier | Required; no nulls; unique across all rows | 100% populated; 0 duplicates after resolution | Quarantine duplicates; block from KPI aggregation |
| `order_date` | date | Date the customer placed the order | Required; ISO format YYYY-MM-DD; between 2025-01-01 and today | 100% valid dates | Reject from all date-based KPIs until corrected |
| `customer_segment` | category | Commercial customer segment | Required; value in {Student, Fresher, Professional} after case normalization | 100% valid after normalization | Flag unexpected values; exclude from segment KPIs |
| `city` | string | Customer billing city | Required; non-empty text | 100% populated | Flag missing; exclude from city-level KPIs |
| `category` | category | Product family | Required; value in {Learning Kit, Course Access, Mentor Session} | 100% valid | Flag unexpected values; exclude from category KPIs |
| `quantity` | integer | Units purchased | Required; whole number strictly greater than zero | 100% valid positive integers | Exclude invalid records from quantity and revenue KPIs |
| `unit_price` | decimal | Price per unit before discount (INR) | Required; numeric; non-negative | 100% valid | Exclude invalid records from all value calculations |
| `discount_pct` | decimal | Percentage discount applied | Numeric; between 0 and 100 inclusive; null treated as zero only after documented justification | 100% of populated discounts in range | Flag out-of-range values; flag nulls; do not silently impute |
| `payment_status` | category | Latest order settlement state | Required; value in {Paid, Pending, Failed, Refunded} after case normalization | 100% valid after normalization | Flag unknown values; exclude from payment-rate KPIs |

---

## 4. Observed Quality Issues (from raw file inspection)

The following issues were identified during initial profiling and drive the validation rules above:

| Order ID | Issue Description | Dimension | Severity |
|---|---|---|---|
| RT-1002 | `order_date` formatted as `03/01/2026` (DD/MM/YYYY) instead of ISO | Validity | Critical |
| RT-1003 | `customer_segment` = `student` (lowercase — not in approved list as-is) | Consistency | High |
| RT-1003 | `discount_pct` is null | Completeness | Warning |
| RT-1004 | Duplicate order ID (appears on rows 5 and 6 with identical values) | Uniqueness | Critical |
| RT-1005 | `city` is null | Completeness | High |
| RT-1006 | `order_date` = `2026-13-10` (month 13 is invalid) | Validity | Critical |
| RT-1006 | `quantity` = `-1` (negative value) | Validity | Critical |
| RT-1007 | `discount_pct` = `105` (exceeds 100%) | Validity | Critical |
| RT-1008 | `quantity` = `"two"` (non-numeric string) | Validity | Critical |
| RT-1011 | `order_date` is null | Completeness | Critical |

---

## 5. Freshness and Refresh Cadence

| Parameter | Value |
|---|---|
| **Proposed refresh cadence** | Daily |
| **Freshness SLA** | To be agreed with the internship mentor or data owner |
| **Date field note** | `order_date` is a business event date, not a file-arrival timestamp |
| **Freshness measurement** | Use file metadata or a dedicated source ingestion timestamp — not `order_date` |

---

## 6. Severity Levels and Escalation

### Critical
**Definition:** Issues that will materially distort published KPIs if left unresolved.

**Triggers:**
- Duplicate `order_id` values
- Unparseable or logically invalid `order_date`
- Non-numeric or negative `quantity`
- `discount_pct` outside the 0–100 range

**Action:**
1. Flag affected records immediately.
2. Prevent publication of any KPI that depends on the flagged records.
3. Notify the data owner (Retail Operations Manager) within one business day.
4. Resume publication only after correction or formal exception approval.

---

### High
**Definition:** Issues that reduce data coverage and introduce measurable bias in KPIs.

**Triggers:**
- Null `city` or `customer_segment`
- `payment_status` or `customer_segment` not in the approved category list (after case normalization)

**Action:**
1. Assign to the data owner for investigation and correction.
2. Exclude affected records from the relevant dimension KPIs.
3. Document the exclusion count and its impact on the metric.

---

### Warning
**Definition:** Issues that are non-critical but must not be silently suppressed.

**Triggers:**
- Null `discount_pct`

**Action:**
1. Do **not** impute zero without documented justification.
2. Exclude the record from discount-sensitive calculations.
3. Report the count of excluded records in the quality issue summary.

---

## 7. KPI Definitions

| KPI Name | Formula | Scope / Filter |
|---|---|---|
| **Total Orders** | `COUNT(DISTINCT order_id)` | After duplicate removal |
| **Total Units Sold** | `SUM(quantity)` | Valid records only (quantity > 0, numeric) |
| **Gross Order Value (INR)** | `SUM(quantity × unit_price)` | Valid quantity and unit_price records |
| **Net Order Value (INR)** | `SUM(quantity × unit_price × (1 − discount_pct / 100))` | Valid quantity, unit_price, and discount_pct records |
| **Average Discount %** | `AVG(discount_pct)` | Non-null discount_pct only |
| **Payment Success Rate** | `COUNT(payment_status = 'Paid') / COUNT(*)` | All non-duplicate records |
| **Data Completeness Rate** | `(non-null cells / total cells) × 100` | Entire dataset |
| **Data Quality Score** | `(records with zero violations / total records) × 100` | All rows including duplicates |

---

## 8. Responsibilities

### Data Analyst / BI Analyst Intern (proposed)
- Design and execute all validation checks in `data_quality_profile.ipynb`.
- Maintain issue reports in `Outputs/data_quality_issues.xls`.
- Document every exclusion with the reason and record count.
- Communicate KPI limitations clearly in `Outputs/project_summary.xls`.

### Retail Operations Manager (proposed)
- Confirm and approve business definitions (category lists, segment labels).
- Review and approve exception handling decisions.
- Sign off on KPI publication before external distribution.

### Data Provider / Source Owner
- Investigate source-level errors (e.g., date formatting, duplicate IDs) where feasible.
- Provide corrected source files or an explanation of root cause.

---

## 9. Evidence and Output Artefacts

| File | Description |
|---|---|
| `Outputs/initial_data_profile.xls` | Per-column statistics (count, nulls, unique values, min/max) |
| `Outputs/data_quality_issues.xls` | Row-level violations with issue type and severity |
| `Outputs/missing_value_summary.xls` | Null counts per column |
| `Outputs/quality_issue_summary.xls` | Aggregated issue counts by dimension and severity |
| `Outputs/preliminary_kpi_results.xls` | Computed KPI values with scope/filter notes |
| `Outputs/retail_orders_analysis_ready.xls` | Cleaned dataset with corrections applied and issue flags |
| `Outputs/project_summary.xls` | Executive summary of findings |

---

## 10. Governance and Audit Notes

1. **Source preservation:** The raw file `Dataset/retail-orders-raw.csv` must never be modified. All corrections are applied programmatically in the notebook and saved to the `Outputs/` folder.
2. **Proposed standards:** Thresholds and ownership roles are proposed project standards. They are **not** confirmed organizational policy until reviewed and approved by the relevant stakeholders.
3. **Exception documentation:** Any deviation from the rules above (e.g., treating a null discount as zero) must be explicitly justified in the notebook and noted in `project_summary.xls`.
4. **Version control:** Update the version number and Last Updated date at the top of this document whenever rules or thresholds change.
