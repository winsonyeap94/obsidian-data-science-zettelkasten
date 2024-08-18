---
tags:
  - explainable-ai
creation-date: 2024-08-18 00:11
---
## Partial Dependence Plot (PDP)
--- start-multi-column: ID_gld9
```column-settings
Number of Columns: 2
Largest Column: standard
```
![[Pasted image 20240818001530.png]]


--- column-break ---

![[Pasted image 20240818001533.png]]

--- end-multi-column
PDP shows the **marginal effect** one or two features have on the predicted outcome of a machine learning model.
- A partial dependence plot can show whether the relationship between the target and a feature is linear, monotonic, or more complex.

---
### Algorithm

#### Regression

The partial dependence function for regression is defined as:
$$
\hat{f}_S(x_S) = E_{X_C}\Big[\hat{f}\big(x_S, X_C\big)\Big] = \int\hat{f}(x_S, X_C)\ d\mathbb{P}(X_C)
$$
where:
- $x_S$ : Features for which the partial dependence function should be plotted.
- $X_C$ : The other features used in the ML model $\hat{f}$, which are here treated as random variables.

Partial dependence works by *marginalising the ML model output* over the distribution of the features in set $C$, so that the function shows the relationship between the features in set $S$ we are interested in an the predicted outcome. By marginalising over the other features, we get a function that depends only on features in $S$, interactions with other features included.

The partial function $\hat{f}_S$ is estimated by calculating *averages in the training data*, also known as the Monte Carlo method:
$$
\hat{f}_S(x_S) = \frac{1}{n}\sum^n_{i=1}\hat{f}\big(x_S, x_C^{(i)}\big)
$$
The partial function tells us for given value(s) of features $S$ what the average marginal effect on the prediction is.
- $x_C^{(i)}$ are actual feature values from the dataset for the features in which we are not interested.
- $n$ : Number of instances in the dataset.

>[!warning] Assumption
>An assumption of the PDP is that **features in $C$ are not correlated with the features in $S$**.
>If this assumption is violated, the averages calculated for the PDP will include data points that are very unlikely or even impossible.

#### Classification

For classification where the ML model outputs probabilities, the PDP displays the probability for a certain class given different values for feature(s) in $S$. 

An easy way to deal with multiple classes is to draw one line or plot per class.

#### Categorical Features

For categorical features, the partial dependence is very easy to calculate. 

We get a PDP estimate by *forcing all data instances to have the same category*.
- For example, if we look at the bike rental dataset and are interested in the PDP for the season, we get four numbers, one for each season. To compute the value for "summer", we replace the season of all data instances with "summer" and average the predictions.

---
### PDP-based feature Importance

The basic motivation is that a flat PDP indicates that the feature is not important, and **the more the PDP varies, the more important the feature is**.

For numerical features, importance is defined as the *deviation of each unique feature value from the average curve*.
$$
I(x_S) = \sqrt{\frac{1}{K-1}\sum^K_{k=1}\Big(\hat{f}_S\big(x_S^{(k)}\big)\Big) - \frac{1}{K}\sum^K_{k=1}\hat{f}_S\big(\big(s_x^{(k)}\big)\big)^2}
$$
Note that here $x_S^{(k)}$ are the $K$ unique values of feature $X_S$. 


For categorical features we have:
$$
I(x_S) = \frac{\bigg(\max_k\Big(\hat{f}_S(x_S^{(k)}\big)\Big) - \min_k\Big(\hat{f}_S\big(x_S^{(k)}\big)\Big) \bigg)}{4}
$$
This is the *range of the PDP values for the unique categories divided by 4*. This strange way of calculating the deviation is called the *range rule*.
- The denominator 4 comes from the standard normal distribution -- In the normal distribution, 95% of the data are $\pm 2$ standard deviations around the mean.

>[!warning] This PDP-based feature importance should be interpreted with care.
>1. It captures only the main effect of the feature and **ignores possible feature interactions**. A feature could be very important based on other methods (e.g., [[Permutation Feature Importance]], but the PDP could be flat as the feature affects the prediction mainly through interactions with other features).
>2. It is **defined over the unique values**. A unique feature value with just one instance is given the same weight in the importance computation as value with many instances.

---
### Advantages

1. **Intuitive**.
2. **Interpretation is clear** if the features selected are not correlated with other features.
3. **Easy to implement**.
4. The calculation for the PDPs has a **causal interpretation**. We intervene on a feature and measure the changes in the predictions.

---
### Disadvantages

1. The realistic **maximum number of features is 2**. It is difficult to visually explain PDPs when $\ge 3$ features are involved.
2. The **assumption of independence** is the biggest issue with PDP. One solution to this problem is [[Accumulated Local Effects (ALE)]] or short ALE plots that work with conditional instead of the marginal distribution.
3. **Heterogenous effects might be hidden** because PDPs only show the average marginal effects.
	- Suppose that for a feature half of the data points have a positive association with the prediction (larger the value, larger the prediction), and the other half has a negative association. *The PD curve could be a horizontal line, since the effects of both halves of the dataset could cancel each other out*.
	- By plotting the [[Individual Conditional Expectations (ICE)|individual conditional expectation curves]] instead of the aggregated line, we can uncover heterogenous effects.

---
## References

- [[Interpretable Machine Learning]]
- [8.1 Partial Dependence Plot (PDP) | Interpretable Machine Learning (christophm.github.io)](https://christophm.github.io/interpretable-ml-book/pdp.html)