# Data Dictionary

## Source tables (real data, 01_Raw_Data/)

**orders.csv** (4,050 rows)
| Column | Type | Description |
|---|---|---|
| id | int | Order ID |
| account_id | int | FK → accounts.id |
| occurred_at | datetime | Order timestamp |
| standard_qty / gloss_qty / poster_qty | int | Units sold by product line |
| total | int | Total units |
| standard_amt_usd / gloss_amt_usd / poster_amt_usd | decimal | Revenue by product line |
| total_amt_usd | decimal | Total order revenue (the field used as `Sales_USD`) |

**accounts.csv** (351 rows): `id`, `name`, `website`, `lat`, `long`, `primary_poc`, `sales_rep_id` (FK → sales_reps.id)

**sales_reps.csv** (50 rows): `id`, `name`, `region_id` (FK → region.id)

**region.csv** (4 rows): `id`, `name` — Northeast, Midwest, Southeast, West

**field_force_operational_data.xlsx → Operational_Data** (1,250 rows = 50 reps × 25 months, Jan 2015–Jan 2017)
| Column | Description |
|---|---|
| Rep_ID / Region_ID | Keys matching sales_reps.id / region.id |
| Month | First-of-month join key |
| Working_Days, Attendance_Days | **Simulated** |
| Planned_Visits, Completed_Visits | **Simulated** |
| Merchandising_Compliance, Product_Availability | **Simulated** |
| Target_Multiplier | **Simulated** — rising expectation factor (1.05–1.15) |

## Derived tables (workbook)

**Clean_Orders** — one row per order, joined to Rep/Region, with `Order_Date`, `Year`, `Month`, `Month_Name`, `Sales_USD` extracted.

**Monthly_Rep_Sales** — one row per (Rep, Year, Month) with at least one order; `Monthly_Sales` = SUMIFS over Clean_Orders.

**Rep_Avg_Sales** — one row per rep; `Avg_Monthly_Sales` = AVERAGEIF over Monthly_Rep_Sales. Used as the independent baseline for Target.

**Rep_Performance** — one row per rep: `Total_Sales`, `Total_Orders`, sorted descending by sales.

**Operational_Data** (workbook version) — the 1,250 simulated rows plus calculated columns: `Attendance_Rate`, `Visit_Completion_Rate`, `Visit_Gap`, `Merchandising_Gap`, `Availability_Gap`, `Actual_Sales`, `Target`, `Sales_Achievement`, `Sales_Gap`, `Performance_Status`.

**Gap_Analysis** — one row per rep, rolled up across all months, sorted by `Sales_Gap` ascending (worst first).

## Color convention (matches the xlsx skill's financial-model convention)
- **Blue font** = simulated/hardcoded input (sourced from `field_force_operational_data.xlsx`)
- **Black font** = real data or live formula
