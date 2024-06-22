---
tags:
  - causal-analytics
creation-date: 2024-05-15 22:30
---
# X-Learner

The X-Learner is designed to address the problem of estimating individual treatment effects (ITEs) from observational data. It works by leveraging two models: one to predict outcomes for treated individuals and one to predict outcomes for control individuals. These predictions are then combined to estimate the treatment effect for each individual.

The benefit compared to S-Learner is that this approach <mark style="background: #FFF3A3A6;">makes full use of the data</mark> when training the final treatment effect models.

## Steps

1. **Step 1: Fit Outcome Models - Estimate $\hat{\mu}_1(x)$ and $\hat{\mu}_0(x)$**
	- Assume $X$ is a sufficient adjustment set and is all observed covariates
2. **Step 2: Impute Counterfactuals**
	- We use the models fit from Step 1 to predict the other group. 
	- Treatment Group: $\hat{\tau}_{1,i} = Y_i(1) - \hat{\mu}_0(x_i)$  *(Note we use the Control group model to predict on Treatment group data)*
	- Control Group: $\hat{\tau}_{0,i} = \hat{\mu}_1(x_i) - Y_i(0)$  *(Note we use the Treatment group model to predict on Control group data)*
3. **Step 3: Compute Pseudo-Outcome - Calculate $\hat{\tau}_{1, i}$ and $\hat{\tau}_{0, i}$**
4. **Step 4: Fit Treatment Effect Models**
	- Based on the results above, we fit two new models:
		- Fit a model $\hat{\tau}_1(x)$ to predict $\hat{\tau}_{1,i}$ from $x_i$ in treatment group.
		- Fit a model $\hat{\tau}_0(x)$ to predict $\hat{\tau}_{0,i}$ from $x_i$ in treatment group.
5. **Step 5: Combine Treatment Effect Estimates**
	- $$\hat{\tau}(x) = g(x)\hat{\tau}_0(x) + (1-g(x))\hat{\tau}_1(x)$$
	- where $g(x)$ is some weighting function between 0 and 1 (e.g., propensity score).

## [[Propensity Scores]]

Propensity score here refers to the *propensity for (probability of) receiving treatment* given that $W=w$.
$$
e(W) \triangleq P(T=1 \mid W)
$$

The beauty of propensity score is that it magically reduces the dimensionality of adjustment set done to 1.
- However, we do not have access to it.
- The best we can do is to model it, shifting the high-dimensionality problem to the modelling of $e(W)$.

![[Pasted image 20240515224823.png]]
![[Pasted image 20240515225014.png]]



---
# References

- [[Casual Inference by Brady Neal#Chapter 5 & 6 Randomised Experiments; Frontdoor Adjustment; *do*-calculus; Graph-Based Identification]]