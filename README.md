# 📉 Telco Customer Churn Analysis

> End-to-end exploratory analysis of telecom customer churn using Python. Covers data cleaning, univariate & bivariate EDA, feature engineering (Phone Setup + Internet Persona), statistical testing (Welch's T-Test), and churn risk profiling across contract type, tenure, internet service, and add-on behaviour.

---

## 📌 Table of Contents
- [Business Problem](#-business-problem)
- [Dataset](#-dataset)
- [Project Structure](#-project-structure)
- [Data Cleaning](#-data-cleaning)
- [Feature Engineering](#-feature-engineering)
- [Key Findings](#-key-findings)
- [Tools Used](#-tools-used)
- [How to Run](#-how-to-run)

---

## 🎯 Business Problem

A telecom company is losing **26.5% of its customers annually** — above the industry benchmark of 15–25%. The company has no structured understanding of:

- **Who** is churning — which demographic and service profiles are highest risk
- **Why** they are churning — whether it is price, service quality, contract structure, or product gaps
- **When** churn happens — at which point in the customer lifecycle risk is highest
- **What** can be done — which interventions would have the highest retention impact

Without this understanding, the retention team is applying generic campaigns to all customers equally — wasting budget on low-risk customers and missing the highest-risk cohorts entirely.

**This analysis answers all four questions using descriptive and diagnostic techniques on 7,043 customer records.**

---

## 📂 Dataset

| Property | Detail |
|---|---|
| Source | IBM Telco Customer Churn Dataset |
| Rows | 7,043 customers |
| Columns | 21 features |
| Target variable | `Churn` (Yes / No) |
| Domain | Telecommunications / Subscription Services |

**Feature categories:**
- **Demographics** — Gender, Age (SeniorCitizen), Partner, Dependents
- **Services** — PhoneService, MultipleLines, InternetService, OnlineSecurity, OnlineBackup, DeviceProtection, TechSupport, StreamingTV, StreamingMovies
- **Contract & Billing** — Contract type, PaperlessBilling, PaymentMethod, MonthlyCharges, TotalCharges
- **Tenure** — Number of months as a customer

---

## 🗂 Project Structure

```
telco-churn-analysis-python/
│
├── Customer_Churn_Final_WithInsights_eda.ipynb   # Main analysis notebook
├── Customer Churn.csv                             # Dataset
└── README.md
```

---

## 🧹 Data Cleaning

Three specific issues were identified and resolved before analysis:

| Issue | Detail | Fix |
|---|---|---|
| `TotalCharges` stored as object | 11 rows contained blank strings `' '` instead of null — discovered via Excel pre-audit | Stripped whitespace, replaced with `'0'`, cast to `float64` |
| `SeniorCitizen` encoded as int | Stored as `0/1` instead of readable `Yes/No` | Mapped to `'yes'`/`'no'` for consistency across all categorical plots |
| `TotalCharges` right-skew noted | Distribution heavily skewed — most customers are relatively new | Documented and accounted for in interpretation |

> **Note:** An initial data audit was conducted in Excel before Python cleaning — converting data to a table format to spot blank strings and structural anomalies that are harder to identify programmatically.

---

## ⚙️ Feature Engineering

Two original features were created to consolidate fragmented service columns into meaningful business groupings:

### 1. `Phone_Setup`
Consolidates `PhoneService` + `MultipleLines` into a single, readable column:

| Original values | Phone_Setup label |
|---|---|
| PhoneService = No | `No Phone Service` |
| PhoneService = Yes, MultipleLines = No | `Single Line` |
| PhoneService = Yes, MultipleLines = Yes | `Multiple Lines` |

**Why:** Keeping two separate columns creates redundancy. A single consolidated feature is cleaner for analysis and directly answers the business question: "what phone plan does this customer have?"

### 2. `Internet_Persona`
Classifies every internet customer into one of four personas based on their add-on combination:

| Persona | Definition |
|---|---|
| `Barebones` | Internet only — zero add-ons |
| `Security-Focused` | Has OnlineSecurity and/or TechSupport |
| `Entertainment-Focused` | Has StreamingTV and/or StreamingMovies |
| `Power User` | Has both security and streaming add-ons |

**Why:** Treating each add-on as a separate binary variable misses the behavioural pattern. Customers don't buy add-ons randomly — they buy them as bundles. The Persona captures the *type* of customer, not just a list of features.

---

## 📊 Key Findings

### Churn Overview
- Overall churn rate: **26.5%** (1,869 of 7,043 customers)
- Industry benchmark: 15–25% — this company is at the higher end

### Contract Type — Strongest Churn Predictor
| Contract | Churn Rate |
|---|---|
| Month-to-Month | ~42% |
| One Year | ~11% |
| Two Year | ~3% |

Month-to-Month customers churn at **14× the rate** of Two-Year customers. Contract conversion is the single highest-leverage retention intervention available.

### Tenure — Early Months Are Critical
- **0–3 month cohort: 50%+ churn rate** — more than 1 in 2 brand-new customers leave
- By month 14, churn rate drops below global average
- By month 48+, churn approaches zero
- **Every month a customer stays, their probability of churning permanently decreases**

### Internet Service — The Fiber Optic Problem
| Internet Service | Churn Rate |
|---|---|
| No Internet | ~7% |
| DSL | ~19% |
| Fiber Optic | ~42% |

Fiber Optic customers pay ~£40/month more than DSL customers **and** have fewer add-ons on average — creating a perceived value problem that drives churn.

### Add-ons — The Most Actionable Finding
| Add-on Count | Churn Rate |
|---|---|
| 0 add-ons | ~50%+ |
| 1–2 add-ons | ~30% |
| 3–4 add-ons | ~18% |
| 5–6 add-ons | <10% |

Every additional add-on meaningfully reduces churn. OnlineSecurity and TechSupport — the two add-ons most linked to lower churn — are subscribed to by **fewer than 35% of internet customers**.

### Internet Persona — Barebones Is the Highest Risk
- **Fiber Optic Barebones** (no add-ons) is the single highest-risk customer persona in the dataset
- **Power Users** (security + streaming) have the lowest churn across both DSL and Fiber Optic

### Demographics
- **Gender** has virtually no impact on churn (~26% for both male and female) — should be deprioritised in retention strategy
- **Senior citizens** churn at ~41% vs ~24% for non-seniors
- **Senior + No Partner + No Dependents** is the highest-risk demographic combination
- **Customers without dependents** churn at ~31% vs ~17% for those with dependents

### Statistical Validation — Welch's T-Test
Churned customers pay **~£74/month on average** vs ~£61 for retained customers — a £13 gap. Welch's T-Test confirms this difference is **statistically significant (p < 0.05)** and not random variation. However, correlation analysis shows the relationship is moderate (~0.19) — price is a contributing factor, not the primary driver. Contract type and internet service are stronger predictors.

### Confounding Variable — Electronic Check
Electronic Check users appear to have the highest churn rate (~45%). However, this is a confounding variable — Electronic Check users are disproportionately on Month-to-Month contracts. When controlled for contract type, payment method loses most of its predictive power.

---

## 🏆 Highest-Risk Customer Profile

Based on all analysis, the highest-risk customer profile is:

> **Month-to-Month contract + Fiber Optic internet + Zero add-ons + Tenure under 6 months + Senior citizen**

This cohort has a churn rate significantly above 50% and represents the most urgent retention target.

---

## 🛠 Tools Used

| Tool | Purpose |
|---|---|
| Python 3.x | Core analysis language |
| Pandas | Data manipulation and cleaning |
| NumPy | Numerical operations |
| Matplotlib | Static visualisations |
| Seaborn | Statistical plots — countplots, heatmaps, violin plots, FacetGrid |
| Plotly Express | Interactive visualisations |
| SciPy (stats) | Welch's T-Test for statistical validation |
| Microsoft Excel | Pre-analysis data audit |
| Google Colab | Cloud notebook environment |

---

## ▶️ How to Run

1. Clone the repository:
```bash
git clone https://github.com/dhritisinghsisodia/telco-churn-analysis-python.git
```

2. Open the notebook in Google Colab or Jupyter:
```
Customer_Churn_Final_WithInsights_eda.ipynb
```

3. Upload `Customer Churn.csv` to your Colab environment or update the file path in Cell 4:
```python
df = pd.read_csv('/content/Customer Churn.csv')
```

4. Run all cells sequentially — all outputs and insight cells are pre-populated.

---

## 📬 Connect

**Dhriti Singh Sisodia**
[GitHub](https://github.com/dhritisinghsisodia) | [LinkedIn](https://www.linkedin.com/in/dhriti-singh-sisodia-728a56237/)
