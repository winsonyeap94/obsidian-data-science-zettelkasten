---
tags:
  - explainable-ai
creation-date: 2024-08-18 00:40
---
## Accumulated Local Effects (ALE)

ALEs describe *how features influence the prediction of a machine learning model **on average***. ALE plots are a faster and unbiased alternative to [[Partial Dependence Plot (PDP)]].

### Motivation & Intuition

If features of a machine learning model are correlated, then [[Partial Dependence Plot (PDP)|PDPs]] cannot be trusted. The following figure illustrates two correlated features and how it comes that the PDP method averages predictions of unlikely instances.

![[Pasted image 20240818085429.png]]
>Strongly correlated features x1 and x2. To calculate the feature effect of x1 at 0.75, the PDP replaces x1 of all instances with 0.75, falsely assuming that the distribution of x2 at x1 = 0.75 is the same as the marginal distribution of x2 (vertical line). This results in unlikely combinations of x1 and x2 (e.g. x2=0.2 at x1=0.75), which the PDP uses for the calculation of the average effect.

ALE plots solve this problem by calculating, based on the *conditional distribution* of the features, the **differences in predictions instead of averages**.

![[Pasted image 20240818090321.png]]
> Calculation of ALE for feature x1, which is correlated with x2. First, we divide the feature into intervals (vertical lines). For the data instances (points) in an interval, we calculate the difference in the prediction when we replace the feature with the upper and lower limit of the interval (horizontal lines). These differences are later accumulated and centred, resulting in the ALE curve.

>[!info]- ALE Plots vs M-Plots
>Marginal plots (M-Plots) calculate feature effects using the *conditional distribution* instead. It is better than PDPs, but it does not solve the problem fully. 
>- For example, if we take the house area prediction use case, if we average the prediction of all houses of about 30m2, the estimate the *combined* effect of living area and number of rooms, because of their correlation. Suppose that the living area has no effect on the predicted value of a house, only the number of rooms has, the M-Plot would still show that the size of the living area increases the predicted value.
>- M-Plots avoid averaging predictions of unlikely data instances, but they *mix the effect of a feature with the effects of all correlated features*.
>  
>  ![[Pasted image 20240818090711.png]]

---
### Theory

ALE plots average the changes in the predictions and accumulate them over the grid.
$$
\begin{align}
\hat{f}_{S,ALE}(x_S) 
& = \int^{x_S}_{z_{0,S}} E_{X_C\mid X_S=x_s} \bigg[\hat{f}^S\Big(X_s, X_c\Big) \mid X_S=z_s\bigg] dz_S - \text{constant}
\\
& = \Bigg(\int_{x_C} \hat{f}^S\Big(z_s, X_c\Big) d\mathbb{P}\Big(X_C\mid X_S=z_s\Big) d\Bigg) dz_S - \text{constant}
\end{align}
$$
#### ALE plots for the interaction of two features

ALE plots can also show the interaction effect of two features. The calculation principles are the same as for a single feature, but we work with *rectangular cells* instead of intervals.

![[Pasted image 20240818091348.png]]

#### ALE for categorical features

The accumulated local effects method needs – by definition – the feature values to have an order, because the method accumulates effects in a certain direction. Categorical features do not have any natural order. To compute an ALE plot for a categorical feature we have to somehow create or find an order. The order of the categories influences the calculation and interpretation of the accumulated local effects.

One solution is to *order the categories according to their similarity based on the other features*. The distance between two categories is the sum over the distances of each feature. The feature-wise distance compares either the cumulative distribution in both categories, also called Kolmogorov-Smirnov distance (for numerical features) or the relative frequency tables (for categorical features). Once we have the distances between all categories, we use multi-dimensional scaling to reduce the distance matrix to a one-dimensional distance measure. This gives us a similarity-based order of the categories.

---
### Examples

#### Example 1 - Unexpected out-of-sample behaviour

Below is a dummy example where PDPs would fail. The prediction model is mostly a linear regression model, but does something weird at a combination of the two features for which we have never observed instances.

![[Pasted image 20240818091551.png]]
> Two features and the predicted outcome. The model predicts the sum of the two features (shaded background), with the exception that if x1 is greater than 0.7 and x2 less than 0.3, the model always predicts 2. This area is far from the distribution of data (point cloud) and does not affect the performance of the model and also should not affect its interpretation.

See in our little example how the partial dependence plots behave compared to ALE plots.

![[Pasted image 20240818091639.png]]
> Comparison of the feature effects computed with PDP (upper row) and ALE (lower row). The PDP estimates are influenced by the odd behaviour of the model outside the data distribution (steep jumps in the plots). The ALE plots correctly identify that the machine learning model has a linear relationship between features and prediction, ignoring areas without data.

#### Example 2 - Bike rental use case


--- start-multi-column: ID_nc8a
```column-settings
Number of Columns: 2
Largest Column: standard
```

![[Pasted image 20240818091826.png]]
> ALE plots for the bike prediction model by temperature, humidity and wind speed. The temperature has a strong effect on the prediction. The average prediction rises with increasing temperature, but falls again above 25 degrees Celsius. Humidity has a negative effect: When above 60%, the higher the relative humidity, the lower the prediction. The wind speed does not affect the predictions much.

--- column-break ---

![[Pasted image 20240818091837.png]]
>PDPs for temperature, humidity and wind speed. Compared to the ALE plots, the PDPs show a smaller decrease in predicted number of bikes for high temperature or high humidity. The PDP uses all data instances to calculate the effect of high temperatures, even if they are, for example, instances with the season “winter”. The ALE plots are more reliable.

--- end-multi-column

---
### Advantages

1. ALE plots are **unbiased**, which means they still work when features are correlated.
2. ALE plots are **faster to compute than PDPs** and scale with O(n), since the largest possible number of intervals is the number of instances with one interval per instance.
3. The **interpretation of ALE plots is clear** - Conditional on a given value, the relative effect of changing the feature on the prediction can be read from the ALE plot.                   ]

---
### Disadvantages

1. An **interpretation of the effect *across intervals* is not permissible** if the features are *strongly correlated*.
2. ALE effects **may differ from the coefficients specified in a linear regression model** when features interact and are correlated.
3. ALE plots can become **a bit shaky with a high number of intervals**. In this case, reducing the number of intervals makes the estimates more stable, but also smoothens out and hides some of the true complexity of the prediction model. There is **no perfect solution for setting the number of intervals**. If the number is too small, the ALE plots might not be very accurate. If the number is too high, the curve can become shaky.
4. Unlike PDPs, ALE plots are **not accompanied by ICE curves**.
5. **2nd-order ALE estimates have a varying stability across the feature space, which is not visualised in any way**. The reason for this is that each estimation of a local effect in a cell uses a different number of data instances. As a result, all estimates have a different accuracy (but they are still the best possible estimates).
6. **2nd-order effect plots can be a bit annoying to interpret**, as you always have to keep the main effect in mind.
7. Even though ALE plots are not biased in case of correlated features, **interpretation remains difficult when features are strongly correlated**. Because if they have a very strong correlation, it only makes sense to analyze the effect of changing both features together and not in isolation. This disadvantage is not specific to ALE plots, but a general problem of strongly correlated features.


---
## References

- [[Interpretable Machine Learning]]
- [8.2 Accumulated Local Effects (ALE) Plot | Interpretable Machine Learning (christophm.github.io)](https://christophm.github.io/interpretable-ml-book/ale.html)