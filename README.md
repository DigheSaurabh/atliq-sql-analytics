# 🚀 Sales, Finance & Supply Chain Analytics (SQL)

## 📌 Project Overview
This repository contains an **end-to-end SQL analytics project** developed as part of my **Data Analyst bootcamp**, built for a **simulated (imaginary) global electronics manufacturer, AtliQ Hardware**.  

The project demonstrates how **advanced SQL** can be used to deliver **business-ready insights** across **Sales, Finance (P&L), and Supply Chain** functions.

> ⚠️ **Note:** AtliQ Hardware is a fictional company created solely for learning and portfolio demonstration purposes during the bootcamp.

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

Due to this scale, the business required a centralized analytics solution to support **data-driven decision-making**.

---

## 🎯 Problem Statement
Key challenges addressed:
1. No **single source of truth** for Sales, Finance, and Supply Chain analytics  
2. Manual and time-consuming P&L and sales reporting  
3. Limited visibility into:
   - Customer- and market-level profitability  
   - Revenue concentration risk  
   - Discount effectiveness  
4. No standardized **forecast accuracy monitoring**  
5. Performance bottlenecks in complex analytical SQL queries  

---

## 🧠 Solution Approach

### 1️⃣ Data Modeling
Designed a **star schema** optimized for analytical workloads.

**Fact Tables**
- `fact_sales_monthly`
- `fact_forecast_monthly`
- `fact_pricing`

**Dimension Tables**
- `dim_customer`
- `dim_product`
- `dim_date`

This structure supports efficient joins, aggregations, and time-based analysis.

---

### 2️⃣ Analytics & SQL Implementation
Implemented using:
- **Views** for reusable business logic  
- **Stored Procedures** for parameterized reporting  
- **User-Defined Functions (UDFs)** for fiscal calculations  
- **Triggers** for automated data consistency  

Analytics coverage includes:
- Finance & P&L reporting  
- Sales & market performance  
- Supply chain forecast accuracy  
- Revenue contribution and concentration analysis  

---

### 3️⃣ Performance Optimization
Key optimizations applied:
- Replaced function-based filtering with a **date dimension**
- Embedded `fiscal_year` into fact tables
- Optimized joins and aggregations
- Refactored complex queries for readability

**Result:**  
⏱️ Query execution time improved by **~35–40%**

---

## 📊 Key Insights
- Top 2 customers contributed **~40% of total net sales**, indicating revenue concentration risk  
- Certain **EU markets showed higher Gross Margin % than APAC**  
- Higher discounts did **not always lead to higher revenue**  
- Forecast accuracy dropped below **80% for select customers**, highlighting demand planning gaps  

---

## 🚀 Business Impact
- Established a **single source of truth** for analytics  
- Reduced manual reporting effort by **~40%**  
- Improved pricing, discount, and margin visibility  
- Enabled proactive forecast accuracy monitoring  
- Delivered scalable SQL solutions aligned with real business use cases  

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


