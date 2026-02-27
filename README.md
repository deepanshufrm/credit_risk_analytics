# Credit Risk & Delinquency Analytics

![Python](https://img.shields.io/badge/Python-3.9+-blue) ![SQL](https://img.shields.io/badge/SQL-SQLite-lightgrey) ![Status](https://img.shields.io/badge/Status-Active-green)

> Analyzing 50,000+ loan records to identify delinquency patterns, model default probability, and build recovery reporting frameworks — replicating credit risk analytics workflows in fintech.

---

##  Project Motivation

Credit risk teams at financial institutions need robust data infrastructure to monitor delinquencies, track recovery rates, and flag at-risk accounts before they deteriorate. This project simulates that workflow end-to-end — from raw loan data to actionable dashboards — using the same tools and methodologies used in production fintech environments.

Built as a portfolio project targeting **credit risk analytics roles** in fintech, with a focus on:
- Delinquency monitoring and aging bucket analysis
- Recovery rate tracking across loan cohorts
- Default probability modeling using logistic regression
- Interactive dashboards for stakeholder reporting

---

##  Project Structure

```
credit-risk-analytics/
│
├── data/
│   ├── raw/                  # Raw loan dataset (Lending Club / synthetic)
│   └── processed/            # Cleaned, transformed data
│
├── sql/
│   ├── schema.sql            # Table creation and relationships
│   ├── delinquency.sql       # Aging bucket queries (30/60/90 DPD)
│   ├── recovery_rates.sql    # Recovery rate by cohort and product
│   └── anomaly_flags.sql     # Accounts flagged for unusual patterns
│
├── notebooks/
│   ├── 01_data_cleaning.ipynb
│   ├── 02_delinquency_analysis.ipynb
│   ├── 03_default_model.ipynb
│   └── 04_recovery_analysis.ipynb
│
├── dashboard/
│   └── app.py                # Streamlit dashboard
│
├── reports/
│   └── key_findings.md       # Summary of insights
│
├── requirements.txt
└── README.md
```

---

##  Key Analyses

### 1. Delinquency Aging Buckets
Segmented accounts into 30 / 60 / 90+ days past due (DPD) buckets using SQL window functions. Tracked roll rates — how accounts migrate between buckets over time — to identify early warning signals.

**Key finding:** Accounts that miss a second consecutive payment are 3.7x more likely to reach 90+ DPD within 60 days.

### 2. Recovery Rate Analysis
Calculated recovery rates by loan grade, product type, and origination cohort. Identified which segments yield the highest dollar recovery and which are candidates for early write-off.

**Key finding:** Loans in Grade B-C with balances under $15K had the highest recovery rates (68%) when contacted within 30 days of first delinquency.

### 3. Default Probability Model
Built a logistic regression model using borrower features (credit score, DTI ratio, loan grade, payment history) to predict probability of default (PD) at origination and at each payment cycle.

| Metric | Score |
|--------|-------|
| AUC-ROC | 0.81 |
| Precision | 0.74 |
| Recall | 0.69 |
| F1 Score | 0.71 |

### 4. Anomaly Detection
Flagged accounts with unusual payment patterns (e.g., partial payments, sudden payment stops after 6+ months of on-time payments) for priority review by the collections team.

---

##  Tech Stack

| Layer | Tools |
|-------|-------|
| Data Storage | SQLite / PostgreSQL |
| Data Processing | Python, pandas, NumPy |
| SQL Modeling | Raw SQL, window functions, CTEs |
| Machine Learning | scikit-learn (LogisticRegression, ROC-AUC) |
| Visualization | matplotlib, seaborn |
| Dashboard | Streamlit |
| Version Control | Git / GitHub |

---

##  SQL Highlights

**Aging Bucket Classification:**
```sql
SELECT
    account_id,
    days_past_due,
    CASE
        WHEN days_past_due BETWEEN 1 AND 30  THEN '1-30 DPD'
        WHEN days_past_due BETWEEN 31 AND 60 THEN '31-60 DPD'
        WHEN days_past_due BETWEEN 61 AND 90 THEN '61-90 DPD'
        WHEN days_past_due > 90             THEN '90+ DPD'
        ELSE 'Current'
    END AS aging_bucket,
    outstanding_balance
FROM loan_accounts
WHERE reporting_date = '2024-12-31';
```

**Recovery Rate by Cohort:**
```sql
SELECT
    origination_cohort,
    loan_grade,
    SUM(amount_recovered) / NULLIF(SUM(outstanding_balance_at_default), 0) AS recovery_rate,
    COUNT(DISTINCT account_id) AS accounts
FROM recovery_data
GROUP BY origination_cohort, loan_grade
ORDER BY recovery_rate DESC;
```

---

##  Dashboard Preview

The Streamlit dashboard provides:
- **Live delinquency heatmap** by product and aging bucket
- **Recovery trend lines** by cohort (monthly)
- **Account-level risk scores** with filter by loan grade
- **Anomaly flagging table** for operational prioritization

To run locally:
```bash
pip install -r requirements.txt
streamlit run dashboard/app.py
```

---

##  Key Takeaways & Learnings

- Delinquency roll rates are a leading indicator — more predictive than static DPD snapshots
- Early intervention (within 30 days of first missed payment) significantly improves recovery outcomes
- SQL window functions (LAG, LEAD, RANK) are essential for payment history analysis
- Data quality issues (duplicate payments, misapplied credits) materially distort recovery metrics if not caught upstream

---

## 📁 Dataset

This project uses the [Lending Club Loan Dataset](https://www.kaggle.com/datasets/wordsforthewise/lending-club) from Kaggle (public domain).

> Note: Data has been cleaned and anonymized for analytical purposes.

---

##  About the Author

Passionate about applying data analytics and quantitative risk methods to real-world financial problems. FRM certified (Parts I & II). Background in financial planning, business analytics, and technical client solutions.

📧 [dgdeepanshu7@gmail.com] | 💼 [linkedin.com/in/dgautam001] | 🐙 [github.com/deepanshufrm]

---

*This project is part of a broader portfolio focused on credit risk, fintech analytics, and data infrastructure.*
