<p align="center">
  <img src="https://github.com/user-attachments/assets/7e22a616-a356-441d-9364-85d03d0ac62f" width="200" />
</p>

###   <h1 align="center">Credit Risk & Utilization Trends</h1>   

# Executive Summary

Analyzes credit portfolio data to examine the relationship between utilization, repayment behavior, and customer risk. Identifies high-exposure segments and regions, tracks delinquency concentration over time, and surfaces early risk signals that support targeted monitoring and informed portfolio decisions.

<img width="2400" height="1601" alt="Screenshot 2025-12-04 232246" src="https://github.com/user-attachments/assets/cdf3f15f-5865-48b5-93e1-92a624bcb90f" />


<p align="center">
  <a href="https://public.tableau.com/app/profile/adel.kaloti1" target="_blank">
    <img src="https://img.shields.io/badge/View%20Interactive Dashboard on%20Tableau-006699?style=for-the-badge&logo=tableau&logoColor=white"/>
  </a>
</p>

-----

## 🎯 Main Business Questions & KPIs

### 1) Where is portfolio exposure concentrated?
- Total Credit Limit
- Total Current Balance (Exposure)
- Exposure Share by Segment/Region (%)

### 2) Which customer groups show the highest emerging risk?
- Delinquency Rate (by Segment/Region)
- Avg DPD (Days Past Due)
- High Utilization Share (% of accounts above a chosen threshold)

### 3) How does utilization relate to repayment behavior?
- Balance-to-Limit Ratio (Utilization)
- Payment Ratio (Amount Paid / Amount Due)
- Delinquency Rate by Utilization Band

### 4) How does risk evolve across 2023?
- Monthly Delinquency Trend
- DPD Bucket Mix by Month (On Time, 1–30, 31–60, 61+)
- Credit Score Trend by Segment

-------------------------------------------------------

###   <h1 align="center"> Data Structure & ERD</h1> 

To improve data quality, performance, and analytical flexibility, the original Raw_Data table was normalized and split into Four separate tables:

* **customers** — Customer profiles, demographics, income
* **credit_accounts** — Account limits, balances, products
* **billing_payments** — Statements, payments, delinquency signals
* **bureau_monthly_snapshot** — Monthly scores, external risk signals

This structure makes analysis faster, cleaner, and BI-ready.



<p align="center">
  <a href="ETL_SQL.txt">
    <img src="https://img.shields.io/badge/View%20 ETL SQL%20File-ff9933?style=for-the-badge&logo=database&logoColor=white" />
  </a>
  <a href="Data_Cleaning_SQL.txt" >
    <img src="https://img.shields.io/badge/Data%20Cleaning%20SQL-006699?style=for-the-badge&logo=tableau&logoColor=white" />
  </a>
  </p>

<p align="center"> 
<img width="800" height="800" alt="image" src="https://github.com/user-attachments/assets/30dc2c85-4d13-4145-918b-9038dc8bf6eb" />
  </p>
  
-------------


<h1 align="center">📊 Project Measures, KPIs & Calculated Fields</h1>

| KPI / Calc Field | Definition & Calculation |
|---|---|
| Utilization Rate % | Measures credit usage per account. **current_balance / credit_limit** from `credit_accounts`. |
| Delinquency Rate % | Share of late payments within a period. Computed from `billing_payments` using **DPD > 0**. |
| Average DPD | Average delay severity across accounts/portfolio. From `billing_payments`: **DATEDIFF(day, due_date, paid_date)** (or reference date if unpaid). |
| On-Time Payment Rate % | Percentage of payments made on or before due date. From `billing_payments`. |
| Exposure at Risk (EaR) | Total outstanding balance tied to delinquent accounts. **SUM(current_balance)** where **DPD > 0** using `credit_accounts` + `billing_payments`. |
| Credit Score Trend | Monthly direction of credit score movement (improving/deteriorating). From `bureau_monthly_snapshot`. |
| Payment Ratio | Measures repayment strength per statement. **amount_paid / amount_due** from `billing_payments`. |
| Balance-to-Income Ratio | Early stress indicator at customer level. **current_balance / income_monthly** using `credit_accounts` + `customers`. |
| High Utilization Flag | Risk marker for highly leveraged accounts. **CASE WHEN utilization >= 0.80 THEN 1 ELSE 0 END**. |
| Score Band | Risk segmentation by credit score. Example bands: **Poor / Fair / Good / Very Good / Excellent** from `bureau_monthly_snapshot`. |


 <tr>
    <h1 align="center">📐 Data Dimentions</h1>
   <p align="center">

Documenting the dimensions ensures a clear understanding of each DIM, This validation step is essential before moving into performance analysis and KPI modeling.


| Dimension | What it Represents | Why It Matters |
|---|---|---|
| Segment | Customer risk/value grouping | Enables risk prioritization and targeted policy actions across distinct customer profiles. |
| Region | Customer geographic location | Highlights geographic concentration of exposure and delinquency for regional monitoring. |
| Product Type | Credit product category (e.g., credit card, personal loan) | Supports product-level risk comparison and portfolio mix analysis. |
| Employment Status | Customer income stability proxy | Helps explain repayment capacity differences and early risk shifts. |
| Statement Month | Time dimension for billing cycles | Drives trend analysis for delinquency, utilization, and seasonal risk patterns. |
| Credit Score Band | Grouped credit score categories | Simplifies risk segmentation and makes score-driven insights dashboard-friendly. |








