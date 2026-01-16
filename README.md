# 🚀 Sales, Finance & Supply Chain Analytics (SQL)

## 📌 Project Overview
This repository contains an **end-to-end SQL analytics project** built for a **simulated (imaginary) global electronics manufacturer, AtliQ Hardware**.  
The project demonstrates how **advanced SQL** can be used to deliver **business-ready insights** across **Sales, Finance (P&L), and Supply Chain** functions.

> ⚠️ **Note:** AtliQ Hardware is a fictional company created solely for learning and portfolio demonstration purposes.

The focus of this project goes beyond writing queries:
- Solving real-world business problems
- Designing scalable data models
- Automating analytics workflows
- Optimizing query performance
- Translating data into actionable insights

---

## 🏢 Business Context
AtliQ Hardware (imaginary company) operates across:
- **3 product divisions**  
  - Peripherals & Accessories (P&A)  
  - PCs (Laptops & Desktops)  
  - Networking & Storage (N&S)
- **300+ unique SKUs**
- **15+ countries** grouped into **4 regions** (APAC, EU, North America, LATAM)
- Multiple sales channels: Direct, Brick & Mortar, and E-commerce

Due to this scale, the business required a centralized analytics solution to support data-driven decision-making.

---

## 🎯 Problem Statement
Key challenges addressed:
1. No **single source of truth** for Sales, Finance, and Supply Chain analytics  
2. Manual and slow P&L and sales reporting  
3. Limited visibility into:
   - Customer- and market-level profitability
   - Revenue concentration risk
   - Discount effectiveness  
4. No standardized **forecast accuracy monitoring**  
5. Performance bottlenecks in complex analytical SQL queries  

---

## 🧠 Solution Approach

### 1️⃣ Data Modeling
A **star schema** optimized for analytics was designed.

**Fact Tables**
- `fact_sales_monthly`
- `fact_forecast_monthly`
- `fact_pricing`

**Dimension Tables**
- `dim_customer`
- `dim_product`
- `dim_date`

This structure enables efficient joins, aggregations, and time-series analysis.

---

### 2️⃣ Analytics & SQL Implementation
The analytics layer uses:
- **Views** for reusable business logic  
- **Stored Procedures** for parameterized reporting  
- **User-Defined Functions (UDFs)** for fiscal calculations  
- **Triggers** for automated data consistency  

**Analytics Coverage**
- Finance & P&L reporting
- Sales & market performance
- Supply chain forecast accuracy
- Revenue contribution & concentration analysis

---

### 3️⃣ Performance Optimization
Key optimizations implemented:
- Replaced function-based filtering with a **date dimension**
- Embedded `fiscal_year` into fact tables
- Optimized joins and aggregations
- Refactored complex queries for readability

**Result:**  
⏱️ Query execution time improved by **~35–40%**

---

## 📊 Key Insights
- Top 2 customers contributed **~40% of total net sales**, indicating concentration risk  
- Certain **EU markets showed higher Gross Margin % than APAC**  
- Higher discounts did **not always increase revenue**  
- Forecast accuracy dropped below **80% for select customers**, highlighting demand planning gaps  

---

## 🚀 Business Impact
- Established a **single source of truth** for analytics  
- Reduced manual reporting effort by **~40%**  
- Improved pricing, discount, and margin visibility  
- Enabled proactive forecast accuracy monitoring  
- Delivered scalable, business-aligned SQL solutions  

---

## 🛠️ Skills & Concepts Demonstrated
- Advanced SQL (CTEs, Window Functions)
- Stored Procedures, Triggers, UDFs
- Star schema data modeling
- Financial analytics (P&L, margins)
- Sales & market analysis
- Supply chain forecasting analytics
- Query performance optimization
- Business-oriented problem solving

---

## 🧪 Sample SQL Queries & Outputs

Below are **representative SQL queries** from each domain.  
(Full SQL scripts are available in the repository folders.)

---

### 💰 Finance Analytics – Monthly Gross Sales

**Business Question:**  
How much revenue does a key customer generate month over month?

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
📊 Output: <img width="602" height="640" alt="Screenshot 2026-01-16 122327" src="https://github.com/user-attachments/assets/44e52717-6019-4637-bb72-4d81c4d143b6" />

