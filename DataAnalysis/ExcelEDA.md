# Comprehensive Medicare Beneficiary Analysis: Actuarial Financials, Clinical Risk Profiling, & Excel Methodology

**Data Source:** CMS DE-SynPUF Dataset (2008-2010)
**Tools Used:** Microsoft Excel (Power Query, Pivot Tables, Calculated Fields, Slicers)
**Focus Area:** Beneficiary Demographic Profiling, Financial Risk Modeling, and PMPM Actuarial Assessment

---

## 1. Executive Summary
This report outlines the exploratory data analysis (EDA) and financial modeling of Medicare beneficiary claims across a three-year period. The primary objective was to establish baseline financial metrics (Average Annual Cost and PMPM), evaluate population health risk, and assess healthcare utilization patterns entirely within Excel. 

A critical finding of this analysis is the stark Pareto distribution of healthcare costs: **Members utilizing hospital facilities represent 54.6% of the population but consume 95.4% of total plan costs**, while doctor-only patients represent roughly 20.8% of the population but drive less than 5% of costs. Furthermore, correcting for "denominator traps" in Per Member Per Month (PMPM) calculations using proper Excel aggregation methods revealed a $14/month variance, translating to a $1.4 million mispricing risk per 100,000 members.

---

## 2. Data Architecture & Excel Methodology (The "How")
To ensure actuarial accuracy, strict data scrubbing and feature engineering pipelines were built using **Excel's Power Query** before any financial metrics were calculated via Pivot Tables.

### Data Cleaning & Standardization in Power Query
* **Payer Scoping:** Excluded Primary Payer responsibility from the analysis. To calculate core Medicare plan liability, the focus was narrowed to Medicare Reimbursement and Beneficiary Out-of-Pocket responsibility columns. HMO and Part D coverage metrics were removed to isolate traditional Part A (Inpatient) and Part B (Outpatient/Carrier) plans.
* **Catching "Ghost Claims":** Established strict rules for eligibility. I identified "Ghost Claims"—instances where a member had $0 or null coverage months but registered claim costs (likely due to delayed billing or HMO routing errors). 
* **Claim Reversals:** Utilized Power Query's standard Number Filters (`>= 0`) to immediately purge negative claim values resulting from hospital overcharge adjustments, preventing skewed averages.

### Feature Engineering
* **Demographic Banding:** Created standard Age Band columns (<65, 65-74, 75-84, 85-94, 95+) to segment the population for Pivot Table trending.
* **Clinical Risk Scoring (`Comorbidity_Count`):** * *Excel Execution:* Replaced the 11 chronic illness text columns with binary flags (1=Yes, 0=No). Created a new calculated column that summed these binary flags per row to generate a `Comorbidity_Count`. This was critical for grouping members by "healthy vs. not healthy" and modeling *clinical synergy*.
* **Utilization Classification (`Care_Level_Flag`):** * *Excel Execution:* Built a cascading "Conditional Column" in Power Query to bucket members into three mutually exclusive groups based on claim dollars: `Hospital User` (If IP > 0 or OP > 0), `Doctor-Only` (If IP/OP = 0 but Carrier > 0), and `Zero-Claim` (Else).

---

## 3. Demographic & Clinical Risk Findings (Pivot Table EDA)
Using Excel Pivot Tables equipped with multi-dimensional Slicers (Year, Comorbidity Count, Age Band, Sex), several clinical and demographic trends were identified.

### Age, Gender, and Demographic Trends
* **The "Under 65" Anomaly:** The <65 age band exhibited abnormally high average claims ($5,068) compared to the 65-74 baseline ($3,967). This is a structural nuance of Medicare, as members under 65 only qualify through severe disability or End-Stage Renal Disease (ESRD). This gap is especially pronounced in the female cohort, where the <65 group costs roughly $1,400 more annually than the 65-74 group. 
* **Temporal Trends:** Average costs rose from $4,922 in 2008 to $5,423 in 2009. *(Note: A sharp decline to $3,193 in 2010 was identified, which is a known data artifact driven by partial-year truncation in the SynPUF dataset).* Beneficiary responsibility remained constant at ~20% of total costs, indicating no major shifts in plan benefit design.

