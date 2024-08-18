---
tags:
  - explainable-ai
creation-date: 2024-08-18 11:11
---
## Functional Decomposition

![[Pasted image 20240818111325.png]]

A supervised machine learning model can be viewed as a function that takes a high-dimensional feature vector as input and produces a prediction or classification score as output. Functional decomposition is an interpretation technique that **deconstructs** the high-dimensional function and expresses it as a **sum of individual feature effects and interaction effects** that can be visualized.

There are different methods that can be used to perform functional decomposition:
1. (Generalised) Functional ANOVA
2. [[Accumulated Local Effects (ALE)]]
3. Statistical regression models

### (Generalised) Functional ANOVA

A requirement for this approach is that the model prediction function $\hat{f}$ is square integrable. As with any functional decomposition, the functional ANOVA decomposes the function into components:
$$
\hat{f}(x) = \sum_{S\subseteq\{1, ..., p\}} \hat{f}_S(x_S)
$$
Based on Functional ANOVA,
$$
\begin{align}
\hat{f}(x) 
& = \int_{X_{-S}} \Bigg(\hat{f}(x) - \sum_{V\subset S} \hat{f}_V(x)\Bigg) dX_{-S} \\
& = \int_{X_{-S}} \bigg(\hat{f}(x)\bigg) dX_{-S} - \int_{X_{-S}}\bigg(\sum_{V\subset S}\hat{f}_V(x)\bigg)dX_{-S}
\end{align}
$$
- On the left side is the integral over the prediction function with respect to the features excluded from the set $S$, denoted with $-S$.
	- For example, if we compute the 2-way interaction component for features 2 and 3, we would integrate over features 1, 4, 5, …
- From this interval, we subtract all components with subsets of $S$. This subtraction removes the effect of all lower-order effects and centres the effect.

Problems arise with the functional ANOVA when features are correlated. As a solution, the generalized functional ANOVA has been proposed.
- Similar to most interpretation techniques based on sampling data (such as the PDP), the functional ANOVA can produce misleading results when features are correlated. If we integrate over the uniform distribution, when in reality features are dependent, we create a new dataset that deviates from the joint distribution and extrapolates to unlikely combinations of feature values.

Hooker (2007) [40](https://christophm.github.io/interpretable-ml-book/decomposition.html#fn40) proposed the generalized functional ANOVA, a decomposition that works for dependent features. It is a generalization of the functional ANOVA we encountered earlier, which means that the functional ANOVA is a special case of the generalized functional ANOVA. The components are defined as projections of f onto the space of additive functions:
$$
\hat{f}_S(x_S) = \text{argmin}_{g_S \in L^2(\mathbb{R}^S)_{S\in P}} \int\Bigg(\hat{f}(x) - \sum_{S \subset P} g_S(x_S)\Bigg)^2 w(x)\ dx
$$


---
## Advantages

1. Functional decomposition gives us a **theoretical justification** for decomposing high-dimensional and complex machine learning models into individual effects and interactions – a necessary step that allows us to interpret individual effects. Functional decomposition is the core idea for techniques such as statistical regression models, [[Accumulated Local Effects (ALE)]], (generalized) functional ANOVA, [[Partial Dependence Plot (PDP)]], the [[Feature Interaction#Friedman's H-Statistic|H-Statistic]], and [[Individual Conditional Expectations (ICE)]] curves.
2. Functional decomposition also provides a **better understanding of other methods**. For example, [permutation feature importance](https://christophm.github.io/interpretable-ml-book/feature-importance.html#feature-importance) breaks the association between a feature and the target. Viewed through the functional decomposition lens, we can see that the permutation “destroys” the effect of all components in which the feature was involved.

---
## Disadvantages

1. The concept of functional decomposition quickly reaches its **limits for high-dimensional components** beyond interactions between two features. Not only does this exponential explosion in the number of features limit practicability, since we cannot easily visualize higher-order interactions, but computational time is insane if we were to compute all interactions.
2. Each method of functional decomposition method has their **individual disadvantages**. The bottom-up approach – constructing regression models – is a quite manual process and imposes many constraints on the model that can affect predictive performance. Functional ANOVA requires independent features. Generalized functional ANOVA is very difficult to estimate. Accumulated local effect plots do not provide a variance decomposition.
3. The functional decomposition approach is **more appropriate for analyzing tabular data than text or images**.

---
## References

- [[Interpretable Machine Learning]]
- [8.4 Functional Decomposition | Interpretable Machine Learning (christophm.github.io)](https://christophm.github.io/interpretable-ml-book/decomposition.html)