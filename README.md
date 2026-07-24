

# Resume Project Title

**Real-World Evidence Analysis of 30-Day Hospital Readmission Risk in Type 2 Diabetes**

# Resume Description

Conducted retrospective real-world evidence analysis using EHR data from 130 U.S. hospitals to identify predictors of 30-day diabetes-related hospital readmission. Performed clinical data cleaning, exploratory analysis, hypothesis testing, multivariable logistic regression modeling, ROC analysis (AUC=0.63), and developed statistical visualizations using R.
## Project Overview

This project presents a retrospective real-world evidence (RWE) analysis using electronic health record (EHR) data to identify clinical factors associated with **30-day hospital readmission risk among patients with diabetes**.

The analysis was performed using the **Diabetes 130-US Hospitals for Years 1999–2008 dataset**, containing patient-level clinical information from 130 U.S. hospitals.

This project demonstrates an end-to-end biostatistical workflow including:

- Clinical data cleaning and validation
- Missing data assessment
- Exploratory data analysis (EDA)
- Statistical hypothesis testing
- Multivariable logistic regression modeling
- Model performance evaluation using ROC analysis
- Healthcare data visualization using R


---

# Research Question

**What clinical and healthcare utilization factors are associated with increased risk of 30-day hospital readmission among diabetic patients?**


---

# Dataset

## Source

Diabetes 130-US Hospitals for Years 1999–2008 dataset (Kaggle)

## Dataset Characteristics

- 101,766 patient encounters
- 50 clinical variables
- Electronic health record-based patient-level data
- Data collected from 130 U.S. hospitals

## Variables Included

The dataset contains information related to:

- Patient demographics
- Admission characteristics
- Hospital utilization history
- Laboratory procedures
- Medication information
- Diagnosis information
- Readmission outcomes


---

# Project Objectives

The objectives of this analysis were:

- Perform clinical data cleaning and validation
- Identify and handle missing values
- Assess data quality and completeness
- Conduct exploratory data analysis
- Compare patient characteristics between readmitted and non-readmitted groups
- Perform statistical hypothesis testing
- Develop a multivariable logistic regression model
- Identify important predictors of 30-day readmission
- Evaluate model performance using ROC curve analysis


---

# Analytical Workflow

## 1. Data Import

The original CSV dataset was imported into R for analysis.

### Functions Used:

```r
read.csv()
```

Purpose:

- Import clinical data into R environment
- Convert external CSV file into a data frame


```r
head()
```

Purpose:

- View first few rows of data
- Understand the dataset format


```r
dim()
```

Purpose:

- Identify number of patient records and variables


```r
str()
```

Purpose:

- Examine variable structure
- Identify numeric, character, and categorical variables before analysis


---

# 2. Data Cleaning and Validation

Healthcare datasets often contain incomplete information and inconsistent values. Data preprocessing was performed before statistical analysis.

## Missing Value Conversion

The dataset contained missing values represented as "?".

These values were converted into proper missing values (NA).

Example:

```r
mutate(
across(
where(is.character),
~na_if(., "?")
)
)
```

Purpose:

- Convert incorrect missing value indicators into recognized missing values
- Allow accurate missing data assessment


---

## Missing Data Assessment

Function used:

```r
colSums(is.na(diabetes_clean))
```

Purpose:

- Count missing values for each variable
- Evaluate data completeness


Variables with excessive missing information were removed:

- Weight
- Payer code
- Medical specialty


Reason:

These variables contained a large proportion of missing values and could reduce model reliability.


---

## Duplicate Record Check

Function used:

```r
sum(duplicated(diabetes_clean))
```

Purpose:

- Identify duplicate patient encounters
- Ensure data quality


Result:

No duplicate records identified.


---

## Convert Variables

Categorical variables were converted into factors.

Example:

```r
as.factor()
```

Purpose:

- Prepare categorical variables for statistical modeling
- Allow R to correctly interpret groups


---

## Creating Study Outcome Variable

Original variable:

```
readmitted
```

Contained:

- <30 days
- >30 days
- NO


Created new binary outcome:

```
readmission_30

0 = No 30-day readmission

1 = Readmitted within 30 days
```

Purpose:

Binary outcome was required for logistic regression modeling.


---

# 3. Exploratory Data Analysis (EDA)

Exploratory analysis was performed to understand patient characteristics, healthcare utilization patterns, and readmission trends.


## Readmission Distribution

Function:

```r
table(readmission_30)
```

Purpose:

- Count patients with and without 30-day readmission


Finding:

- 90,409 patients had no 30-day readmission
- 11,357 patients experienced 30-day readmission


Approximately:

- 88.8% No readmission
- 11.2% Readmission


---

## Hospital Length of Stay Analysis

Compared hospital stay duration between groups.

Function:

```r
tapply(
time_in_hospital,
readmission_30,
mean
)
```

Finding:

