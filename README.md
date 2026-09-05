# 🧮 AtliQ Hardware — Finance & Supply Chain Ad Hoc Analysis

<p align="center">

![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-Analysis-orange?style=for-the-badge)
![Finance Analytics](https://img.shields.io/badge/Finance-Analytics-green?style=for-the-badge)
![Supply Chain](https://img.shields.io/badge/Supply%20Chain-Analytics-purple?style=for-the-badge)

</p>

---

# 📌 Project Background

**AtliQ Hardware** is a fictional computer hardware manufacturer that sells PCs, printers, and peripherals through retail and e-commerce partners across multiple markets.

As a Data Analyst supporting the business, the objective of this project was to analyze sales, finance, and supply chain data using **MySQL** and provide answers to a set of real-world **Ad Hoc business requests**.

The project consists of **12 business requests**:

- **10 Finance & Sales Analytics requests**
- **2 Supply Chain Analytics requests**

The analysis focuses on turning business questions into SQL-based solutions that can help stakeholders understand sales performance, customer contribution, market performance, product trends, and forecasting accuracy.

---

## 🔎 Key Areas of Analysis

Insights and analysis are provided across the following key areas:

- **Finance & Sales Performance**
- **Customer & Market Analysis**
- **Product & Regional Performance**
- **Supply Chain & Forecast Accuracy**

The SQL queries developed for the analysis can be found in the repository under the relevant analysis folders.

---

# 🗂️ Data Structure & Initial Checks

The project uses a relational data model consisting of **fact** and **dimension tables** covering sales, pricing, deductions, freight, manufacturing costs, customers, products, dates, and sales forecasting.

## Fact Tables

- `fact_sales_monthly` — Actual monthly product sales by customer
- `fact_forecast_monthly` — Monthly forecast quantities
- `fact_act_est` — Actual vs. forecast quantities
- `fact_gross_price` — Product gross prices by fiscal year
- `fact_pre_invoice_deductions` — Customer-level pre-invoice discounts
- `fact_post_invoice_deductions` — Product/customer post-invoice deductions
- `fact_freight_cost` — Market-level freight and other costs
- `fact_manufacturing_cost` — Product manufacturing costs

## Dimension Tables

- `dim_customer` — Customer, platform, channel, market, sub-zone and region attributes
- `dim_product` — Product, division, segment, category and variant hierarchy
- `dim_date` — Calendar date and fiscal-year information

This structure enables analysis across **sales, pricing, profitability, customers, markets, products, and forecast accuracy** using SQL.

---

# 📐 Fiscal Calendar

AtliQ Hardware follows a fiscal year beginning in **September** and ending in **August**.

To simplify fiscal-period analysis, custom MySQL functions were created.

### Fiscal Year

```sql
CREATE FUNCTION get_fiscal_year(calendar_date DATE)
RETURNS INT
DETERMINISTIC
BEGIN
    DECLARE fiscal_year INT;

    SET fiscal_year =
        YEAR(DATE_ADD(calendar_date, INTERVAL 4 MONTH));

    RETURN fiscal_year;
END;
```

### Fiscal Quarter

```sql
CREATE FUNCTION get_fiscal_quarter(calendar_date DATE)
RETURNS CHAR(2)
DETERMINISTIC
BEGIN
    DECLARE m TINYINT;
    DECLARE qtr CHAR(2);

    SET m = MONTH(calendar_date);

    CASE
        WHEN m IN (9,10,11) THEN SET qtr = 'Q1';
        WHEN m IN (12,1,2) THEN SET qtr = 'Q2';
        WHEN m IN (3,4,5) THEN SET qtr = 'Q3';
        ELSE SET qtr = 'Q4';
    END CASE;

    RETURN qtr;
END;
```

These functions allow fiscal-year and fiscal-quarter logic to be reused across multiple analytical queries.

---

# 📊 Executive Summary

## Overview of Findings

The analysis provides a view of AtliQ Hardware's business performance from multiple perspectives.

From the Finance and Sales analysis, **Croma India's gross sales increased substantially from $1.32M in FY2018 to $44.64M in FY2022**, highlighting strong growth over the period.

The project also examines the contribution of different **markets, customers, products, and regions**, while the Supply Chain analysis focuses on **forecast accuracy and changes in customer forecasting performance**.

### Key Takeaways

- 📈 Croma India experienced significant gross sales growth between FY2018 and FY2022.
- 🌍 Market, customer, product, and regional analysis helps identify major contributors to business performance.
- 🚚 Forecast accuracy analysis highlights customers where supply chain forecasting performance requires closer attention.

---

# 🔍 Insights Deep Dive

# 💰 1. Finance & Sales Performance

## Croma India Gross Sales Growth

One of the key analyses focused on Croma India's gross sales performance across fiscal years.

| Fiscal Year | Gross Sales |
|-------------|------------:|
| FY2018 | $1.32M |
| FY2019 | $3.56M |
| FY2020 | $6.50M |
| FY2021 | $23.22M |
| FY2022 | $44.64M |

### Key Insight

Croma India's gross sales increased from **$1.32M in FY2018 to $44.64M in FY2022**.

This represents approximately **33.7× growth** over the period.

The analysis also includes a monthly sales view to understand how gross sales changed across fiscal periods.

---

## Product-Level Sales Analysis

A detailed product-level sales report was created for **Croma India for FY2021**.

The analysis includes:

- Product
- Variant
- Sold Quantity
- Gross Price
- Gross Sales

This allows sales performance to be analyzed at the individual product level rather than only at the overall customer level.

---

# 🌍 2. Customer & Market Analysis

## Market Performance

Market-level analysis was performed to identify markets contributing significantly to overall business performance.

The analysis examines:

- Net Sales
- Market contribution
- Market ranking
- Regional performance

A percentage-of-total calculation was also used to understand each market's contribution to overall net sales.

---

## Customer Performance

Customer-level analysis was performed to identify the customers contributing the most to net sales.

This provides stakeholders with visibility into:

- Top customers
- Customer contribution
- Regional customer performance
- Customer-level sales trends

---

## Market Performance Classification

A reusable stored procedure was created to classify markets into:

- 🥇 **Gold**
- 🥈 **Silver**

The classification is based on total sold quantity for a selected fiscal year.

The procedure also uses **India as the default market** when no market is provided.

```sql
CALL get_market_badge('India', 2021, @level);
SELECT @level;
```

This demonstrates how SQL can be used to create reusable business logic rather than relying only on one-time queries.

---

# 📦 3. Product & Regional Performance

## Top Products by Division

The analysis identifies the **Top N products within each division** based on sold quantity.

A SQL window function was used:

```sql
DENSE_RANK()
```

This allows products to be ranked within their respective divisions.

### Business Value

This analysis helps identify:

- Best-performing products
- Product leaders within each division
- Areas of strong product demand

---

## Top Markets Within Each Region

The analysis also identifies the **Top 2 markets within every region based on gross sales**.

This provides a more granular view of market performance and allows regional leaders to understand which markets are driving sales.

---

# 🚚 4. Supply Chain & Forecast Analysis

## Forecast Accuracy

The supply chain analysis compares actual sales quantities against forecast quantities at the customer level.

The analysis calculates:

- **Actual Sales Quantity**
- **Forecast Quantity**
- **Net Error**
- **Absolute Error**
- **Forecast Accuracy**

### Forecast Accuracy Concept

Forecast accuracy measures how closely the forecasted quantity matches actual sales.

A higher accuracy indicates that forecasted demand is closer to actual demand.

---

## Declining Forecast Accuracy

Customer-level forecast accuracy was compared between:

- **FY2020**
- **FY2021**

The objective was to identify customers whose forecasting performance declined year-over-year.

This provides an opportunity for supply chain teams to investigate changes in demand patterns and improve future forecasting.

---

# ⚙️ SQL Automation

One of the objectives of the project was to make frequently requested analysis more reusable.

## Monthly Gross Sales Stored Procedure

A stored procedure named:

```sql
get_monthly_gross_sales_for_customer
```

was created to generate monthly gross sales for one or multiple customers.

Example:

```sql
CALL get_monthly_gross_sales_for_customer('90002002');
```

Instead of modifying the underlying SQL query every time a different customer needs to be analyzed, the customer code can be passed as an input parameter.

This demonstrates the use of SQL for **reusable and scalable reporting**.

---

# 🧠 SQL Skills Demonstrated

This project provided hands-on experience with a wide range of SQL concepts.

### SQL Fundamentals

- SELECT
- WHERE
- GROUP BY
- ORDER BY
- HAVING
- CASE WHEN

### Joins

- INNER JOIN
- Multi-table joins
- Fact-to-dimension joins

### Aggregations

- SUM()
- COUNT()
- AVG()
- ROUND()
- Percentage calculations

### Advanced SQL

- Stored Procedures
- User Defined Functions
- Variables
- Input Parameters
- Output Parameters
- Window Functions
- DENSE_RANK()
- Subqueries

### Business Analysis

- Fiscal Year Analysis
- Fiscal Quarter Analysis
- Market Analysis
- Customer Analysis
- Product Ranking
- Regional Analysis
- Forecast Accuracy
- Year-over-Year Comparison

---

# 💡 Recommendations

Based on the analysis performed, the following areas could be considered by the relevant business teams:

### 1. Focus on high-growth customers and markets

The strong growth observed in Croma India's gross sales indicates an opportunity to monitor high-growth customers closely and understand the factors driving their performance.

### 2. Monitor market-level sales contribution

Market contribution analysis can help management identify markets that have a significant impact on overall sales and prioritize resources accordingly.

### 3. Review top-performing products

Product-level and division-level rankings can help sales teams understand which products are driving demand and support inventory and sales planning.

### 4. Investigate declining forecast accuracy

Customers showing a decline in forecast accuracy between FY2020 and FY2021 should be reviewed to identify changes in demand patterns or forecasting assumptions.

### 5. Use reusable SQL procedures for recurring analysis

Stored procedures can reduce repetitive query development and make recurring business reports easier to generate.

---

# ⚠️ Assumptions & Caveats

Throughout the analysis, the following assumptions and considerations were applied:

- AtliQ Hardware is a **fictional business case** used for analytics learning and portfolio development.
- Fiscal year calculations follow AtliQ Hardware's **September–August fiscal calendar**.
- Gross sales are calculated using **sold quantity × gross price**.
- Market classification uses the defined sold-quantity threshold from the business requirement.
- Forecast accuracy analysis depends on the availability and quality of actual and forecast quantity data.
- The insights presented are based on the available project dataset and the defined business requirements.

---

# 📁 Repository Structure

```text
AtliQ-Hardware-SQL-Ad-Hoc-Analysis/
│
├── Documentation/
│  └── AtliQ_Hardware_Finance_Supply_Chain_Analytics.pptx
└── README.md
```

---

# 📚 Project Coverage

| Area | Business Requests |
|------|------------------:|
| Finance & Sales | 10 |
| Supply Chain | 2 |
| **Total** | **12** |

---

# 🎓 Acknowledgements

This project was completed as part of my hands-on learning journey with **Codebasics**.

Special thanks to **Dhaval Patel**, **Hemanand Vadivel**, and the **Codebasics team** for providing practical, business-focused learning resources and real-world style analytics projects.

---

# 👨‍💻 About Me

I am a **Data Analytics professional** focused on using data to solve business problems and generate actionable insights.

### Core Skills

**SQL | Power BI | Excel | Python | Data Analytics | Business Intelligence**

---

# 🔗 Connect With Me

**Portfolio:**  
https://codebasics.io/portfolio/Arun-R

**LinkedIn:**  
https://www.linkedin.com/in/arun-r95/

**GitHub:**  
https://github.com/Arun-R95

---

⭐ If you found this project useful, consider giving the repository a **Star**.

---

### 🏷️ Tags

`MySQL` `SQL` `Data Analytics` `Business Analytics`
`Finance Analytics` `Sales Analytics` `Supply Chain Analytics`
`Stored Procedures` `Window Functions` `Forecast Analysis`
`Business Intelligence` `Portfolio Project`
