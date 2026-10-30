# 🍎 Apple Quality: Predictive Weight Modeling & Shrinkage Methods

## 📌 Project Overview
This project investigates the biological and structural factors that influence apple weight using data sourced from Kaggle. Eating an apple daily sparked my curiosity about which characteristics truly impact their development. 

By implementing exploratory data analysis, data transformations, variable selection, and regularized machine learning techniques (LASSO, Ridge, Elastic Net), this analysis establishes a framework to optimize harvesting schedules, improve packaging logistics, and guide consumer choice.

---

## 🧹 Data Pipeline & Preprocessing
* **Scale Shift:** The raw dataset featured pre-scaled values falling symmetrically above and below a mean, introducing negative data points. To satisfy the mathematical constraints of variance-stabilizing transformations, the response variable was shifted into entirely positive space.
* **Dimensional Reduction:** Removed categorical string variables and unique tracking identifiers (`A_id`) to condition the matrix for purely mathematical operations.
* **Outlier Mitigation:** Evaluated studentized residuals to iteratively identify and strip out 5 severe, high-leverage outliers (Observations: `84`, `1023`, `526`, `1063`, and `560`). This process stabilized the model's error distribution, allowing the residuals to satisfy normality assumptions ($p = 0.1066$).

---

## 📈 Modeling Methodologies & Statistical Verification

### 1. Simple Linear Regression (Baseline)
Initial single-variable regressions were run against `Weight` to establish baseline significance. Strong linear dependencies were confirmed across key standalone metrics:
* **Ripeness vs. Weight:** $t = -7.514$, $p = 1.21 \times 10^{-13}$ (Highly significant relationship)
* **Size vs. Weight:** $t = -5.216$, $p = 2.2 \times 10^{-7}$
* **Sweetness vs. Weight:** $t = -4.411$, $p = 1.13 \times 10^{-5}$

### 2. Ordinary Least Squares (OLS) with Box-Cox Transformation
A maximum model evaluating all variable interactions and quadratic terms was optimized using **Backward Selection** based on the Akaike Information Criterion (AIC). 
* **Transformation:** A Box-Cox grid search identified an optimal parameter of $\lambda = 1.07$.
* **Explanatory Power:** The final model accounted for **39.59%** of the total variance in shifted apple weight ($R^2 = 0.3959, F\text{-statistic} = 27.35, p < 2 \times 10^{-16}$).
* **Residual Diagnostics:** While normality was successfully achieved via outlier suppression, heteroscedasticity persisted (Breusch-Pagan test $p < 2.2 \times 10^{-16}$), highlighting a boundary condition of linear approximations on this dataset.

### 3. Regularization & Machine Learning Comparison
To counteract potential multicollinearity among the interaction terms, the structural features were validated utilizing **5-fold Cross-Validation** across three distinct regularization penalties:

| Regularization Method | Hyperparameter Tuning | Out-of-Sample MSPE | Optimization Behavior |
| :--- | :--- | :--- | :--- |
| **LASSO (L1)** | $\lambda = 0.0011918$ | **1.8034** | **Top Performer.** Forces non-impactful coefficients strictly to zero to optimize feature selection. |
| **Ridge (L2)** | $\lambda = 0.0381000$ | **1.8086** | Retains all features while shrinking coefficients uniformly; yields slightly higher prediction errors. |
| **Elastic Net** | $\alpha = 1.0000000$ | **1.8127** | Collapses directly into the absolute penalty structure of LASSO, confirming L1 efficiency. |

---

## 🎯 Key Takeaways & Actionable Insights
* **The Vital Few:** Feature selection via LASSO identified **Ripeness, Sweetness, Size, and Quality** as the primary drivers of shifted apple weight (evidenced by the largest absolute coefficients). Producers should concentrate cultivation parameters, harvest sorting, and quality assurance directly on these four vectors.
* **The Trivial Many:** Attributes such as **Acidity, Juiciness, and Crunchiness** showed negligible predictive impacts on overall weight profiles and can be safely deprioritized during resource allocation.

---

## 💻 Tech Stack & Environment
* **Language:** R (v4.x)
* **Core Libraries:**
  * `tidyverse` & `ggplot2` — Data Wrangling & Graphical Explorations
  * `MASS` — Box-Cox Transformation Implementations
  * `glmnet` — Cross-Validated LASSO, Ridge, and Elastic Net regularizations
  * `lmtest` — Breusch-Pagan homoscedasticity diagnostics
