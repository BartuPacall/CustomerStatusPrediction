# Customer Status Prediction

**Bartu Paçal & Begüm Başovalı**  
Data Science Course Project — 2025

---

## About

This is our data science course project where we tried to predict whether a customer will **churn**, stay **retained**, or be in **onboarding** using machine learning in R.

We worked with 9 different data sources from a real B2B SaaS company and merged them together to build a single feature-rich dataset.

---

## The Problem

Given historical data about a customer — their support tickets, satisfaction surveys, revenue, engagement, bug reports etc. — can we predict their current status?

**Target classes:**
- `Churn` — customer is at risk of leaving
- `Retained` — customer is active and healthy
- `Onboarding` — customer is new / still getting started

The dataset had ~2,000 customers and was fairly imbalanced (~47% Churn, ~41% Retained, ~12% Onboarding).

---

## Data

We used 9 CSV files that each contained a different piece of the puzzle:

| File | What's in it |
|------|-------------|
| `customer_status_level` | Target variable + customer tier |
| `customer_demographics` | Customer age in months |
| `customer_monthly_recurring_revenue` | MRR |
| `customer_region_and_industry` | Region, vertical, subvertical |
| `customer_revenue_history` | Total historical revenue |
| `customer_satisfaction_scores` | NPS, quality, usability scores |
| `newsletter_engagement` | Newsletter interaction count |
| `product_bug_reports` | Number of bug reports filed |
| `support_ticket_activity` | Ticket count + resolution time |

Not all customers appeared in every table (e.g. only ~200 had newsletter data), so we had to handle a lot of missing joins carefully.

---

## What We Did

**1. Data Cleaning**
Standardized ID column names across all tables, checked for duplicates, and flagged missing values.

**2. EDA**
Looked at distributions, class balance, and how each variable correlated with the target before touching the data.

**3. Feature Engineering**
We created 6 new features based on the correlation analysis:

- `Satisfaction_Composite` — combined score from survey responses
- `Support_Burden` — ticket volume relative to customer age
- `Revenue_per_Month` — MRR efficiency
- `Bug_Frequency` — bug reports over time
- `Engagement_Ratio` — newsletter interaction rate
- `MRR_Ratio` — MRR as a share of total revenue

**4. Preprocessing**
Median imputation for missing numerics, a `has_survey` flag for customers without survey data, one-hot encoding for categoricals, and multicollinearity check (dropped features with |r| > 0.8).

**5. Modeling**
We trained 3 models with 5-fold cross-validation:
- Random Forest
- Multinomial Logistic Regression
- Gradient Boosting Machine (GBM)

---

## Results

| Model | Accuracy | Kappa |
|-------|----------|-------|
| **Random Forest** | **95.2%** | **0.919** |
| GBM | 94.4% | 0.907 |
| Logistic Regression | 91.0% | 0.850 |

Random Forest came out on top and was saved as the final model.

---

## Project Structure

```
Customer_Status_Prediction/
├── Train Datasets/
├── Test Datasets/
├── train.R                    # training pipeline
├── predict.R                  # prediction on test data
├── train_models_final.RData   # saved models + preprocessing objects
└── README.md
```

---

## How to Run

First run the training script:

```r
PROJECT_DIR <- "path/to/Customer_Status_Prediction"
source("train.R")
```

This saves `train_models_final.RData`. Then run predictions:

```r
source("predict.R")
```

---

## Libraries Used

```r
dplyr, tidyr, caret, ggplot2, randomForest, nnet, gbm,
corrplot, pROC, tidytext, psych, scales, knitr, kableExtra
```
