---
tags:
  - explainable-ai
creation-date: 2024-08-18 09:24
---
## Feature Interaction

When features interact with each other in a prediction model, the prediction cannot be expressed as the sum of the feature effects, because the effect of one feature depends on the value of the other feature. Aristotle’s predicate “The whole is greater than the sum of its parts” applies in the presence of interactions.

One way to estimate the interaction strength is to measure how much of the variation of the prediction depends on the interaction of the features. This measurement is called H-statistic, introduced by Friedman and Popescu (2008)[35](https://christophm.github.io/interpretable-ml-book/interaction.html#fn35).

### Friedman's H-Statistic

We are going to deal with two cases:
1. First, a **two-way interaction measure** that tells us *whether and to what extent two features in a model interact with each other*, and
2. Second, a **total interaction measure** that tells us *whether and to what extent a feature interacts in the model with **all other features***.

If two features do not interact, we can decompose the [[Partial Dependence Plot (PDP)|partial dependence function]] as follows (assuming the partial dependence functions are centred at zero):
$$
PD_{jk}(x_j, x_k) = PD_j(x_j) + PD_k(x_k)
$$
Likewise, if a feature has no interaction with any of the other features, we can express the prediction function $\hat{f}(x)$ as a sum of partial dependence functions:
$$
\hat{f}(x) = PD_j(x_j) + PD_{-j}(x_{-j})
$$

This decomposition expresses the partial dependence (or full prediction) function without interactions (between features j and k, or respectively j and all other features). In a next step, we measure the difference between the observed partial dependence function and the decomposed one without interactions. We calculate the **variance** of the output of the partial dependence (to measure the interaction between two features) or of the entire function (to measure the interaction between a feature and all other features). The amount of the variance explained by the interaction (difference between observed and no-interaction PD) is used as **interaction strength statistic**. The statistic is 0 if there is no interaction at all and 1 if all of the variance of the $PD_{jk}$ or $\hat{f}$ is explained by the sum of the partial dependence functions. An interaction statistic of 1 between two features means that each single PD function is constant and the effect on the prediction only comes through the interaction. The H-statistic can also be larger than 1, which is more difficult to interpret. This can happen when the variance of the 2-way interaction is larger than the variance of the 2-dimensional partial dependence plot.

Mathematically, the H-statistic proposed by Friedman and Popescu for the interaction between feature $j$ and $k$ is:
$$
H^2_{jk} = \frac{\sum^n_{i=1}\bigg[PD_{jk} (x_j^{(i)}, x_k^{(i)}) - PD_j(x_j^{(i)}) - PD_k(x_k^{(i)}) \bigg]^2}{\sum^n_{i=1} PD^2_{jk}(x_j^{(i)}, x_k^{(i)})}
$$
The same applies to measuring whether a feature $j$ interacts with any other feature.
$$
H^2_j=\frac{\sum^n_{i=1}\bigg[\hat{f}(x^{(i)}) - PD_j(x_j^{(i)}) - PD_{-j}(x_{-j}^{(i)})\bigg]^2}{\sum^n_{i=1}\hat{f}^2(x^{(i)})}
$$

The H-statistic is **expensive to evaluate**, because it iterates over all data points and at each point the partial dependence has to be evaluated which in turn is done with all n data points.

---
### Examples

#### Case 1 - Total Interaction Measure
--- start-multi-column: ID_bsz9
```column-settings
Number of Columns: 2
Largest Column: standard
```

![[Pasted image 20240818110349.png]]

> The interaction strength (H-statistic) for each feature with all other features for a support vector machine predicting bicycle rentals. Overall, the interaction effects between the features are very weak (below 10% of variance explained per feature).

--- column-break ---

![[Pasted image 20240818110359.png]]

> The interaction strength (H-statistic) for each feature with all other features for a random forest predicting the probability of cervical cancer. The years on hormonal contraceptives has the highest relative interaction effect with all other features, followed by the number of pregnancies.

--- end-multi-column
#### Case 2 - 2-way Interactions

![[Pasted image 20240818110812.png]]
> The 2-way interaction strengths (H-statistic) between number of pregnancies and each other feature. There is a strong interaction between the number of pregnancies and the age.


---
### Advantages

1. The H-statistic has a **meaningful interpretation** - The interaction is defined as the share of variance that is explained by the interaction.
2. Since the statistic is **dimensionless**, it is comparable across features and even across models.
3. The statistic **detects all kinds of interactions**, regardless of their particular form.
4. It is also possible to analyse arbitrary **higher interactions** (e.g., interaction of 3 or more features).

---
### Disadvantages

1. **Computationally expensive**.
2. As the computation involves estimating marginal distributions, these estimates also have a **certain variance if we do not use all data points**. This means that as we sample points, the estimates also vary from run to run and the results can be unstable.
3. It is unclear whether an interaction is significantly greater than 0. We would need to conduct a statistical test, but this test is not available in a model-agnostic version.

---
## References

- [[Interpretable Machine Learning]]
- [8.3 Feature Interaction | Interpretable Machine Learning (christophm.github.io)](https://christophm.github.io/interpretable-ml-book/interaction.html)