Average hospital stay:

- No readmission: 4.35 days
- 30-day readmission: 4.77 days


---

## Visualization Created

Generated publication-style figures:

- Readmission distribution plot
- Hospital length of stay distribution plot
- Readmission by age group plot


---

# 4. Statistical Hypothesis Testing

Statistical tests were performed to identify associations between clinical variables and readmission status.


# Independent T-Test

## Research Question

Do patients with 30-day readmission have a different hospital length of stay compared with patients without readmission?


## Method

```r
t.test(
time_in_hospital ~ readmission_30,
data = diabetes_clean
)
```


## Purpose

The independent t-test compares the mean value of a continuous variable between two groups.


Groups:

- Group 0: No 30-day readmission
- Group 1: 30-day readmission


## Result

The difference in hospital stay duration was statistically significant.

p-value:

```
<0.001
```


## Interpretation

Patients with 30-day readmission had longer hospital stays, suggesting increased disease complexity and healthcare needs.


---

# Chi-Square Test


## Research Question

Is gender associated with 30-day hospital readmission?


## Method

```r
chisq.test(
table(
gender,
readmission_30
)
)
```


## Purpose

Chi-square test evaluates association between two categorical variables.


Variables:

- Gender
- Readmission status


## Result

p-value:

```
0.5387
```


## Interpretation

Gender was not statistically associated with 30-day readmission risk in this dataset.


---

# 5. Multivariable Logistic Regression Model


## Objective

A logistic regression model was developed to identify independent predictors of 30-day hospital readmission while adjusting for multiple clinical variables.


## Why Logistic Regression?

The outcome variable was binary:

```
0 = No readmission

1 = Readmitted within 30 days
```

Logistic regression estimates the probability of an event occurring and provides odds ratios for risk factors.


## Model

```r
model <- glm(
readmission_30 ~

age +
gender +
time_in_hospital +
num_medications +
number_inpatient +
number_diagnoses,

data = diabetes_clean,

family = "binomial"
)
```


## Predictors Included

The model evaluated:

- Age group
- Gender
- Hospital length of stay
- Number of medications
- Previous inpatient visits
- Number of diagnoses


---

# 6. Logistic Regression Results


## Previous Inpatient Visits

Odds Ratio:

```
1.32
```

Interpretation:

Patients with more previous inpatient visits had higher odds of 30-day readmission.


---

## Hospital Length of Stay

Odds Ratio:

```
1.02
```

Interpretation:

Each additional hospital day slightly increased the probability of readmission.


---

## Number of Diagnoses

Odds Ratio:

```
1.04
```

Interpretation:

Patients with greater disease complexity had increased readmission risk.


---

# 7. Model Evaluation


## ROC Curve Analysis

ROC analysis was performed to evaluate model discrimination ability.


Functions used:

```r
roc()

auc()
```


## Model Performance

Area Under Curve (AUC):

```
0.6316
```


## Interpretation

The model demonstrated moderate ability to distinguish between:

- Patients with 30-day readmission
- Patients without readmission


AUC Interpretation:

- 0.50 = No predictive ability
- 0.70–0.80 = Acceptable discrimination
- 0.80–0.90 = Good discrimination
- >0.90 = Excellent discrimination


---

# Technologies Used

## Programming Language

- R


## Statistical Methods

- Descriptive statistics
- Missing data analysis
- Data cleaning
- T-test
- Chi-square test
- Logistic regression
- ROC analysis


## R Packages


### tidyverse

Used for:

- Data manipulation
- Data cleaning
- Visualization


### pROC

Used for:

- ROC curve generation
- AUC calculation


### broom

Used for:

- Formatting statistical model results
- Creating analysis tables


---

# Project Structure


```
Diabetes_Readmission_Portfolio/

│
├── data/
│   ├── diabetes_data.csv
│   └── diabetes_clean.csv
│
├── scripts/
│   ├── 01_import.R
│   ├── 02_cleaning.R
│   ├── 03_EDA.R
│   ├── 04_statistics.R
│   ├── 05_logistic_regression.R
│   └── 06_visualization.R
│
├── figures/
│   ├── readmission_distribution.png
│   ├── hospital_stay_distribution.png
│   ├── readmission_by_age.png
│   ├── ROC_curve.png
│   └── odds_ratio_plot.png
│
└── README.md
```


---

# Key Skills Demonstrated

- Real-world evidence (RWE) analysis
- Clinical data management
- Electronic health record (EHR) analysis
- Biostatistical programming using R
- Statistical hypothesis testing
- Regression modeling
- Healthcare analytics
- Clinical research methodology
- Data visualization

---

# Conclusion

This project demonstrates an end-to-end biostatistical workflow applied to real-world healthcare data. The analysis identified important clinical and utilization factors associated with hospital readmission risk and reflects common methods used in clinical research, healthcare analytics, and real-world evidence generation.
