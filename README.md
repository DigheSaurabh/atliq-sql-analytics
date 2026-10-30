# 🚀 Sales, Finance & Supply Chain Analytics (SQL)

## 📌 Project Overview
An end-to-end **MySQL analytics project** on **AtliQ Hardware**, a fictional global
electronics manufacturer. It uses SQL to answer business questions across
**Sales, Finance (P&L) and Supply Chain**, with a focus on reliable reporting,
forecast accuracy and query performance.

> ⚠️ **Note:** AtliQ Hardware is a fictional company. The dataset and business
> questions come from the **Codebasics SQL for Data Analysts** course. I wrote and
> ran the queries, applied and measured the performance optimization, ran the
> forecast-accuracy analysis below, and documented the results as a business case.
> Because the data is fictional, there is no real business impact.

---

## ⭐ Highlights
- **Cut report query time 86% (3.00 s → 0.41 s)** by replacing a row-by-row
  fiscal-year function with a stored `fiscal_year` column, with an identical result
  ($1,664.64M gross sales, FY2021).
- **Analyzed forecast accuracy for 209 customers** using CTEs and window
  functions: none reached 48% in FY2021, and 60 customers declined year over year,
  exposing a systemic demand-planning gap.
- **Built reusable reporting** with views, stored procedures for top-N markets and
  customers, and a user-defined function for fiscal-year logic.

---

## 🏢 Business Context
AtliQ Hardware (fictional) operates across:
- **3 product divisions:** Peripherals & Accessories (P&A), PCs, Networking & Storage (N&S)
- **300+ SKUs**
- **15+ countries** in **4 regions** (APAC, EU, North America, LATAM)
- **Sales channels:** Direct, Brick & Mortar, E-commerce

## 🎯 Problem Statement
1. No **single source of truth** for Sales, Finance and Supply Chain reporting
2. Manual, slow P&L and sales reporting
3. Limited visibility into customer- and market-level profitability and revenue concentration
4. No standard **forecast accuracy** monitoring
5. Slow analytical queries

---

## 🧠 Solution Approach

### 1️⃣ Data Model
I worked with a **star schema** built for analytics:

| Fact tables | Dimension tables |
|---|---|
| `fact_sales_monthly`, `fact_forecast_monthly`, `fact_gross_price`, `fact_pricing` | `dim_customer`, `dim_product`, `dim_date` |

### 2️⃣ Analytics Layer
- **Views** for reusable business logic (e.g., net sales)
- **Stored procedures** for parameterized top-N reports
- **A user-defined function** (`get_fiscal_year`) for fiscal-year logic
- **CTEs and window functions** for contribution and forecast-accuracy analysis

---

## ⏱️ Performance Optimization (measured)

**Problem:** the original reports filtered on `get_fiscal_year(s.date) = 2021`. That
runs the function on every row of `fact_sales_monthly` and stops MySQL from using
an index.

**Fix:** store `fiscal_year` as a column in the fact tables and filter on it directly.

**Before**
```sql
SELECT ROUND(SUM(s.sold_quantity * g.gross_price)/1000000, 2) AS gross_sales_mln
FROM fact_sales_monthly s
JOIN fact_gross_price g
  ON g.product_code = s.product_code
 AND g.fiscal_year = get_fiscal_year(s.date)
WHERE get_fiscal_year(s.date) = 2021;
```

**After**
```sql
SELECT ROUND(SUM(s.sold_quantity * g.gross_price)/1000000, 2) AS gross_sales_mln
FROM fact_sales_monthly s
JOIN fact_gross_price g
  ON g.product_code = s.product_code
 AND g.fiscal_year = s.fiscal_year
WHERE s.fiscal_year = 2021;
```

**Result** (MySQL Workbench duration; each query run 4 times, first warm-up run excluded):

| Run | Before (function) | After (stored column) |
|---|---|---|
| 2 | 3.015 s | 0.406 s |
| 3 | 2.969 s | 0.406 s |
| 4 | 3.015 s | 0.422 s |
| **Average** | **3.00 s** | **0.41 s** |

**86% faster (about 7×)**, with an identical result: **$1,664.64M** gross sales in FY2021.