2. Net Sales View
```sql
 CREATE VIEW net_sales AS
SELECT 
    *, 
    net_invoice_sales*(1-post_invoice_discount_pct) AS net_sales
FROM sales_postinv_discount;
```
📊 Output: <img width="1540" height="342" alt="Screenshot 2026-01-16 123602" src="https://github.com/user-attachments/assets/960a75b4-c517-4de1-82c5-15fe269a6f2c" />
            <img width="1556" height="339" alt="Screenshot 2026-01-16 123618" src="https://github.com/user-attachments/assets/1486df6c-f094-4cf0-8ef9-91b65792ac43" />
            <img width="154" height="303" alt="Screenshot 2026-01-16 123630" src="https://github.com/user-attachments/assets/3b717bf3-af3f-4a03-b1ca-4721eb0a45f2" />


3. Stored Procedure – Top N Markets by Net Sales
```sql
   CREATE PROCEDURE get_top_n_markets_by_net_sales(
    IN in_fiscal_year INT,
    IN in_top_n INT
)
BEGIN
    SELECT 
        market, 
        ROUND(SUM(net_sales)/1000000,2) AS net_sales_mln
    FROM net_sales
    WHERE fiscal_year = in_fiscal_year
    GROUP BY market
    ORDER BY net_sales_mln DESC
    LIMIT in_top_n;
END;
```
```sql
call gdb0041.get_top_n_products_by_net_sales(2021, 5);
```
📊 Output: <img width="514" height="167" alt="image" src="https://github.com/user-attachments/assets/feb55a25-ddfc-43da-91a1-f4ce60910fd5" />

SALES ANALYTICS
4. Customer Contribution % (Window Function) 
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
📊 Output: <img width="418" height="314" alt="image" src="https://github.com/user-attachments/assets/a80d1368-a6c6-4703-8329-e86ac0a482a2" />

5. Top Customers by Market (Stored Proc)
```sql
      CREATE PROCEDURE get_top_n_customers_by_net_sales(
    IN in_market VARCHAR(45),
    IN in_fiscal_year INT,
    IN in_top_n INT
)
BEGIN
    SELECT 
        customer, 
        ROUND(SUM(net_sales)/1000000,2) AS net_sales_mln
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
call gdb0041.get_top_n_customers_by_net_sales('USA', 2021, 5);
```
📊 Output: <img width="334" height="167" alt="image" src="https://github.com/user-attachments/assets/b685ecec-8b90-4220-a8a8-de24abd01b4c" />

SUPPLY CHAIN ANALYTICS

6. Forecast Accuracy (CTE Version)
```sql
     WITH forecast_err_table AS (
    SELECT
        s.customer_code,
        c.customer,
        SUM(s.sold_quantity) AS total_sold_qty,
        SUM(s.forecast_quantity) AS total_forecast_qty,
        ROUND(
            SUM(ABS(s.forecast_quantity - s.sold_quantity)) * 100 
            / SUM(s.forecast_quantity), 2
        ) AS abs_error_pct
    FROM fact_act_est s
    JOIN dim_customer c
        ON s.customer_code = c.customer_code
    WHERE s.fiscal_year = 2021
    GROUP BY s.customer_code
)
SELECT *,
       IF(abs_error_pct > 100, 0, 100 - abs_error_pct) AS forecast_accuracy
FROM forecast_err_table
ORDER BY forecast_accuracy DESC
limit 5;
```
📊 Output: <img width="774" height="206" alt="image" src="https://github.com/user-attachments/assets/31b64f03-a626-4664-9c94-2c67d4afa243" />

7. Performance Optimization
Replaced repeated fiscal year function calls with a date dimension and later embedded fiscal_year directly into the fact table, reducing query execution time by 35–40%.

8. ADVANCED ANALYTICS

```sql
     SELECT 
    *,
    SUM(amount) OVER (PARTITION BY category ORDER BY date) AS expenses_till_date
FROM random_tables.expenses
limit 5;
```
output: <img width="557" height="186" alt="image" src="https://github.com/user-attachments/assets/d892e715-bda7-4b9e-82b4-a566f7e8682c" />



