# **PROJECT REPORT: ADVANCED CHURN PREDICTION SYSTEM VIA MULTI-MODEL K-FOLD ENSEMBLE**

**Team Name:** Syntax\_Error  
**Course/Competition:** NSU Datathon Phase 2  
**Date:** June 15, 2026

### **1\. EXECUTIVE SUMMARY**

This report presents a high-fidelity machine learning pipeline developed to forecast customer churn for FictiPay during the April 2024 validation window. Digital wallets operate in high-churn environments where identifying early disengagement signals is critical for maximizing customer retention.  
By restructuring and merging massive, multi-gigabyte KYC registers, daily balance histories, and raw transaction logs, we engineered a multi-dimensional behavioral feature set. To avoid memory overhead and handle big data constraints seamlessly, we utilized parallel computing frameworks. Our final solution relies on a robust 5-Fold Stratified Cross-Validation framework and a weighted 3-way Ensemble Blend consisting of LightGBM, XGBoost, and CatBoost classifiers, achieving a highly stable local Out-of-Fold (OOF) AUC score of **0.9842** and securing a competitive position on the leaderboard.

### **2\. DATA PIPELINE & PREPROCESSING**

The primary challenge of this dataset was its sheer scale and high-frequency nature. Standard row-by-row data manipulation techniques were insufficient due to memory limitations.

* **Dask Parallelized Framework:** We leveraged Dask DataFrames to load, lazy-evaluate, and compute complex group-by aggregations across millions of raw rows without encountering out-of-memory errors.  
* **Master Ledger Synthesis:** A master base dataframe was constructed by isolating all unique customer accounts (ACCOUNT\_ID). Data alignment was tightly maintained by performing structured left-joins from the target universe to the processed transaction and balance tables.  
* **Missing Value Imputation:** Missing fields arising from completely inactive users across specific time-frames were systematically imputed with logical zeros (0) or structural baselines to prevent mathematical divergence in tree-based splits.

### **3\. FEATURE ENGINEERING & RATIONALITY**

Complying with the core hacakthon directives, we engineered more than 10 advanced behavioral features designed to capture specific stages of user attrition. These are broadly categorized into three distinct business pillars:

#### **A. Recency and Inactivity Dynamics**

* days\_since\_last\_tx**:** Captures the number of days elapsed between the reference date (2024-03-31) and the user's latest transaction. A wider gap serves as a direct proxy for systemic detachment.  
* trx\_count\_7d **&** trx\_count\_30d**:** Sliding window transaction frequencies. Computing the ratio between these two windows reveals immediate velocity drop-offs, identifying users who were historically active but abruptly went silent in late March.

#### **B. Wallet Liquidity & Depletion Trends**

* balance\_trend**:** Computed as the mathematical delta between March Mean Balance and January Mean Balance. A steeply negative trend acts as a definitive signal that the user is systematically draining their account before completely abandoning the application.  
* bal\_end\_of\_march**:** A focused snapshot isolating the average balance during the final 7 days of March. Users leaving the platform typically reduce this specific boundary to zero.  
* bal\_cv **(Coefficient of Variation):** Calculated as Standard Deviation divided by Mean Balance. High financial volatility highlights irregular, uncommitted application utilization.

#### **C. Ecosystem Stickiness and Volume Allocation**

* cashout\_ratio **&** p2p\_ratio**:** Tracks the proportion of outbound funds leaving through peer-to-peer or cash-out channels versus lifestyle services. Churning users skew heavily toward total asset liquidation.  
* trx\_type\_MerchantPay **&** trx\_type\_BillPay**:** High interaction with merchants and utility bills signals intense lifestyle ecosystem integration, marking these customers as structurally sticky.

### **4\. VALIDATION STRATEGY & TESTING METHODOLOGY**

Relying on a single train-test split introduces severe variance risks and public leaderboard overfitting. To ensure maximum generalization on the hidden private test set, we instituted a strict **5-Fold Stratified Cross-Validation** strategy.

\[Full Training Dataset\]   
   └── Fold 1: 80% Train / 20% Stratified Validation ➔ LGBM \+ XGB \+ CatBoost  
   └── Fold 2: 80% Train / 20% Stratified Validation ➔ LGBM \+ XGB \+ CatBoost  
   └── Fold 3: 80% Train / 20% Stratified Validation ➔ LGBM \+ XGB \+ CatBoost  
   └── Fold 4: 80% Train / 20% Stratified Validation ➔ LGBM \+ XGB \+ CatBoost  
   └── Fold 5: 80% Train / 20% Stratified Validation ➔ LGBM \+ XGB \+ CatBoost

Stratification preserves the original minority churn class distribution across all five folds. The stable metrics obtained across each validation split guarantee that the model is learning generalized behavioral patterns rather than noise:

* **Overall OOF LightGBM AUC:** 0.98399  
* **Overall OOF XGBoost AUC:** 0.98397  
* **Overall OOF CatBoost AUC:** 0.98420

### **5\. ENSEMBLE ARCHITECTURE & BLENDING**

To squeeze out every ounce of predictive power, we moved away from standalone architectures. We engineered a meta-blended ensemble consisting of three powerful gradient-boosting variations, each capturing distinct mathematical interactions:

1. **LightGBM (30% Weight):** Optimized for broad leaf-wise tree extensions, capturing global feature hierarchies instantly.  
2. **XGBoost (30% Weight):** Rigorously bound by depth constraints (max\_depth=6) to avoid local overfitting.  
3. **CatBoost (40% Weight):** Specially optimized for high-cardinality splits and advanced symmetric tree growth, leading our individual validation baselines.

**Blending Formula:**  
$$\\text{Final Probability} \= (0.30 \\times \\text{LGBM}) \+ (0.30 \\times \\text{XGB}) \+ (0.40 \\times \\text{CatBoost})$$  
This weighted configuration effectively reduces model variance, resulting in highly precise decimal probabilities optimized directly for the Area Under the ROC Curve (AUC-ROC) metric.

### **6\. EXPLAINABILITY & BUSINESS ACTIONABILITY**

Rather than deploying a non-transparent "black-box" asset, we mapped model attribution through **SHAP (SHapley Additive exPlanations)** values. The explainer revealed that temporal recency (days\_since\_last\_tx), sliding window count contractions, and account liquidity depletion (balance\_trend) hold the highest global feature importance.

#### **Data-Driven Strategic Recommendations:**

* **Automated Re-engagement Triggers:** FictiPay should configure automated triggers that flag accounts immediately when days\_since\_last\_tx exceeds 7 days alongside a negative balance\_trend.  
* **Targeted Incentives:** Customers flagging high churn probabilities driven by low utility bill footprints should be channeled into high-yield cashback campaigns for BillPay or MerchantPay to enforce structural lifestyle stickiness.

**\[End of Report\]**  
