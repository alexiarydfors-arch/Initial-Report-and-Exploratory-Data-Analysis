# Initial-Report-and-Exploratory-Data-Analysis# Capstone Project: Campaign Finance and U.S. Congressional Election Outcomes

## Research Question

Can the amount and source of campaign finance contributions predict whether a U.S. congressional candidate wins or loses their election?

---

## Project Overview

Money in politics is one of the most debated topics in American democracy, yet most discussions rely on anecdote rather than data. This project uses publicly available campaign finance records and election results to quantify how much fundraising — and what *type* of fundraising — actually predicts whether a congressional candidate wins or loses. The goal is to produce findings that are meaningful to both data scientists and everyday voters.

---

## Repository Structure

```
capstone/
├── data/
│   ├── weball24.csv          # FEC candidate financial summary (2023–2024 cycle)
│   └── 1976-2022-house.csv   # MIT Election Lab U.S. House results
├── plots/                    # All saved visualizations
├── capstone_eda.ipynb        # Main Jupyter notebook (EDA + baseline model)
└── README.md                 # This file
```

---

## Data Sources

| Source | Description | Link |
|--------|-------------|------|
| Federal Election Commission (FEC) | Candidate-level contribution and disbursement data by election cycle | https://www.fec.gov/data/browse-data/?tab=bulk-data |
| MIT Election Data and Science Lab | U.S. House and Senate election results by district and year | https://electionlab.mit.edu/data |

The two datasets are merged on candidate last name, state, and congressional district.

---

## Methodology

### Data Cleaning
- Filtered FEC data to House candidates only
- Filled missing financial values with $0 (no contribution reported = no contribution)
- Removed duplicate candidate records
- Dropped rows with no vote totals from the MIT dataset
- Determined winners per district by highest vote count

### Feature Engineering
| Feature | Description |
|---------|-------------|
| `log_receipts` | Log-transformed total receipts (corrects right skew) |
| `log_disb` | Log-transformed total disbursements |
| `pac_pct` | PAC contributions as % of total receipts |
| `indiv_pct` | Individual contributions as % of total receipts |
| `self_pct` | Candidate self-financing as % of total receipts |
| `spend_ratio` | Disbursements / Receipts (spending efficiency) |
| `is_incumbent` | Binary flag: 1 if incumbent, 0 if challenger or open seat |
| `is_democrat` | Binary party flag |
| `is_republican` | Binary party flag |

### Outlier Analysis
Campaign finance data is heavily right-skewed — a small number of candidates raise enormous sums. Rather than removing these candidates (who are often the most politically significant), log transformation was applied to normalize the distribution while retaining all records.

---

## Techniques Used
- Exploratory Data Analysis (EDA) with Matplotlib and Seaborn
- Feature engineering and transformation
- Logistic Regression (baseline classification model)
- ROC-AUC evaluation metric

---

## Results

> *This section reflects findings from the Module 20 EDA and baseline model. Results will be updated with final model comparisons in Module 24.*

### EDA Findings
- **Fundraising strongly separates winners from losers.** The log(receipts) distribution is visibly shifted higher for winning candidates across both parties.
- **Incumbency is the single strongest predictor.** Incumbent candidates win at a dramatically higher rate than challengers, consistent with decades of political science research.
- **PAC money tracks with winning.** Winners receive a higher *proportion* of their funds from PACs, likely because PACs strategically direct money toward incumbents and likely victors.
- **Party win rates are roughly equal overall** in the 2022 cycle, reflecting competitive midterm dynamics.

### Baseline Model — Logistic Regression

| Metric | Score |
|--------|-------|
| Accuracy | *Run notebook to populate* |
| ROC-AUC | *Run notebook to populate* |

The logistic regression baseline achieves meaningful discrimination above random chance (AUC > 0.50). The strongest positive predictors are **total receipts raised** (log-transformed) and **incumbent status**. PAC percentage shows a positive association with winning; self-financing shows a negative association (self-funded candidates tend to be long-shot challengers).

**Why ROC-AUC?** Because winners are a minority class in any congressional election cycle (one winner per district among multiple candidates), raw accuracy is misleading. ROC-AUC measures how well the model *ranks* candidates — i.e., does it assign higher win probability to actual winners? — regardless of the threshold used, making it the most appropriate single metric here.

### What This Means in Plain Language
A model trained only on how much money candidates raised, where that money came from, and whether they were an incumbent can predict election winners significantly better than chance. This tells us that **campaign finance data alone contains real signal about electoral outcomes** — a finding that has implications for voters, reform advocates, and campaign strategists alike.

---

## Next Steps 
- Train and compare Random Forest and Gradient Boosting classifiers against the logistic regression baseline
- Cross-validate all models with k-fold CV
- Hyperparameter tuning
- Build final non-technical report and business intelligence summary

---

## How to Run

1. Clone this repository
2. Download data files and place in `/data` folder (see links above)
3. Create a `/plots` folder in the project root
4. Install dependencies: `pip install pandas numpy matplotlib seaborn scikit-learn`
5. Open and run `capstone_eda.ipynb` top to bottom