<!-- Add your two Workbench screenshots here, e.g. docs/images/before.png and docs/images/after.png -->

---

## 📦 Supply Chain: Forecast Accuracy (FY2021)

Forecast accuracy per customer = 100% − absolute forecast error %
(set to 0% when the error is above 100%).

| Finding | Result |
|---|---|
| Customers analyzed | **209** |
| Customers below 80% accuracy | **209 of 209** |
| Customers below 50% accuracy | **209 of 209** |
| Accuracy range | **0% – 47.8%** |
| Average accuracy, FY2020 → FY2021 | **27.6% → 36.9%** |
| Customers whose accuracy declined vs. FY2020 | **60 of 209** |
| Over-forecast vs. under-forecast | **109 vs. 100** |

**What it means:** average accuracy improved, but no customer reached even 50%,
and errors were split almost evenly between over-forecasting (excess stock) and
under-forecasting (stockouts). This points to a **systemic forecasting-process
gap**, not a few problem accounts.

**What a demand planner would do:** start with the 60 declining customers, ranked
by sales volume; review their forecast inputs with the sales team; adjust safety
stock for their products; and track accuracy monthly.

```sql
-- Forecast accuracy spread, FY2021
WITH forecast_err_table AS (
  SELECT s.customer_code,
         SUM(s.forecast_quantity - s.sold_quantity) AS net_err,
         ROUND(SUM(ABS(s.forecast_quantity - s.sold_quantity)) * 100
               / SUM(s.forecast_quantity), 2) AS abs_error_pct
  FROM fact_act_est s
  WHERE s.fiscal_year = 2021
  GROUP BY s.customer_code
), acc AS (
  SELECT *, IF(abs_error_pct > 100, 0, 100 - abs_error_pct) AS forecast_accuracy
  FROM forecast_err_table
)
SELECT ROUND(MIN(forecast_accuracy),1) AS min_acc,
       ROUND(AVG(forecast_accuracy),1) AS avg_acc,
       ROUND(MAX(forecast_accuracy),1) AS max_acc,
       SUM(forecast_accuracy < 50)     AS below_50,
       SUM(net_err > 0)                AS over_forecast,
       SUM(net_err < 0)                AS under_forecast
FROM acc;
```

```sql
-- Year-over-year change, FY2020 vs FY2021
WITH acc AS (
  SELECT s.customer_code, s.fiscal_year,
         IF(SUM(ABS(s.forecast_quantity - s.sold_quantity))*100/SUM(s.forecast_quantity) > 100, 0,
            100 - SUM(ABS(s.forecast_quantity - s.sold_quantity))*100/SUM(s.forecast_quantity)) AS fa
  FROM fact_act_est s
  WHERE s.fiscal_year IN (2020, 2021)
  GROUP BY s.customer_code, s.fiscal_year
)
SELECT ROUND(AVG(CASE WHEN a.fiscal_year = 2020 THEN a.fa END),1) AS avg_fa_2020,
       ROUND(AVG(CASE WHEN a.fiscal_year = 2021 THEN a.fa END),1) AS avg_fa_2021,
       COUNT(DISTINCT CASE WHEN a.fiscal_year = 2021 AND a.fa < b.fa THEN a.customer_code END) AS customers_dropped,
       COUNT(DISTINCT CASE WHEN a.fiscal_year = 2021 AND b.fa IS NOT NULL THEN a.customer_code END) AS customers_in_both_years
FROM acc a
LEFT JOIN acc b ON a.customer_code = b.customer_code AND b.fiscal_year = 2020;
```

---

## 📊 Other Key Insights
- The top 2 customers contributed **[X]%** of FY2021 net sales, a revenue concentration risk
- Some **EU markets showed higher gross margin % than APAC**
- **Higher discounts did not always increase revenue**

---

## 🧪 Sample SQL Queries & Outputs

