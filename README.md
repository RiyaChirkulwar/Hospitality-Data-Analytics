# Hospitality-Data-Analytics

**Domain:** Hospitality | **Stack:** Python, Pandas, Matplotlib, Jupyter

A data analytics project built on a star-schema hospitality dataset to surface revenue and occupancy insights for a multi-city luxury hotel chain. The work spans the full analytics pipeline — schema profiling, data quality remediation, feature engineering, cross-table joins, and business question resolution.

---

## Dataset Architecture

The dataset follows a standard star schema with two fact tables and three dimension tables:

```
dim_hotels        →  property metadata (city, category)
dim_rooms         →  room type to class mapping
dim_date          →  date dimension with day_type, month labels

fact_bookings            →  booking-level transactions
fact_aggregated_bookings →  property × room × date aggregations
new_data_august.csv      →  incremental monthly append
```

---

## Data Cleaning

All remediation was applied to `fact_bookings` before any analysis:

**Invalid records** — Filtered out bookings where `no_guests ≤ 0`, representing corrupt or test entries.

**Outlier removal (revenue_generated)** — Applied a 3σ upper bound filter. Records exceeding `mean + 3×std` were dropped to prevent skewed aggregations. No records fell below the lower bound.

**Outlier validation (revenue_realized)** — Same 3σ analysis run on realized revenue as a cross-check.

**Type correction** — `check_in_date` and `dim_date.date` were cast to `datetime64` to enable temporal joins and month-level groupbys.

**Incremental append** — August data was schema-validated against the main dataframe before concatenation via `pd.concat`.

---

## Feature Engineering

**Occupancy Percentage (`occ_pct`)**
```python
df['occ_pct'] = (df['successful_bookings'] / df['capacity']) * 100
```
Derived on `fact_aggregated_bookings` before cross-table merges.

**Month extraction**
```python
df['month'] = df['check_in_date'].dt.month
```
Used for month-on-month revenue aggregation.

---

## Business Problems Solved

**1. Average occupancy rate by room class**
Merged `fact_aggregated_bookings` with `dim_rooms` on `room_id`, then grouped by `room_class` to compute mean `occ_pct`.

**2. Average occupancy rate by city**
Extended the merge chain with `dim_hotels` on `property_id`, grouped by `city`. Visualized as a bar chart.

**3. Weekday vs. Weekend occupancy**
Joined with `dim_date` on `check_in_date`, grouped by `day_type` — weekends consistently outperform weekdays.

**4. City-level occupancy for June 2022**
Filtered `mmm yy == "Jun 22"` on the merged frame, then grouped by `city`.

**5. Revenue realized per city**
Merged `fact_bookings` with `dim_hotels`, aggregated `revenue_realized` by `city`.

**6. Month-on-month revenue trend**
Joined with `dim_date`, extracted month from datetime, grouped and summed `revenue_realized` across months.

---

## Repository Structure

```
├── dataExploration.ipynb        # Schema profiling, distribution analysis, platform breakdown
├── dataCleaning.ipynb           # Cleaning, transformation, joins, business Q&A
└── data/
    ├── dim_date.csv
    ├── dim_hotels.csv
    ├── dim_rooms.csv
    ├── fact_bookings.csv
    ├── fact_aggregated_bookings.csv
    └── new_data_august.csv
```

---

## Setup

```bash
git clone https://github.com/your-username/hospitality-analytics.git
cd hospitality-analytics
pip install pandas matplotlib jupyter
jupyter notebook
```

Start with `dataExploration.ipynb` for schema context, then `dataCleaning.ipynb` for the full pipeline.

---

## Author

**Your Name** — [LinkedIn](www.linkedin.com/in/riyachirkulwar07) · [GitHub](https://github.com/RiyaChirkulwar/Hospitality-Data-Analytics)