###   <h1 align="center">Credit Risk & Utilization Trends</h1>   

# Executive Summary

Analyzes credit portfolio data to examine the relationship between utilization, repayment behavior, and customer risk. Identifies high-exposure segments and regions, tracks delinquency concentration over time, and surfaces early risk signals that support targeted monitoring and informed portfolio decisions.

<img width="2400" height="1600" alt="Screenshot 2025-12-07 195248" src="https://github.com/user-attachments/assets/85cb1d05-5933-490b-8514-ab86310b4d05" />

----------------------------------------------

<img width="2400" height="1600" alt="Screenshot 2025-12-06 224010" src="https://github.com/user-attachments/assets/1997ae5f-6b1d-4319-a0ed-6f09f054f535" />


<p align="center">
  <a href="https://public.tableau.com/app/profile/adel.kaloti1" target="_blank">
    <img src="https://img.shields.io/badge/View%20Interactive Dashboard on%20Tableau-D3B348?style=for-the-badge&logo=tableau&logoColor=white"/>
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
  <a href="EDA_SQL/EDA_SQL.txt">
    <img src="https://img.shields.io/badge/View%20 EDA SQL%20File-ff9933?style=for-the-badge&logo=database&logoColor=white" />
  </a>
  <a href="SQL_Data_Cleaning/SQL_Data_Cleaning.txt" >
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

# Utilization vs Avg DPD per Account

<p align="center">
  <img width="1580" height="644" alt="Screenshot 2025-12-07 195159" src="https://github.com/user-attachments/assets/bf992971-6801-47d7-9ce4-ca7e6e56e15b" />

  </p>

### 🎯Insights

- Most accounts cluster at low-to-mid utilization (~0.05–0.40) with very low Avg DPD (~0–5), suggesting the core portfolio is generally stable.
- The scatter shows weak/unclear linear relationship between utilization and Avg DPD; delinquency appears driven by additional factors beyond usage level alone.
- High-risk outliers exist across the range, but very high utilization (~0.70–1.00) includes several elevated Avg DPD points (often 10–40+), supporting a “high utilization = early warning” flag for closer monitoring.

----------------------------------------

# Risk Concentration by Segment

<p align="center">
<img width="600" height="408" alt="Screenshot 2025-12-07 195225" src="https://github.com/user-attachments/assets/d404d88b-c51d-4a90-8478-9adccd8c09e8" />

  </p>

### 🎯Insights

- **SME-like** holds the highest Exposure at Risk (EaR) at **$3.58M**, making it the primary driver of portfolio risk concentration.
- **Mass** is the second-largest risk bucket at **$2.77M**, indicating that broad retail exposure still contributes materially to overall risk.
- **Young** shows the lowest EaR (**$0.63M**), suggesting lower absolute risk today, but it may still warrant monitoring if growth or utilization spikes.


----------------------------------------

# Monthly Utilization Trend

<p align="center">

<img width="600" height="400" alt="Screenshot 2025-12-07 195217" src="https://github.com/user-attachments/assets/3a6ce47e-c8e6-4149-b999-c9ec96178e92" />


  </p>

### 🎯Insights

- Utilization is **stable around ~38%** across 2023, with an overall average of **38.07%**, suggesting steady portfolio credit usage.
- The **lowest point appears in Jul-23 (37.46%)**, a noticeable dip below the average that could reflect seasonal spending or tighter credit usage mid-year.
- A **quick rebound follows (peak around 38.34%)** and utilization returns close to the average in later months, indicating no sustained upward risk pressure from utilization alone.



----------------------------------------

# DPD Bucket Mix by Quarter

<p align="center">

<img width="600" height="400" alt="Screenshot 2025-12-07 195206" src="https://github.com/user-attachments/assets/14daecb0-dce7-42e2-b285-945d84dc5ef2" />



  </p>

### 🎯Insights

- The payment mix looks **stable across Q1–Q3**, with **On-Time holding ~81.3%–81.7%**, indicating consistent repayment behavior for most of 2023.
- **Q4 shows a slight deterioration**, where **On-Time drops to 79.53%**, suggesting a late-year shift toward higher delinquency.
- The **delinquent share appears more concentrated in Q4** (visible uptick in upper buckets), making **Q4 the key quarter to investigate** by segment/region for emerging risk drivers.
- 
----------------------------------------

# Score Band × Utilization Band

<p align="center">
  
<img width="617" height="400" alt="Screenshot 2025-12-07 195255" src="https://github.com/user-attachments/assets/93ed76dd-c7b6-418d-bec3-12dda2c77911" />
  </p>

### 🎯Insights

- The **Very High utilization (80%+) cells look consistently elevated** across key score bands (e.g., ~40–43% in Excellent/Fair/Good), suggesting heavy limit usage is common even among stronger-score customers.
- The **Low utilization (<30%) values appear comparatively smaller** in most bands, which implies utilization alone may not clearly separate risk/score quality without pairing it with payment/DPD signals.
- The **missing/blank combinations in the “Poor” row** likely indicate low volume or data gaps, so add a view of **account counts per cell** (tooltip or separate bar) to validate the strength of this pattern.

----------------------------------

# 🤝 Stakeholder Recommendations

- **Prioritize EaR-driven actions by segment.** Focus short-term risk reviews and proactive outreach on **SME-like** and **Mass** segments since they carry the largest Exposure at Risk, then validate if this concentration persists after filtering by product type and region.

- **Implement an early-warning rule combining utilization + behavior.** Flag accounts that move into **High/Very High utilization bands** *and* show rising DPD or declining payment ratio, because utilization alone may not explain risk without a behavior shift.

- **Refine credit limit strategy with guardrails.** For strong score bands with persistently high utilization, consider **structured limit increases** only when on-time rate and payment ratio stay healthy; for weaker bands, add **soft caps** or step-up limits based on 2–3 months of consistent payment performance.

- **Reduce delinquency via channel nudges.** Promote **lower-friction payment channels** (e.g., mobile app/auto-pay) for segments/regions with higher late-payment share, and monitor impact on on-time rate and Avg DPD.

- **Build a monthly risk governance cadence.** Track a compact scorecard: **Portfolio Utilization %, Delinquency %, On-Time %, Avg DPD, EaR** with month-over-month deltas, and require explanations for spikes by **region, segment, and product type** to turn dashboard insights into operational decisions.


--------
### 🧰 Tools Kit

<p align="center">
  <img src="tools.png" width="600" />
</p>

<p align="center">
  <!-- Tableau button -->

  <a href="https://public.tableau.com/app/profile/adel.kaloti1" target="_blank">
    <img src="https://img.shields.io/badge/View%20Interactive Dashboard on%20Tableau-D3B348?style=for-the-badge&logo=tableau&logoColor=white"/>
  </a>
  <!-- SQL file button -->
  <a href="EDA_SQL/EDA_SQL.txt">
    <img src="https://img.shields.io/badge/View%20 SQL%20File-006699?style=for-the-badge&logo=database&logoColor=white" />
  </a>
</p>





