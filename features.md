### Feature Engineering & Business Rationality**

**Team Name:** Syntax\_Error

We generated 10+ advanced features across three domain categories using parallelized processing to map user churn behavior accurately:

**1\. Days Since Last Transaction (Recency):**  
Days from 2024-03-31 to the last transaction. High inactivity directly flags high churn risk.

**2\. Balance Trend (Financial Drop):**  
(March Mean Balance − January Mean Balance). A negative value signifies the user is pulling out money before leaving.

**3\. End-of-March Balance (Liquidity Snapshot):**  
Average balance of the last 7 days of March. A near-zero balance confirms abandonment.

**4\. Balance Volatility (Balance CV):**  
Coefficient of variation (Standard Deviation / Mean Balance). High values represent irregular application usage.

**5\. Transaction Count (7-Day and 30-Day Windows):**  
Captures short-term and mid-term transaction velocity drop-offs.

**6\. CashOut Ratio and P2P Ratio:**  
Churning users tend to liquidate assets through CashOut and P2P transactions rather than engaging in regular platform activities.

**7\. MerchantPay and BillPay Ratios:**  
High ecosystem integration ("sticky users") is associated with lower churn probability.

**8\. Account Age (Tenure):**  
Number of days since account opening; used to evaluate historical loyalty and long-term engagement.

