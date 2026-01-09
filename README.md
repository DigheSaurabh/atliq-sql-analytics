# Sales, Finance & Supply Chain Analytics (SQL)

## 📌 Project Overview
This repository contains an **end-to-end SQL analytics project** built for a simulated global electronics manufacturer, **AtliQ Hardware**.  
The project demonstrates how **advanced SQL** can be used to deliver **business-ready insights** across **Sales, Finance (P&L), and Supply Chain** functions.

The focus of this project is not just querying data, but:
- Solving real business problems
- Designing scalable data models
- Automating analytics workflows
- Optimizing query performance
- Translating data into actionable insights

---

## 🏢 Business Context
AtliQ Hardware is a manufacturer of computer and electronic hardware products operating across:
- **3 product divisions**:  
  - Peripherals & Accessories (P&A)  
  - PCs (Laptops & Desktops)  
  - Networking & Storage (N&S)
- **300+ unique SKUs**
- **15+ countries** grouped into **4 business regions** (APAC, EU, North America, LATAM)
- Multiple sales channels: Direct, Brick & Mortar, and E-commerce

Due to this scale, the business required a robust analytics solution to support decision-making across functions.

---

## 🎯 Problem Statement
The key challenges addressed in this project were:
1. No **single source of truth** for Sales, Finance, and Supply Chain analytics  
2. Manual and time-consuming P&L and sales reporting  
3. Limited visibility into:
   - Customer and market-level profitability
   - Revenue concentration risk
   - Discount effectiveness  
4. No standardized way to monitor **forecast accuracy**  
5. Performance issues with complex analytical SQL queries

---

## 🧠 Solution Approach

### 1️⃣ Data Modeling
- Designed a **star schema** optimized for analytical queries

**Fact Tables**
- `fact_sales_monthly`
- `fact_forecast_monthly`
- `fact_pricing`

**Dimension Tables**
- `dim_customer`
- `dim_product`
- `dim_date`

This structure enables efficient joins, aggregations, and time-based analysis.

---

### 2️⃣ Analytics & SQL Implementation
The analytics layer was built using:
- **Views** for reusable reporting logic
- **Stored Procedures** for parameterized reports
- **User-Defined Functions (UDFs)** for consistent calculations
- **Triggers** for automated updates

#### 🔹 Finance & P&L Analytics
- Monthly and yearly P&L calculations
- Net Sales, COGS, Gross Margin, and GM%
- Market-wise margin comparison
- Automated fiscal year logic

#### 🔹 Sales & Market Analytics
- Top customers by net sales
- Product and division-level performance
- Market and region-wise revenue contribution
- Discount impact analysis

#### 🔹 Supply Chain Analytics
- Actual vs Forecast demand comparison
- Forecast accuracy calculation
- Customer-wise accuracy reporting
- Automated forecast accuracy reports

---

### 3️⃣ Performance Optimization
To ensure scalability and real-world applicability:
- Removed function-based filtering in WHERE clauses
- Introduced a dedicated date dimension for fiscal logic
- Optimized joins and aggregations
- Refactored queries for improved readability

**Result:**  
Query execution time improved by **~35–40%** for large analytical workloads.

---

## 📊 Key Insights
- **Top 2 customers contributed ~40% of total net sales**, indicating revenue concentration risk  
- Certain **EU markets showed higher Gross Margin % compared to APAC**  
- Higher discounts did **not always lead to higher revenue**  
- **Forecast accuracy dropped below 80% for select retail customers**, highlighting demand planning gaps

---

## 🚀 Business Impact
- Created a **single source of truth** for Sales, Finance, and Supply Chain analytics  
- Reduced manual reporting effort by **~40%** through automation  
- Improved pricing, discount, and margin visibility  
- Enabled proactive monitoring of forecast accuracy  
- Delivered scalable SQL solutions aligned with business needs

---

## 🛠️ Skills & Concepts Demonstrated
- Advanced SQL (CTEs, Window Functions)
- Stored Procedures, Triggers, and UDFs
- Star schema data modeling
- Financial analytics (P&L, margins)
- Sales and market analysis
- Supply chain forecasting analytics
- Query performance optimization
- Business-oriented problem solving

---


