---
tags:
  - causal-analytics
creation-date: 2024-04-29 22:40
---
# Covariate Adjustment

Also known as "***Response Surface Modelling***".

Covariate adjustment is a statistical technique used to **account for the influence of confounding variables when estimating treatment effects**. It's often done to improve the precision of the estimates, and it is a common technique used in the analysis of randomised controlled trials and observational studies to improve the precision and accuracy of causal effect estimates. 

The key points are:
1. Covariate adjustment involves including relevant covariates (baseline variables) in the statistical model used to estimate the causal effect of an exposure or treatment on an outcome. 
2. Adjusting for covariates that predict the outcome can <mark style="background: #FFF3A3A6;">improve statistical power and precision of the causal effect estimate, by reducing the residual variance in the outcome</mark>. This is especially useful when randomisation does not fully balance the covariates between treatment groups.
3. However, adjusting for the wrong covariates, such as those that are colliders or mediators in the causal structure, can actually introduce bias into the causal effect estimate. Careful selection of the adjustment set is crucial, often guided by causal diagrams (directed acyclic graphs). 
4. Graphical criteria like the back-door criterion can be used to identify a sufficient set of covariates to adjust for in order to obtain an unbiased estimate of the total causal effect. This involves blocking all back-door paths between the exposure and outcome. 
5. There is an optimal adjustment set that provides the most efficient (minimum variance) causal effect estimate, which can be characterized graphically. This is an important consideration when multiple valid adjustment sets are available. 

It is a regression problem:

![[Pasted image 20240429224652.png]]

![[Pasted image 20240429225537.png]]

## Parametric G-Formula

The parametric g-formula is a statistical method to estimate the causal effects of sustained treatment strategies from observational data with time-varying confounding.
- It involves replacing the components of the g-formula (the joint density of the observed data) with parametric models of the outcome and covariate histories, rather than using sample means and probabilities. 

The goal is to fit a model $f(x, t) \approx \mathbb{E}[Y_t|T = t,x]$. 
- Once we have the function, then we approximate the [[Average Treatment Effect (ATE)]] using the formula below.
$$
\hat{ATE} = \frac{1}{n} \sum^n_{i=1}f(x_i, 1) - f(x_i, 0)
$$
$$
\hat{CATE(x_i)} = f(x_i, 1) - f(x_i, 0)
$$

![[Pasted image 20240429225214.png]]


## Covariate adjustment with linear models

Assume that:
$$
Y_t(x) = \beta x + \gamma \cdot t + \epsilon_t
$$
$$
\mathbb{E}[\epsilon_t] = 0
$$
Then:
$$
\begin{equation}
\begin{split}
CATE(x) & := \mathbb{E}[Y_1(x) - Y_0(x)] \\
&\  = \mathbb{E}[(\beta x + \gamma + \epsilon_1) - (\beta x + \epsilon_0)] \\
&\  = \gamma
\end{split}
\end{equation}
$$
And thus, since the terms cancel out, the [[Average Treatment Effect (ATE)]] can then be simplified as $\gamma$ (the coefficient of the treatment model):
$$
ATE := \mathbb{E}_{p(x)}[CATE(x)] = \gamma
$$
Therefore, for causal inference, we need to estimate $\gamma$ well, not $Y_t(x)$. This is why causal inference focuses on **identification, not prediction**.


> [!warning]
> That said, I personally do not believe that is correct, because the $\beta$ value can be different between both linear models due to the presence of the $\gamma \cdot t$ term, which ultimately affects the coefficients of the linear model.


## Covariate adjustment with non-linear models

Several techniques have been developed to try to fit covariate adjustment without making assumptions on the form of the data (non-parametric methods):
1. Random forests and Bayesian trees
2. Gaussian processes
3. Neural networks

![[Pasted image 20240430211415.png]]

![[Pasted image 20240430211433.png]]




---
# References

[[MIT 6.S897 Machine Learning for Healthcare, Spring 2019#Causal Inference, Part 1]]
[[MIT 6.S897 Machine Learning for Healthcare, Spring 2019#Causal Inference, Part 2]]