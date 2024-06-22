---
tags:
  - causal-analytics
creation-date: 2024-04-29 22:01
---
# Potential Outcomes Framework (Rubin-Neyman Causal Model)

Each unit (individual) $x_i$ has two potential outcomes:
1. $Y_0(x_i)$ is the potential outcome had the unit not been treated -- "***control outcome***".
2. $Y_1(x_i)$ is the potential outcome had the unit been treated -- "***treated outcome***".

**[[Conditional Average Treatment Effect (CATE)]]** for unit $i$ helps us answer the question of "which treatment is better?"
$$
CATE(x_i) = \mathbb{E}_{Y_1 \sim p(Y_1|x_i)}[Y_1|x_i] - \mathbb{E}_{Y_0 \sim p(Y_0|x_i)}[Y_0|x_i]
$$

**[[Average Treatment Effect (ATE)]]**
$$
ATE := \mathbb{E}[Y_1 - Y_0] = \mathbb{E}_{x \sim p(x)}[CATE(x)]
$$

![[Pasted image 20240429220941.png]]

![[Pasted image 20240429220959.png]]

## Factual vs Counterfactuals

> "The fundamental problem of causal inference" 
> *We only ever observe one of the two outcomes.*

Observed factual outcome:
$$
y_i = t_i Y_1(x_i) + (1 - t_i)Y_0(x_i)
$$
Unobserved counterfactual outcome:
$$
y_i^{CF} = (1 - t_i) Y_1(x_i) + t_iY_0(x_i)
$$

## Typical Assumptions

### 1) Ignorability -- No unmeasured confounders

$Y_0$, $Y_1$ : Potential outcomes for control and treated
$x$ : Unit covariates (features)
$T$ : Treatment assignment

We assume that the potential outcomes are **independent of treatment assignment**, conditioned on covariates $x$.
- $\newcommand{\indep}{\perp \!\!\! \perp}\indep$ : is independent of
$$
\newcommand{\indep}{\perp \!\!\! \perp}
(Y_0, Y_1) \indep T \mid x
$$
![[Pasted image 20240429221906.png]]

![[Pasted image 20240429221925.png]]

### 2) Common Support

We assume that the probability an individual is given a treatment will happen regardless of the individual's attributes.
$$
p(T=t \mid X=x) > 0 \ \forall \ t, x
$$
This is something we can check from data.



---
# References

[[MIT 6.S897 Machine Learning for Healthcare, Spring 2019#Causal Inference, Part 1]]