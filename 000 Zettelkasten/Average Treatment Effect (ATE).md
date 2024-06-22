---
tags:
  - causal-analytics
creation-date: 2024-04-29 22:23
---
# Average Treatment Effect (ATE)

The expected causal effect of $T$ on $Y$:
$$
ATE := \mathbb{E}[Y_1 - Y_0]
$$

## Deriving ATE

Based on the *law of total expectation*:
$$
\mathbb{E}[Y_1] = \mathbb{E}_{x \sim p(x)}[\mathbb{E}_{Y_1 \sim p(Y_1 \mid x)}[Y_1 \mid x]]
$$
$$
\begin{equation}
\begin{split}
\mathbb{E}[Y_1] 
& = \mathbb{E}_{x \sim p(x)}[\mathbb{E}_{Y_1 \sim p(Y_1 \mid x)}[Y_1 \mid x]] \\
& = \mathbb{E}_{x \sim p(x)}[\mathbb{E}_{Y_1 \sim p(Y_1 \mid x)}[Y_1 \mid x , T=1]] && \text{ -- based on ignorability assumption } \\ 
& = \mathbb{E}_{x \sim p(x)}[\mathbb{E}[Y_1 \mid x, T=1]] && \text{ -- shorter notation}
\end{split}
\end{equation}
$$
The above equation is the same for $\mathbb{E}[Y_0]$.

Under the assumption of [[Potential Outcomes Framework (Rubin-Neyman Causal Model)#1) Ignorability -- No unmeasured confounders|ignorability]], we have that:
$$
\begin{equation}
\begin{split}
ATE 
&= \mathbb{E}[Y_1 - Y_0] \\ 
&= \mathbb{E}_{x \sim p(x)}[ \mathbb{E}[Y_1 \mid x, T=1] - \mathbb{E}[Y_0 \mid x , T=0] ]
\end{split}
\end{equation}
$$
From the above, the two terms $E[Y_i \mid x, T=i]$ are quantities that we can estimate from our data.

## Improving ATE Estimate

The precision of the ATE estimate can be improved using the [[Covariate Adjustment]] technique by reducing the residual variance in the outcome. This is especially useful when randomisation does not fully balance the covariates between treatment groups.
- However, adjusting for the wrong covariates, such as those that are colliders or mediators in the causal structure, can introduce bias into the ATE estimate. Careful selection of the adjustment set is crucial, often guided by causal diagrams.
- There is an optimal adjustment set that provides the most efficient (minimum variance) ATE estimate, which can be characterized graphically. This is an important consideration when multiple valid adjustment sets are available.

Another approach of estimating ATE is by using a technique known as [[Matching in Causal Inference|matching]]. 
- The idea is to find each unit's long-lost counterfactual identical twin, then check up on his outcome.
- For example, we can match each record to the *nearest neighbour from the opposite group*.

Another tool that is often used for estimating ATE is by using [[Propensity Scores]], which helps weighting data points. It is helpful for cases when the distribution of attributes ($x$) are not equal between groups.

## Assumptions to make ATE equal to Associational Difference
![[Pasted image 20240512213739.png]]
### 1) Ignorability $(Y(1), (Y0)) \indep T$

There are no unmeasured confounders.
Also related to [[Potential Outcomes Framework (Rubin-Neyman Causal Model)]].

$$
\begin{align}
\mathbb{E}[Y(1)] - \mathbb{E}[Y(0)] & = \mathbb{E}[Y(1) \mid T=1] - \mathbb{E}[Y(0) \mid T=0] \\
& = \mathbb{E}[Y \mid T=1] - \mathbb{E}[Y \mid T=0]
\end{align}
$$
### 2) Exchangeability

If we are to swap the groups of treatment, the expected values would remain the same.

![[Pasted image 20240512213132.png]]

$$
\begin{align}
\text{(Before Switch) }\ \ \mathbb{E}[Y(1) \mid T = 1] & = \mathbb{E}[Y(1) \mid T = 0] \ \ \text{ (After Switch)} \\
\text{(Before Switch) }\ \ \mathbb{E}[Y(0) \mid T = 0] & = \mathbb{E}[Y(0) \mid T = 1] \ \ \text{ (After Switch)} \\
\end{align}
$$

### 3) Identifiability

A causal quantity (e.g., $\mathbb{E}[Y(t)]$) is **identifiable** if we can compute it from a purely *statistical quantity* (e.g., $\mathbb{E}[Y \mid t]$).

![[Pasted image 20240512213419.png]]

This is something we can achieve via [[Randomised Control Trials (RCT)]].


### 4) Unconfoundedness

Unconfoundedness is an *untestable assumption*. We never know if we have unobserved confounders.

![[Pasted image 20240512213855.png]]
### 5) Positivity

For all values of covariates $x$ present in the population of interest (i.e., $x$ such that $P(X=x)>0$)),
$$
0 < P(T=1 \mid X=x) < 1
$$
If positivity is not satisfied, it means we would be conditioning for *zero probability events* in our adjustment formula, which does not make sense.

### 6) Overlap (Common Support)

This is an important assumption as we assume that distributions between both groups have some overlap.
- **No overlap** means we have a *severe positivity violation*.
- Complete overlap means we have no positivity violation (ideal case).

What would happen if we try to estimate the ATE if we have a severe positivity violation?
- It would result in **inaccurate extrapolation**.

![[Pasted image 20240512214152.png]]
![[Pasted image 20240512214238.png]]

### 7) No Interference

This means that the function of all treatment outcomes are independent of each other (do not interfere with each other).
$$
Y_i(t_1, ..., t_{i-1}, t_i, t_{i+1}, ..., t_n) = Y_i(t_i)
$$
### 8) Consistency

This means that the **same value of treatment** should result in the **same outcome**.
- If this is not satisfied, it means our potential outcome ($Y(t)$) is *not well defined*.
- Also known as the <mark style="background: #FFF3A3A6;">no multiple versions of treatment</mark> assumption.
$$
T=t \Longrightarrow Y=Y(t)
$$




---
# References

- [[MIT 6.S897 Machine Learning for Healthcare, Spring 2019#Causal Inference, Part 1]]
- [[Casual Inference by Brady Neal#Chapter 2 Potential Outcomes; A Complete Example with Estimation]]
