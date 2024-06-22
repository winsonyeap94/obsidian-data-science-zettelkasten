---
tags:
  - causal-analytics
creation-date: 2024-05-15 22:53
---
# Double Machine Learning

Double Machine Learning (DML) is a robust method used in causal inference to estimate treatment effects while controlling for confounding variables. It combines the strengths of machine learning algorithms with traditional econometric techniques to provide consistent and unbiased estimates of causal parameters, even when the relationship between the covariates and the outcome or treatment is complex and potentially nonlinear.

It is called double machine learning because it comprises of two main stages:

1. **Stage 1: Fitting Models that relate Covariates to Treatment and Outcome**
	- Model 1: Predict $Y$ from $W$ to get the predicted $\hat{Y}$.
	- Model 2: Predict $T$ from $W$ to get the predicted $\hat{T}$.
	
2. **Stage 2: Estimate the treatment effect by isolating the effect of Covariates**
	- We then estimate the treatment effect by fitting a model to predict $Y-\hat{Y}$ from $T-\hat{T}$.
	- These residuals are essentially "de-confounded" versions of the treatment and outcome.

## Key Concepts of Double Machine Learning

1. **Orthogonalization**: This involves separating the nuisance parameters (functions that capture the complex relationships between covariates and outcomes/treatments) from the parameter of interest (the treatment effect). This helps in reducing the bias caused by model overfitting.
    
2. **Nuisance Parameter Estimation**: Machine learning models are used to flexibly estimate the nuisance parameters, which include:
    - The relationship between covariates and the *treatment* (propensity score).
    - The relationship between covariates and the *outcome*.
	
3. **Cross-Fitting**: This is a technique to avoid overfitting by splitting the data into multiple folds. Each fold is used to train the nuisance parameter models, and then the treatment effect is estimated on the held-out fold. This helps in achieving a form of double robustness.

---
# References

- [[Casual Inference by Brady Neal#Chapter 5 & 6 Randomised Experiments; Frontdoor Adjustment; *do*-calculus; Graph-Based Identification]]