### Clinical Synergy & Condition Volatility
The engineered `Comorbidity_Count` metric perfectly illustrated the exponential nature of healthcare costs.
* Members with 0 conditions averaged an exceptionally low annual cost of ~$250. 
* As condition counts increase, costs grow multiplicatively rather than additively due to clinical synergy (illnesses compounding one another).
* **Statistical Volatility at High-Risk Tiers:** For members with 1 condition, costs were stable (tightly clustered between $1,000 and $2,000). For members with 10+ conditions, sample sizes drop and costs become hyper-volatile (ranging from $13,000 to $150,000). Pricing for these catastrophic cohorts requires advanced actuarial smoothing techniques, such as **Credibility Theory**, to adjust rate setting rather than relying on standard confidence intervals.

---

## 4. Actuarial Financials & PMPM Modeling
A clear methodological delineation was made between metrics used for retrospective analysis (Average Annual Cost) and prospective rate-setting (PMPM).

### The PMPM "Denominator Trap"
Calculating PMPM requires extreme precision regarding a member's *Exposure* (months of coverage). Two calculation methods were tested in Excel to highlight a common analytical pitfall:
* **Method 1 (The Flawed Average of Averages):** Setting the Pivot Table value aggregation to "Average" on the monthly cost column. This incorrectly gives equal weight to a 1-month member and a 12-month member, artificially dragging the total PMPM down to $421.
* **Method 2 (The Actuarial Standard):** Designing the Pivot Table (or Calculated Field) to divide the **Sum** of total population claims by the **Sum** of total population coverage months (`Total $ / Total Exposure`). This properly weights members by their time on the plan, resulting in a true PMPM of **$435**.
* **Business Impact:** Using Method 1 would result in underpricing premiums by $14 per member per month. For a plan of 100,000 members, this single mathematical error would generate a **$1.4 million annual loss**.

### PMPM Cost Drivers (2008 Baseline)
Using Method 2, the $435 PMPM was broken down by utilization type to highlight where plan dollars are being spent:
* **Inpatient (Part A):** $219 PMPM
* **Carrier / Physician (Part B):** $139 PMPM
* **Outpatient (Part B):** $77 PMPM
* *Total Part B (OP + Carrier):* $216 PMPM

---

## 5. Healthcare Utilization & The Pareto Principle
To create actionable risk-management insights, the population was segmented by facility utilization. 

### Resolving Data Discrepancies (The Excel Confusion Matrix)
During analysis, a discrepancy appeared between the original binary `Zero_Claims` column and the new `Care_Level_Flag` text column. 
* *Excel Execution:* A Pivot Table was utilized as a **Confusion Matrix** (Rows: Old Binary Flag, Columns: New Classification Flag, Values: Count of ID). Double-clicking the intersection errors allowed for immediate drill-down into the raw data, which successfully identified the "0-Month Ghost Claims" that were subsequently filtered out in Power Query.

### The Zero-Claim Population (Preventative Baseline)
In 2008, 24.5% of the total population generated $0 in claims. 
* **Condition Correlation:** By cross-referencing the `Comorbidity_Count` and Zero-Claim flags, it was found that **99.15%** of all zero-claim members have 0 chronic conditions.
* **Healthy Utilization:** Among the entirely healthy population (0 conditions), roughly 60% had zero claims. This represents a highly profitable risk pool that subsidizes the sickest members. 

### Hospital Users vs. Doctor-Only (The Pareto Split)
Isolating the members who utilized medical services revealed a drastic disparity in financial footprint:
* **Doctor-Only Users:** Made up **20.81%** of the population but accounted for **<5%** of the total plan cost, boasting a highly manageable average annual cost of **$1,071.75**.
* **Hospital Users (IP or OP):** Made up **54.66%** of the population but consumed **95.47%** of the total plan dollars, with a staggering average annual cost of **$8,597.49**. 

**Strategic Takeaway:** The data mathematically proves that escalating from an outpatient physician setting to a hospital facility is the single largest driver of financial loss. 

---
## 6. Next Steps
With the wide-data beneficiary profiling and high-level PMPM baselines completed seamlessly in Excel, the next phase of the project will involve migrating transactional claims data (Long Data) into SQL. This will bypass Excel's row-limit constraints on transactional data to perform Episode-of-Care analytics, including Cost Per Admission (Unit Cost) and Average Length of Stay (ALOS) via SQL JOINs.