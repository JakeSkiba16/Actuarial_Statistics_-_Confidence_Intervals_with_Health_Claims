# Health Insurance Claims Modeling: From Simulation to Actuarial Practice

## 📊 Project Overview
This project explores the mathematical and practical challenges of estimating health insurance claim costs (Per Member Per Month, or PMPM). Because healthcare costs are heavily right-skewed and driven by a small percentage of catastrophic claims, standard statistical methods often fail to accurately capture risk. 

This repository traces the entire data lifecycle: ingesting and cleaning raw CMS claims data via Excel and SQL, simulating the failure of standard Confidence Intervals on skewed data, and applying actuarial modeling techniques (GLMs, Credibility Theory, and Two-Part Models) to estimate costs accurately.

## 🛠️ Tech Stack
* **Data Processing:** Excel (Power Query, Advanced Formulas), SQL (SQLite, DBeaver)
* **Statistical Modeling & Simulation:** Python/R (Monte Carlo simulations, GLMs, Bootstrapping)
* **Version Control:** Git & GitHub

## 📂 Data Source
* **CMS DE-SynPUF (2008-2010):** A synthetic public use file provided by the Centers for Medicare and Medicaid Services. It mimics real Medicare claims data (Inpatient, Outpatient, Carrier, and Prescription Drug Events) while preserving patient privacy. 

---

## 🔬 Methodology

### Phase 1: Data Pipeline & Exploration (SQL & Excel)
* Ingested and cleaned massive, unstructured health claims data.
* Handled data validation, missing values, and inconsistent formatting.
* Executed complex SQL joins to combine eligibility files with medical and pharmacy claims, establishing a foundational analytical dataset.

### Phase 2: The Failure of Standard Inference (Simulation)
* Conducted a 10,000-iteration Monte Carlo simulation testing standard 95% Confidence Intervals (t-interval, z-interval, bootstrap) across five distributions: Normal, Gamma, Lognormal (Moderate/High), and a 95/5 Lognormal Mixture.
* **Key Finding:** Standard CI methods severely under-cover the true mean in mixture distributions (catastrophic tail risk). Underestimating this mean leads to underpricing premiums and inadequate reserving.

### Phase 3: Generalized Linear Models (GLMs)
* Implemented Intercept-Only and Covariate GLMs (Gamma and Lognormal) to model variance structure correctly.
* Demonstrated the critical importance of the lognormal retransformation correction factor.
* Proved that while GLMs improve upon basic CIs, they still struggle against pure mixture distributions, shifting the problem from inference failure to model misspecification risk.

### Phase 4: Actuarial Solutions (Sensitivity & Credibility)
* **Sensitivity Analysis:** Modeled the impact of large claim pooling (capping at the 95th/99th percentiles) to demonstrate the bias-variance tradeoff inherent in stop-loss reinsurance.
* **Credibility Theory:** Implemented Bühlmann credibility weighting to stabilize small-sample estimates, blending group-specific experience with population means to manage uncertainty.

### Phase 5: Real Data Application & Two-Part Model
* Applied the theoretical framework to the cleaned CMS dataset.
* Deployed a **Two-Part Model**:
  1. Logistic Regression predicting the probability of utilizing any healthcare services.
  2. Gamma GLM predicting the severity of costs for members with >$0 in claims.
* Validated the model using predicted vs. actual decile buckets, representing industry-standard pricing validation.

---

## 📈 Key Actuarial Insights
1. **Risk Margins:** The simulation proves that statistical uncertainty in skewed distributions requires explicit actuarial risk margins.
2. **Reinsurance:** Single parametric models cannot reliably price massive tail events, necessitating large claim pooling and stop-loss mechanisms.
3. **Credibility:** Small employer groups ($n < 50$) exhibit massive volatility; their historical data cannot stand alone without credibility weighting against a broader risk pool.
4. **Zero-Inflation:** Real healthcare data contains a massive spike at zero (healthy members). Splitting frequency and severity into a two-part model prevents the conflation of utilization risk with severity risk.

