# Health Insurance Actuarial Claims Modeling: Project Knowledge Base

## Section 1: Dataset Acquisition and Data Cleaning (SQL & Excel)

Phase 0 — Dataset Acquisition and Feasibility (3–5 hours) 
Goal: Find datasets that are meaningful to join, not just technically joinable, and assess what cleaning will be required. 
What you do: 
* Search for publicly available health claims datasets. Good sources to check:  
* CMS (Centers for Medicare and Medicaid Services) — they have several public use files including Medicare claims data 
* HCUP (Healthcare Cost and Utilization Project) — hospital inpatient and outpatient data 
* Kaggle health insurance datasets 
* UCI Machine Learning Repository health datasets 
* MEPS (Medical Expenditure Panel Survey) 
* Look for datasets that could represent different claim types — ideally something resembling pharmacy claims, hospital/inpatient claims, and outpatient/professional claims separately. This mirrors how real carrier data is actually structured. 
* Assess joinability: do they share a common member or patient identifier? Do they share diagnosis codes, provider codes, or date fields that could serve as join keys? 
* Pick 2–3 datasets that are reasonably joinable and document why the join is actuarially meaningful, not just technically possible. A join that makes business sense (same members, different claim types) is more impressive than a join that is just possible. 
* Note what cleaning issues are visible upfront — missing values, inconsistent formatting, duplicate records, mixed data types. The messier the better for your purposes. 
 
