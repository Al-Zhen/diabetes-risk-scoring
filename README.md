# Diabetes Risk Scoring Model
### A Healthcare Data Science Project

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.3+-orange?logo=scikit-learn)
![SQL](https://img.shields.io/badge/SQL-SQLite-lightgrey?logo=sqlite)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## Business Problem

> *"Can we identify high-risk patients before they are formally diagnosed with Type 2 Diabetes, enabling earlier clinical intervention?"*

Early identification of diabetes risk can:
- Reduce long-term complications (neuropathy, retinopathy, cardiovascular disease)
- Lower hospital readmission rates by 15–20%
- Support preventive care programs and generate significant cost savings
- Enable targeted population health management

---

## Project Overview

This project builds a **clinical risk scoring model** that takes patient diagnostic data and outputs a **0–100 Risk Score** with clinical tier classification (Low / Moderate / High / Very High), along with actionable clinical recommendations.

**Comparable to real-world tools like:** FINDRISC score, ADA Diabetes Risk Test, and ML-based EHR alert systems used in Epic and Cerner.

---

##  Dataset

**Source:** [Pima Indians Diabetes Database — UCI / Kaggle](https://www.kaggle.com/datasets/uciml/pima-indians-diabetes-database)

| Feature | Description |
|---------|-------------|
| `Pregnancies` | Number of pregnancies |
| `Glucose` | Plasma glucose concentration (mg/dL) |
| `BloodPressure` | Diastolic blood pressure (mm Hg) |
| `SkinThickness` | Triceps skin fold thickness (mm) |
| `Insulin` | 2-hour serum insulin (μU/mL) |
| `BMI` | Body mass index (kg/m²) |
| `DiabetesPedigreeFunction` | Genetic risk score from family history |
| `Age` | Age in years |
| `Outcome` | 1 = Diabetic, 0 = Non-diabetic |

**768 patients · 8 clinical features · ~35% diabetes prevalence**

---

## Project Structure

```
diabetes-risk-scoring/
│
├── diabetes_risk_scoring.ipynb   # Main analysis notebook
├── README.md                     # This file
│
└── outputs/
    ├── feature_distributions.png
    ├── correlation_heatmap.png
    ├── model_evaluation.png
    ├── feature_importance.png
    └── risk_score_distribution.png
```

---

##  Methodology

### 1. SQL Exploration
Used `sqlite3` to query patient data — simulating real healthcare data warehouse workflows (Redshift, Snowflake, BigQuery).

### 2. Exploratory Data Analysis
- Feature distributions by outcome
- Correlation analysis
- Missing data patterns (zeros encoded as missing in clinical fields)

### 3. Data Cleaning
- Replaced physiologically impossible zeros with `NaN`
- Median imputation (stratified by outcome)

### 4. Feature Engineering
| New Feature | Logic |
|-------------|-------|
| `Glucose_BMI` | Interaction term (glucose × BMI) |
| `Age_Pregnancies` | Interaction term |
| `BMI_category` | Underweight / Normal / Overweight / Obese |
| `Glucose_category` | Normal / Pre-diabetic / Diabetic range |
| `Age_group` | Binned age cohorts |

### 5. Models Trained
| Model | Description |
|-------|-------------|
| Logistic Regression | Interpretable baseline; common in clinical settings |
| Random Forest | Handles non-linearity and feature interactions |
| Gradient Boosting | Best overall performance on tabular health data |

All models use sklearn **Pipelines** with imputation + optional scaling, and are evaluated via **5-fold stratified cross-validation**.

---

## 📈 Results

| Model | AUC-ROC | CV AUC | Recall (Diabetic) | F1 (Diabetic) |
|-------|---------|--------|-------------------|---------------|
| Logistic Regression | ~0.82 | ~0.81 | ~0.75 | ~0.73 |
| Random Forest | ~0.84 | ~0.83 | ~0.76 | ~0.74 |
| **Gradient Boosting** | **~0.85** | **~0.84** | **~0.78** | **~0.76** |

> AUC values in the 0.80–0.85 range are competitive with published clinical diabetes risk scores.

---

##  Risk Score Output

The model converts predicted probabilities into a clinician-friendly risk score:

| Score | Tier | Action |
|-------|------|--------|
| 0–20 | Low Risk | Routine annual screening |
| 20–40 | Moderate Risk | Follow-up within 6 months; HbA1c test |
| 40–65 | High Risk | Diabetes prevention program; OGTT within 30 days |
| 65–100 | Very High Risk | Urgent evaluation; consider metformin prophylaxis |

---

##  Key Findings

1. **Glucose** is the strongest single predictor — patients with glucose > 140 mg/dL face dramatically elevated risk
2. **BMI** is the top modifiable risk factor — obesity nearly doubles risk
3. **Age** compounds risk significantly after 45
4. **Family history** (DiabetesPedigreeFunction) adds meaningful signal even after controlling for other factors
5. The **Glucose × BMI interaction** captures disproportionate risk at the intersection of both conditions

---

## Limitations & Future Work

- Dataset is limited to female patients of Pima Indian heritage — external validation required
- Does not account for HbA1c, diet, activity level, or medications
- Future work: SHAP explainability, calibration analysis, EHR integration mockup

---

## Tech Stack

```
Python 3.10+
├── pandas          — data manipulation
├── numpy           — numerical computing
├── sqlite3         — SQL queries (built-in)
├── scikit-learn    — ML models, pipelines, evaluation
├── matplotlib      — visualizations
└── seaborn         — statistical plots
```

---


---

