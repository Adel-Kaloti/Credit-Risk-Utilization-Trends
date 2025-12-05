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

## 🎯 Main Business Questions

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


---

# CPA by Target Audience

<p align="center">
<img width="400" height="400" alt="Screenshot 2025-12-04 225326" src="https://github.com/user-attachments/assets/a5972385-0692-48a8-b787-aa84dc313570" />
  </p>
  
### 🎯Insights

- **Men 18–24 are the most expensive segment**, with an average CPA of **$4.7K**, almost **3x higher** than the overall “All Ages” benchmark (~$1.7K).
- **Women 35–44 are the most cost-efficient audience**, with an average CPA of only **$0.8K**, making them a strong candidate for budget scaling.
- **Men 25–34 also perform efficiently** at around **$1.0K CPA**, staying below the overall average and offering a cheaper alternative to Men 18–24.
- **Women 25–34 sit in the middle** (~**$2.3K CPA**), suggesting that performance is acceptable but less efficient than older women or Men 25–34.

----



| New Column | Description |
|---|---|
| statement_month | Normalized month key derived from `statement_date` (first day of the month) to simplify monthly trend analysis. |
| utilization | Credit usage ratio per account: `current_balance / credit_limit`. |
| dpd | Days Past Due calculated from `due_date` to `paid_date` (or `2023-12-31` if unpaid). |
| is_delinquent | Flag for late payment: `1` if `dpd > 0`, else `0`. |
| is_on_time | Flag for on-time payment: `1` if `dpd <= 0`, else `0`. |
| dpd_bucket | Categorical delinquency band: `On Time`, `1-30`, `31-60`, `61+`. |
| payment_ratio | Repayment strength per statement: `amount_paid / amount_due`. |
| balance_to_income_ratio | Early stress indicator: `current_balance / income_monthly`. |
| high_util_flag | High leverage flag: `1` if `utilization >= 0.80`, else `0`. |
| score_band | Risk segmentation based on latest score: `Poor`, `Fair`, `Good`, `Very Good`, `Excellent`. |
| exposure_at_risk_amount | Row-level risk exposure: `current_balance` if delinquent, else `0`. |


