### 💰 Finance: Monthly Gross Sales for a Key Customer
```sql
SELECT 
    s.date,
    SUM(ROUND(s.sold_quantity * g.gross_price, 2)) AS monthly_gross_sales
FROM fact_sales_monthly s
JOIN fact_gross_price g
  ON g.product_code = s.product_code
 AND g.fiscal_year = s.fiscal_year
WHERE s.customer_code = 90002002
GROUP BY s.date
ORDER BY s.date;
```
📊 Output: <img width="602" height="640" alt="Monthly gross sales output" src="https://github.com/user-attachments/assets/44e52717-6019-4637-bb72-4d81c4d143b6" />

### 💰 Finance: Net Sales View
```sql
CREATE VIEW net_sales AS
SELECT 
    *, 
    net_invoice_sales * (1 - post_invoice_discount_pct) AS net_sales
FROM sales_postinv_discount;
```
📊 Output: <img width="1540" height="342" alt="Net sales view output" src="https://github.com/user-attachments/assets/960a75b4-c517-4de1-82c5-15fe269a6f2c" />

### 📈 Sales: Top N Markets by Net Sales (Stored Procedure)
```sql
CREATE PROCEDURE get_top_n_markets_by_net_sales(
    IN in_fiscal_year INT,
    IN in_top_n INT
)
BEGIN
    SELECT 
        market, 
        ROUND(SUM(net_sales)/1000000, 2) AS net_sales_mln
    FROM net_sales
    WHERE fiscal_year = in_fiscal_year
    GROUP BY market
    ORDER BY net_sales_mln DESC
    LIMIT in_top_n;
END;
```
```sql
CALL gdb0041.get_top_n_markets_by_net_sales(2021, 5);
```
📊 Output: <img width="514" height="167" alt="Top N markets output" src="https://github.com/user-attachments/assets/feb55a25-ddfc-43da-91a1-f4ce60910fd5" />

### 📈 Sales: Customer Contribution % (CTE + Window Function)
```sql
WITH cte1 AS (
    SELECT  
        customer,
        ROUND(SUM(net_sales / 1000000), 2) AS net_sales_mln
    FROM net_sales s
    JOIN dim_customer c 
        ON s.customer_code = c.customer_code
    WHERE s.fiscal_year = 2021
    GROUP BY customer
)
SELECT *,
       (net_sales_mln * 100) / SUM(net_sales_mln) OVER () AS pct_contribution
FROM cte1
ORDER BY net_sales_mln DESC;
```
📊 Output: <img width="418" height="314" alt="Customer contribution output" src="https://github.com/user-attachments/assets/a80d1368-a6c6-4703-8329-e86ac0a482a2" />

### 📈 Sales: Top N Customers by Market (Stored Procedure)
```sql
CREATE PROCEDURE get_top_n_customers_by_net_sales(
    IN in_market VARCHAR(45),
    IN in_fiscal_year INT,
    IN in_top_n INT
)
BEGIN
    SELECT 
        customer, 
        ROUND(SUM(net_sales)/1000000, 2) AS net_sales_mln
    FROM net_sales s
    JOIN dim_customer c
        ON s.customer_code = c.customer_code
    WHERE s.fiscal_year = in_fiscal_year
      AND s.market = in_market
    GROUP BY customer
    ORDER BY net_sales_mln DESC
    LIMIT in_top_n;
END;
```
```sql
CALL gdb0041.get_top_n_customers_by_net_sales('USA', 2021, 5);
```
📊 Output: <img width="334" height="167" alt="Top N customers output" src="https://github.com/user-attachments/assets/b685ecec-8b90-4220-a8a8-de24abd01b4c" />

---

## 🛠️ Skills Demonstrated
- SQL (MySQL): joins, aggregations, CTEs, window functions
- Views, stored procedures, user-defined functions
- Star schema data models
- Query performance optimization (measured before and after)
- Financial analytics (P&L, net sales, contribution)
- Supply chain forecast-accuracy analysis
- Turning query results into business recommendations

## 📁 Repository Contents
SQL scripts are in the `sql/` folder, organized by domain (finance, sales, supply chain, performance).

---

<p align="center"><sub>Built by Saurabh Dighe · <a href="https://github.com/DigheSaurabh">GitHub</a> · <a href="https://www.linkedin.com/in/saurabh-dighe-9b84a637a/">LinkedIn</a></sub></p>
