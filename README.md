# Bayesian Multilevel Modeling of Cardiovascular Disease Mortality
**Target Population:** European Women Aged 65–69  

## Project Overview
This project investigates the relationship between cardiovascular disease (CVD) mortality and health, 
dietary, and socioeconomic factors in European women aged 65–69. Using data from the 2021 Global Burden of Disease (GBD) 
study and GDP data from Our World in Data, Bayesian multilevel Beta regression models were fitted to quantify predictors of the proportion of deaths attributed to CVD.  

---

## Table of Contents
1. [Dataset Selection](#dataset-selection)  
2. [Data Preprocessing](#data-preprocessing)  
3. [Model Exploration](#model-exploration)  
4. [Power Sensitivity Analysis](#power-sensitivity-analysis)  
5. [Model Assessment](#model-assessment)  
6. [Model Comparison](#model-comparison)  
7. [Interpretation of Parameters](#interpretation-of-parameters)  
8. [Reporting Loss Functions](#reporting-loss-functions)  
9. [References](#references)  

---

## Dataset Selection
- **Primary Source:** 2021 GBD study, Institute for Health Metrics and Evaluation (IHME)  
- **Additional Source:** GDP per capita from Our World in Data  
- Focused on **females aged 65–69**, resulting in **456 observations**  
- Variables included:
  - Health & lifestyle: daily intake of trans fats, sodium, fiber, fruits, legumes, mean prevalence of overweight/obesity, smoking/tobacco use  
  - Socioeconomic: GDP per capita, total GDP  
  - Demographics: country, region, year  

- **Missing Data Handling:**  
  - Trans-fat intake: replaced 0s with NA and applied multiple imputation  
  - Tobacco use: excluded due to excessive missing values  

---

## Data Preprocessing
- Transformed target variable into a proportion:  
  `proportion_of_cvd_deaths = percent_of_cvd_deaths / 100`  
- Log-transformed skewed numeric variables (e.g., trans fats, fiber, GDP per capita)  
- Scaled and centered numeric variables  
- Filtered dataset for **females aged 65–69**  
- Split data into **training (80%)** and **testing (20%)** sets  

---

## Model Exploration
Two Bayesian multilevel Beta regression models were fitted using `brms`:

1. **Model 1:**  
   - Predictors: year, log-trans fat intake, log mean obesity prevalence, log GDP per capita  
   - Hierarchical structure: `(1 | region/country)`  
   - Priors: Default, later adjusted for phi  

2. **Model 2:**  
   - Predictors: year, log mean obesity prevalence, log sodium intake, log fiber intake, log-trans fat intake, fiber × trans fat interaction  
   - Hierarchical structure: `(1 | country)`  
   - Priors: Default, later adjusted for phi  

Both models converged well with minor warnings (divergent transitions), which were addressed by increasing `adapt_delta` and `max_treedepth`.  

---

## Power Sensitivity Analysis
- Initial analyses revealed potential prior-data conflicts for the phi parameter.  
- Adjusted priors: `lognormal(log(1800), 0.25)` for phi to better capture observed variance.  
- After adjustment, all warnings were resolved, and prior-data conflict was mitigated.  

---

## Model Assessment
- **Posterior Predictive Checks (PPCs)** indicate both models fit the observed data well:  
  - Observed vs. predicted scatter plots show central alignment  
  - Density plots of predicted vs. observed values match the data distribution  
  - Interval plots show adequate capture of uncertainty across countries and regions  

---

## Model Comparison
- **10-fold cross-validation (k-fold)** used for predictive performance assessment  
- Expected Log Predictive Density (ELPD):  
  - Model 1: 1130.32 ± 16.17  
  - Model 2: 1124.90 ± 15.44  
- Difference in ELPD not statistically significant  
- **Model 1** considered slightly more stable due to Rhat = 1.00 and higher effective sample sizes (ESS)  

---

## Interpretation of Parameters (Model 1)
- **Year:** Negative effect; CVD mortality proportion decreased from 2008 to 2019  
- **Log mean obesity prevalence:** Positive effect; higher obesity prevalence associated with higher CVD mortality  
- **Log trans fat intake:** Small positive effect  
- **Log GDP per capita:** Negative effect; wealthier countries showed slightly lower CVD mortality proportion  
- **Random effects:** Significant variation exists between regions and countries  

> Note: Coefficients are on logit scale; can be converted to proportions for interpretability  

---

## Reporting Loss Functions
- **Root Mean Squared Error (RMSE)** and **Mean Absolute Error (MAE)** calculated on the test set to evaluate predictive performance  
- Both models demonstrated low RMSE and MAE, consistent with good posterior predictive fit  

---

## References
1. Institute for Health Metrics and Evaluation (IHME). [Global Burden of Disease Study 2021](https://vizhub.healthdata.org/gbd-results/)  
2. Our World in Data. [GDP per capita dataset](https://ourworldindata.org/grapher/gdp-per-capita-worldbank)  
