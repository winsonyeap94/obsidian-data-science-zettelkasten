---
tags: 
creation-date: 2024-05-15 22:17
---
# Conditional Outcome Modelling (COM)

Conditional outcome modelling is a statistical approach used in causal inference to estimate causal effects, i.e., the [[Average Treatment Effect (ATE)]] and [[Conditional Average Treatment Effect (CATE)]]. It involves fitting a **regression model** for the outcome variable, conditional on the treatment assignment and other covariates. The key steps are:

1. Specify a parametric regression model for the outcome, with the treatment variable and covariates as predictors. This is often a generalized linear model like logistic regression for binary outcomes or linear regression for continuous outcomes.
2. Estimate the conditional outcome model parameters, typically using maximum likelihood methods. This provides estimates of the treatment effect conditional on the covariates.
3. Obtain marginal (population-averaged) estimates of the treatment effect by averaging the conditional estimates over the covariate distributions of interest, often using standardization techniques like inverse probability weighting or model-based standardization with multiple imputation.

![[Pasted image 20240515222038.png]]

The model trained can be ***any model***, ranging from simple linear regression models to deep neural networks. The conditional outcome model is also referred to as $\mu$ above.

## COM Estimation of CATEs

![[Pasted image 20240515222422.png]]

## Other names of COM estimation

1. G-computation estimators
2. Parametric G-formula
3. Standardisation
4. S-learner (where "S" stands for "Single")

## Problem with COM estimation in high dimensions; Grouped COM (GCOM)

When we have a high-dimensional input, there is high tendency for the model to pay less attention to our treatment variable $T$. Therefore, it is possible that our models tend to be **bias towards $T=0$**.

![[Pasted image 20240515222638.png]]

The workaround is to use a **Grouped COM (GCOM)** estimation. 
- Instead of training a single model which has both treatment effects, we train *two models* in the GCOM approach, where each model is specific for a given treatment.
- Downside: Networks have higher variance than they would if they were trained with all the data (not efficient).

![[Pasted image 20240515222740.png]]
![[Pasted image 20240515222807.png]]



---
# References

- [[Casual Inference by Brady Neal#Chapter 5 & 6 Randomised Experiments; Frontdoor Adjustment; *do*-calculus; Graph-Based Identification]]