Phase 1 — Data Cleaning in Excel (10–15 hours) 
Goal: Demonstrate fluency with Excel cleaning tools on raw messy data before touching SQL. 
* 1A — Basic cleaning operations: 
* Import each raw dataset into Excel 
* Sort and filter to understand the shape of the data — how many rows, what columns, obvious anomalies 
* Remove duplicate records using the built in Remove Duplicates tool, then verify with COUNTIFS to confirm 
* Use Text-to-Columns to split any fields that came in combined (common with date fields or name fields in raw exports) 
* Find and Replace to standardize inconsistent entries — for example if gender is coded as M/F in one dataset and Male/Female in another 
* Data Validation to flag out of range values — negative claim amounts, impossible ages, future dates 
* 1B — Formula-based cleaning: 
* TRIM to remove leading and trailing whitespace from text fields 
* CLEAN to remove non-printable characters that sometimes appear in exported data 
* SUBSTITUTE to replace specific characters — for example removing dollar signs or commas from cost fields so they read as numbers 
* VALUE to convert text-formatted numbers to actual numeric values 
* TEXT to reformat date or ID fields to a consistent format across datasets 
* FILTER and SORT for dynamic views of subsets of data 
* UNIQUE to identify distinct values in categorical fields like diagnosis codes or plan types 
* 1C — Lookup and logic formulas: 
* IF statements to create flag columns — for example flagging claims above a certain threshold, flagging members with missing age, flagging records where claim date is before enrollment date 
* SUMIFS and COUNTIFS to build summary aggregations — total claims per member, count of claims by plan type, average cost by age band 
* XLOOKUP to pull fields from one dataset into another using a shared key — this is your first join analog before moving to SQL 
* INDEX + MATCH as an alternative to XLOOKUP, worth knowing both since older Excel versions and many actuarial teams still use INDEX + MATCH 
* 1D — Power Query: 
* Import all datasets into Power Query 
* Apply cleaning steps as a recorded transformation pipeline — this is important because Power Query transformations are reproducible and auditable, which matters in a professional setting 
* Merge queries (Power Query's equivalent of a SQL join) to combine datasets on shared keys 
* Append queries to stack datasets vertically where appropriate (for example combining two years of claims data) 
* Create a source column after appending so each record retains its dataset origin — this is your indicator variable distinguishing pharmacy vs hospital vs outpatient claims 
* Document each transformation step so the pipeline is readable 
 
Phase 2 — Data Integration and SQL Work (15–20 hours) 
Goal: Replicate and extend the Excel work in SQL, demonstrating that you can work in both environments and understand when each is appropriate. 
* 2A — Database setup: 
* Load your cleaned datasets into a SQL environment — SQLite is free and straightforward, DBeaver is a good free GUI, or you can use MySQL or PostgreSQL if you have access 
* Create one table per dataset with appropriate column types — numeric for costs, date types for dates, varchar for codes and identifiers 
* This setup step itself is worth documenting because defining schema and data types is a real task actuaries and analysts do 
* 2B — Basic SQL operations: 
* SELECT statements to explore each table — column names, row counts, sample records 
* WHERE with AND/OR/NOT to filter to specific subsets — active members only, claims within a date range, specific plan types 
* IN to filter to a list of values — specific diagnosis code groups, specific states or regions 
* BETWEEN for range filters — claims between two dates, costs between two thresholds 
* IS NULL and IS NOT NULL to identify and handle missing values — count nulls per column, filter them out or flag them 
* ORDER BY to sort results for inspection 
* 2C — Joins: 
* INNER JOIN to combine datasets on shared member or claim keys — only records present in both tables 
* LEFT OUTER JOIN to keep all records from one table and pull in matching records from another — important for identifying members who appear in one dataset but not another, which has actuarial meaning (a member with pharmacy claims but no medical claims, for example) 
* Join multiple tables in a single query — member demographics table joined to medical claims joined to pharmacy claims 
* After joining, create a source indicator column identifying which claim type each record came from, consistent with what you did in Power Query 
* 2D — Aggregations: 
* GROUP BY with COUNT, SUM, and AVG to build summary tables — total claims per member, average cost by age band, claim count by plan type and month 
* HAVING to filter aggregated results — for example only showing member groups with more than 10 claims, or age bands with average cost above a threshold 
* These aggregations are the SQL equivalent of your pivot tables and will produce the same numbers — showing they match is a good validation step 
* 2E — Subqueries: 
* Use a subquery to filter to members whose total annual claims exceed a threshold — this is the catastrophic claimant identification query and connects directly to your simulation work 
* Use a subquery to compute a member-level average and then join it back to the claim-level table — for example flagging each claim as above or below that member's own average 
* Use a subquery in a HAVING clause to filter groups based on a computed aggregate 
* Write at least one Common Table Expression (CTE) using WITH — CTEs are more readable than nested subqueries and are standard in professional SQL work 
 
Phase 3 — Excel Actuarial and Analysis Tools (8–10 hours) 
Goal: Demonstrate familiarity with the statistical and financial functions actuaries use regularly in Excel. 
* 3A — Statistical functions: 
* STDEV.P and STDEV.S on claim cost columns — compute and explain the difference between population and sample standard deviation in this context 
* VAR.P and VAR.S similarly 
* NORM.DIST and NORM.INV to compute probabilities and quantiles — for example what is the probability a random claim exceeds a given threshold under a normal approximation 
* T.DIST and T.INV for small sample inference — connect this back to your CI simulation work 
* CHISQ.DIST for goodness of fit context — does the observed claim distribution fit a theoretical one 
* 3B — Financial functions: 
* RATE and NPER for basic time value of money calculations — these appear in reserving contexts where future claim payments are discounted 
* These don't need to be deeply developed but showing you know they exist and what they do is worth including 
* 3C — Pivot tables and charts: 
* Build pivot tables summarizing claims by member demographics, plan type, claim type, and time period 
* Add slicers for interactive filtering by year, plan type, and claim category 
* Build pivot charts showing claim cost distribution, PMPM trends over time, and cost by age band 
* These directly feed into your EDA section 
* 3D — What-If analysis: 
* Build a sensitivity table using Data Tables showing how estimated PMPM changes under different assumptions about the catastrophic claim threshold — connects directly to your sensitivity analysis in the simulation 
* Scenario Manager to save and compare low/mid/high cost scenarios 
* 3E — Basic VBA and macros: 
* Record a macro automating your most repetitive cleaning step — for example standardizing column formats across multiple sheets 
* Write a simple VBA sub that loops through sheets and applies a consistent format 
* Set a time cap on this — VBA is a rabbit hole and the goal is familiarity not mastery 
 
Phase 4 — Exploratory Data Analysis (8–12 hours) 
Goal: Produce a thorough EDA that connects the real data back to your simulation distributions and sets up the modeling in Part 6. 
* 4A — Univariate analysis: 
* Distribution of raw claim costs — histogram with appropriate bin width 
* Compute skewness and kurtosis 
* Compute CV (coefficient of variation) 
* Compute proportion of total costs attributable to top 1%, 5%, and 10% of claimants 
* Log-transform claim costs and replot — does the distribution look approximately normal on the log scale 
* Explicitly state which of your five simulated distributions the real data most closely resembles based on skewness, CV, and tail behavior. This is the connection that makes the two halves of your project coherent. 
* 4B — Bivariate and group analysis: 
* Average claim cost by age band — plot and table 
* Average claim cost by gender 
* Average claim cost by plan type if available 
* Claim frequency (claims per member) by the same groupings 
* PMPM by month if date data is available — look for seasonality or trend 
* 4C — Zero and missing value analysis: 
* What proportion of members have zero claims in a given period 
* Are zeros concentrated in specific subgroups — younger members, specific plan types 
* Are there systematic patterns in missing data — is missingness random or associated with specific variables 
* Document all findings because this directly motivates your two-part model in Part 6 
* 4D — Outlier and catastrophic claim analysis: 
* Identify claims above the 95th, 98th, and 99th percentile 
* What diagnosis codes or claim types are driving the extreme values 
* How much do the top 5 claimants contribute to total cost 
* Remove top percentile claims and recompute the mean — show how sensitive PMPM is to catastrophic claims 
* Connect explicitly: this is the real data version of your sensitivity analysis from Part 3 
* 4E — Connection to simulation: 
* Write a short summary paragraph explicitly mapping your EDA findings to your simulation distributions 
* State which distribution the data most resembles and why 
* Note where the real data is more complex than any single simulated distribution — this is your honest setup for why the mixture distribution was the right worst-case scenario to simulate 

---

## Section 2: Confidence Interval Simulation Summary

**Objective:** Test whether standard 95% confidence intervals (CIs) can reliably capture the true mean of skewed claim costs (PMPM), and assess the actuarial consequences when they fail.
 
**Simulation Setup:** Conducted 10,000 Monte Carlo replications across sample sizes ranging from 25 to 500. Evaluated three CI methods: the t-interval, z-interval, and percentile bootstrap.
 
**Distributions Tested:**
* **Normal (mean=1, var=1):** Used purely as a benchmark to verify CLT mechanics.
* **Gamma (mean=1, shape=2):** Moderate skew (1.41), commonly representing standard severity claims like pharmacy.
* **Lognormal (mean=1, log-sd=0.75):** Standard health population claim severity.
* **Lognormal (mean=1, log-sd=1.5):** High-cost, high-risk population with heavy skew (11).
* **Mixture (95/5 Split):** The most realistic distribution. 95% standard claims, 5% catastrophic claims (high mean/high variance) pulling the total average.

**Key Findings:**
* **Normal & Gamma:** Handled well by all methods. T-interval reliably hit ~95% coverage even at small sample sizes.
* **Lognormal (Moderate):** Underperformed at small sample sizes (91.4% coverage at n=25) but converged as sample size grew, relying slowly on the Central Limit Theorem.
* **Lognormal (High-Skew):** Serious breakdown. At n=25, coverage dropped to ~78%, meaning 1 in 4 intervals missed the true mean entirely. It failed to reach 95% coverage even at n=500.
* **Mixture:** Complete failure. Coverage hovered around 63–67% regardless of sample size or method. The extreme variance introduced by the 5% catastrophic tail prevents standard CIs from ever stabilizing.

**Actuarial Consequences:** When CIs fail to capture the true mean, particularly by undercovering on the downside, the point estimate of expected claims is too low. In practice, this directly causes underpriced premiums and inadequate reserves. Because tight MLR regulations govern margins, even a 3-5% miss turns a profitable book into a loss. The simulation proves that standard statistical inference cannot handle catastrophic tail risk alone, explicitly motivating the need for reinsurance (stop-loss), risk margins, and credibility weighting.

---

## Section 3: GLM Modeling, Credibility, and Real Data
 
Part 1 — Intercept-Only GLM Simulation (Direct CI Comparison) 
Goal: Show that likelihood-based inference under the correct distributional assumption outperforms CLT-based inference under misspecification, using the exact same simulation setup from Phase 1. 
What you do: 
* Same 5 distributions, same 5 sample sizes, same 10,000 replications 
* Fit two new methods alongside your existing four:  
* Intercept-only Gamma GLM with log link 
* Intercept-only Lognormal GLM (log-transform response, fit normal linear model, back-transform with retransformation correction factor: E[Y] = exp(μ + σ²/2)) 
* Extract GLM-based confidence intervals carefully: the Gamma GLM estimates log(μ) on the linear predictor scale, so confidence intervals must be back-transformed to the mean scale using exp(), with variance accounted for via the delta method. Skipping this step produces misleading comparisons. 
* Record the exact same metrics: coverage probability, average width, bias, MSE 
* What you expect to find: 
* Normal and Gamma distributions: all methods perform similarly, GLMs offer no dramatic improvement 
* Lognormal Moderate: Gamma and Lognormal GLMs start outperforming CIs, especially at small n, because they model the variance structure correctly not just the mean 
* Lognormal High: clear improvement in coverage from GLMs, most visible at n=25 and n=50 
* Mixture: GLMs improve on CIs but still substantially undercovered — this is the first signal that no single parametric distribution is sufficient, which sets up everything that follows 
* Key comparison table: Five methods side by side (t-interval, z-interval, bootstrap, Gamma GLM, Lognormal GLM) across all distributions and sample sizes on coverage, average width, bias, and MSE. 
* Model selection note: Report AIC for both GLMs on each distribution. This establishes that Gamma GLM fits better on Gamma-generated data and Lognormal GLM fits better on lognormal-generated data. Note that AIC is a relative measure — it tells you which model fits better among the candidates, not whether any model fits well in absolute terms. This distinction matters and should be stated explicitly. 
* Retransformation note: Show what happens to bias in the Lognormal GLM if you omit the correction factor. This illustrates a real and common practical mistake and demonstrates that the correction is not a technicality but a meaningful source of error. 
* Narrative conclusion: The improvement of GLMs over CIs comes from modeling the variance structure correctly, not just the mean. This is likelihood-based inference under the correct distribution outperforming CLT-based inference under misspecification. The mixture result is not just "GLMs improve but undercovered" — it is the first clear signal that the failure mode has shifted from inference failure to model misspecification risk. That distinction carries through the rest of the project. 
 
Part 2 — Covariate GLM Simulation (Extending the Framework) 
Goal: Show that GLMs do something CIs fundamentally cannot — provide a coherent framework for estimating structured, covariate-dependent costs. 
What you do: 
* Modify the simulation to generate data where the true mean varies by member characteristics 
* Simple setup: two covariates, age group (young/middle/old) and risk category (low/high), each with known true coefficients 
* True mean for each member is exp(β₀ + β₁×age + β₂×risk), so true parameter values are known and recoverable 
* Fit Gamma GLM and Lognormal GLM with these covariates 
* Apply retransformation correction factor consistently for the Lognormal GLM 
* Measure: coefficient recovery (bias and MSE of each β), coverage of intervals around each coefficient, and predicted mean accuracy by subgroup 
* Variance function clarification: When comparing Gamma vs Lognormal GLM, make explicit why AIC prefers one over the other — it is not arbitrary. The Gamma assumes Var(Y) ∝ μ², while the Lognormal implies a different variance structure. When the data-generating process matches one of these variance assumptions, AIC will correctly favor that model. This is the deeper reason model selection works when the true distribution is a standard parametric family, and why it breaks down for the mixture. 
* Model selection: 
* Compare Gamma GLM vs Lognormal GLM using AIC and BIC across your 5 distributions 
* Include residual diagnostic plots for both GLMs on one representative distribution — residuals vs fitted and a Q-Q plot 
* Add a predicted vs actual validation by decile bucket: rank predicted means into deciles and compare to observed means within each decile. This is a standard actuarial validation step that goes beyond AIC alone. 
* On the mixture distribution, show that AIC does not consistently favor either model across replications — this is a key result because it means even model selection cannot guide you when the true distribution is a mixture 
* Key insight to make explicit: CIs do not provide a coherent framework for estimating structured, covariate-dependent costs. A CI can be computed for subgroups, but it does not model structure, does not borrow strength across groups, and explodes in variance at small sample sizes. A covariate GLM addresses all three of those problems simultaneously. 
* Narrative conclusion: Covariate GLMs are strictly more powerful than CIs for actuarial purposes. The variance function assumption is what drives model fit, and AIC correctly identifies this when the true distribution is parametric. The mixture distribution is the exception — AIC gives no clear signal, residuals look poor for both models, and the predicted vs actual decile plot will show systematic misfitting in the tail. The failure mode has fully shifted from inference failure to model misspecification risk, and no amount of covariate adjustment fixes a fundamentally misspecified distributional assumption. 
 
Part 3 — Sensitivity Analysis 
Goal: Quantify exactly how fragile GLM estimates are under the mixture distribution and establish the concept of decision stability. 
What you do: 
* Focus on the mixture distribution since that is where instability lives 
* Run three sensitivity analyses: 
* 3A — Distributional sensitivity: Fit Gamma GLM and Lognormal GLM to the same mixture-generated samples. Compare AIC between the two models across replications — show that AIC does not consistently favor either model. The key interpretive statement here is: if two plausible models produce materially different estimates, the estimate is not decision-stable. That is the standard actuaries use in practice, and framing it this way connects your simulation directly to how practicing actuaries think about model risk. 
* 3B — Catastrophic claim sensitivity: Systematically remove or cap claims above the 95th, 98th, and 99th percentiles (use percentiles, not fixed observation counts, to make the analysis scalable and consistent across sample sizes) and refit the GLM. Track how much the estimated mean shifts with each threshold. Report both the shift in the point estimate and the change in interval width — capping claims narrows the interval but introduces deliberate bias. This is equivalent to large claim pooling in practice, and tying it forward explicitly to Part 5 here rather than only at the end makes the actuarial relevance immediate. The tradeoff between variance reduction and bias introduction is the same tradeoff actuaries make when they pool large claims above an excess threshold. 
* 3C — Small sample instability: For the mixture at n=25 and n=50, plot the distribution of GLM-estimated means across your 10,000 replications. Compare not just visually but quantitatively — report the variance of the estimated mean across replications for each distribution at each sample size. The mixture variance should dwarf the others at small n, making the instability concrete rather than just visual. This directly motivates credibility theory as a formal solution. 
* Narrative conclusion: Sensitivity analysis reveals that GLM estimates for mixture-like distributions are not decision-stable — two reasonable model choices produce materially different estimates, and those estimates shift substantially when a handful of extreme claims are moved. This is not a flaw in the GLM, it is an honest reflection of the data. The variance of estimates across replications at small n quantifies exactly why small group estimates cannot stand alone, and that quantification is the direct motivation for credibility weighting. 
 
Part 4 — Credibility Theory 
Goal: Formally manage the uncertainty from limited data by blending group-specific estimates with population-level information. 
What you do: 
* 4A — Introduce the Bühlmann credibility framework: 
* Credibility estimate = Z × (group's own experience) + (1 − Z) × (overall population mean) 
* Z = n / (n + k), where k = within-group variance / between-group variance 
* k measures how much variability comes from differences between groups versus randomness within groups — state this interpretation explicitly so k is not a black box 
* Be explicit about how k is estimated using the analysis of variance approach: within-group variance is estimated from observed variation of individual observations within each group, and between-group variance is estimated from the variance of group means minus the within-group component scaled by group size. Show this calculation clearly. 
* Plot Z vs sample size for the mixture distribution. Connect this plot directly back to your coverage probability plots from Part 1 — the sample sizes where coverage failed most are exactly the sample sizes where Z is lowest and credibility weighting matters most. 
* Important framing: Credibility is not fixing the model. It is managing uncertainty due to limited data. That distinction matters — the GLM is still misspecified for the mixture, and credibility does not change that. What it does is prevent a small group's volatile experience from being taken at face value when the data is too thin to trust. 
* 4B — Simulate credibility-weighted estimates: 
* Apply credibility to group-level predicted means, not raw GLM coefficients — credibility weighting is applied to means or rates, and mixing it with coefficient-level GLM outputs creates an ill-defined hybrid 
* Generate multiple groups from the mixture distribution with different sample sizes 
* Compute three estimates for each group: raw sample mean, GLM predicted mean, credibility-weighted mean 
* Compare bias, MSE, and coverage across all three 
* The credibility estimate should show meaningfully lower MSE than the raw mean or GLM alone at small n 
* 4C — Honest coverage results: 
* Show how credibility weighting affects coverage across replications 
* State clearly that coverage improves but does not reach 95% for the mixture at small n 
* This is the correct and honest answer — the mixture distribution is genuinely hard, and no method fully solves it. Present this as a conclusion, not a failure of the analysis. 
* Narrative conclusion: Credibility theory stabilizes estimates for small groups by formally acknowledging that their own data is insufficient to stand alone. The k parameter is transparent and interpretable. Credibility weighting does not fully solve the mixture problem — it manages the uncertainty rather than eliminating it. In practice this is one of several tools actuaries layer together, which is exactly what Part 5 establishes. 
 
Part 5 — Results, Limitations, and Actuarial Practice 
Goal: Synthesize everything from the simulation into honest conclusions and connect each finding explicitly to the actuarial tool it motivates. 
What you cover: 
* 5A — Unified results summary: 
* One master table: all methods across all distributions and sample sizes on coverage, average width, bias, and MSE 
* Separate summary of AIC comparisons across distributions and methods 
* Narrative walking through what each distribution teaches you in order of increasing skewness 
* One signature result to anchor the section: plot of estimated mean vs sample size across all methods for the mixture distribution. This single plot captures the core message of the entire simulation — all methods struggle, instability persists, and the gap between methods is most visible at small n. 
* 5B — Explicit mapping of findings to actuarial tools: Rather than just listing tools, tie each one directly to a specific result from your simulation: 
* CI coverage failure for skewed distributions → motivates risk margins, which buffer against the estimation uncertainty you quantified 
* Mixture instability and catastrophic tail sensitivity → motivates stop-loss reinsurance, which transfers tail risk that no parametric model can reliably price 
* Sensitivity to extreme claims → motivates large claim pooling, the same bias-variance tradeoff you demonstrated in Part 3B 
* Small n instability → motivates credibility weighting, the formal solution you implemented in Part 4 
* 5C — Honest limitations: 
* No method achieves reliable coverage for the mixture distribution at small n 
* GLMs shift the failure mode from inference failure to model misspecification risk — they do not eliminate the problem 
* AIC cannot reliably guide model selection when the true distribution is a mixture, as your simulation demonstrated 
* The retransformation correction for the lognormal GLM introduces its own uncertainty at small n 
* Credibility theory requires estimating k, which is itself uncertain with thin data 
* The simulation uses known true means — in practice you never know the true mean, which makes all of this harder 
* Unifying statement to carry throughout: All methods struggle when the data-generating process is a mixture with extreme tail risk. This is not a failure of any individual method — it is a property of the problem itself, and it is why actuarial practice layers multiple tools rather than relying on any one. 
 
Part 6 — Real Data Application 
Goal: Apply the modeling framework to actual claims data and show that the distributional challenges from simulation are present in practice. 
* 6A — Exploratory Data Analysis: 
* Histogram of raw claim costs — connect visually to your simulated distributions. State explicitly which simulated distribution the real data most resembles, using skewness, CV, and proportion of total costs from the top 5% of claimants as your evidence. This connection is what makes the two halves of the project feel like one coherent piece. 
* Log-transform the claims and replot — does it look approximately normal on the log scale? This informs which GLM is likely to fit better before you run any model. 
* Identify zeros — report the proportion of members with no claim. This motivates the two-part model with a real number rather than a theoretical argument. Also explain why zero inflation breaks single-distribution assumptions — a Gamma GLM requires strictly positive values, but beyond the technical requirement, a zero observation reflects a fundamentally different process (no utilization) than a positive claim (some utilization occurred), and combining them in one model conflates two distinct phenomena. 
* 6B — Naive estimation: 
* Compute sample mean PMPM and classical t-interval 
* Show sensitivity to extreme claims using percentile-based capping (95th, 98th, 99th) rather than removing a fixed number of observations, consistent with Part 3B 
* Connect explicitly: the instability you simulated in Part 3 is now visible in real data with real dollars attached to it 
* 6C — Single-Part GLM: 
* Fit intercept-only Gamma GLM first, compare to sample mean 
* Add available covariates 
* Compare Gamma GLM vs Lognormal GLM using AIC, BIC, residual diagnostic plots, and predicted vs actual by decile bucket — the same validation approach from Part 2 
* State which model fits better and why, connecting back to which simulated distribution your EDA suggested the data resembles 
* Note that AIC is relative — a lower AIC means better fit among candidates, not a guarantee of correct specification 
* 6D — Two-Part Model: 
* Part 1: logistic regression predicting P(any claim), report coefficients and odds ratios 
* Part 2: Gamma GLM on nonzero claims only, same covariate structure 
* Combine: E[cost] = P(claim) × E[cost | claim] 
* Compare AIC of two-part model vs single-part GLM on the nonzero subset to assess whether the separation adds value beyond just handling zeros 
* Compare final PMPM estimate to the single-part GLM and naive mean 
* 6E — Credibility-Weighted Estimate: 
* Apply Bühlmann credibility across meaningful natural subgroups — age bands, employer groups, plan types — not arbitrary splits 
* Show credibility weights Z for each subgroup explicitly 
* Report the estimated k parameter and show how it was calculated, consistent with Part 4 
* Final comparison table: naive mean, Gamma GLM, two-part model, credibility-weighted estimate side by side with confidence intervals for each 
* 6F — Model Selection Summary: 
* Consolidated AIC and BIC table across all models fit to real data 
* Predicted vs actual decile plot for your preferred model — this is the primary validation visual 
* State explicitly that AIC is relative not absolute, and that your preferred model is the best available candidate, not a guarantee of correct specification 
* State which model you would recommend for pricing use and justify based on AIC, residual diagnostics, decile validation, and practical interpretability 
* 6G — Final interpretation: 
* Which estimate would you use for pricing and why? 
* Where does uncertainty remain, and how would a practicing actuary handle it — mapped explicitly to the tools from Part 5B 
* What additional data or methods would improve estimates further? 
* Closing statement: the real data likely resembles your mixture distribution more than any single parametric distribution, which is exactly why actuarial practice never relies on a single method, and exactly what your simulation demonstrated from the beginning