---
tags:
  - explainable-ai
creation-date: 2024-08-18 11:47
---
## Global Surrogate

A global surrogate model is an ***interpretable model** that is trained to approximate the predictions of a black box model*. We can draw conclusions about the black box model by interpreting the surrogate model. Solving machine learning interpretability by using more machine learning!

We want to approximate our black box prediction function $f$ as closely as possible with the surrogate model prediction function $g$, under the constraint that $g$ is interpretable. For the function $g$ any interpretable model – for example from the [interpretable models chapter](https://christophm.github.io/interpretable-ml-book/simple.html#simple) – can be used.
- For example, a linear model, or a decision tree.

Perform the following steps to obtain a surrogate model:
1. Select a dataset $X$. This can be the same dataset that was used for training the black box model or a new dataset from the same distribution. You could even select a subset of the data or a grid of points, depending on your application.
2. For the selected dataset $X$, get the predictions of the black box model.
3. Select an interpretable model type (linear model, decision tree, …).
4. Train the interpretable model on the dataset $X$ and its predictions.
5. Congratulations! You now have a surrogate model.
6. Measure how well the surrogate model replicates the predictions of the black box model.
7. Interpret the surrogate model.

One way to measure how well the surrogate replicates the black box model is the R-squared measure:
$$
R^2 = 1 - \frac{SSE}{SST} = 1 - \frac{\sum^n_{i=1}(\hat{y}_*^{(i)} - \hat{y}^{(i)})^2}{\sum^n_{i=1}(\hat{y}^{(i)}) - \bar{\hat{y}})^2}
$$
---
### Advantages

1. The surrogate model method is **flexible**: Any model from the [interpretable models chapter](https://christophm.github.io/interpretable-ml-book/simple.html#simple) can be used.
2. The approach is very **intuitive** and straightforward. This means it is easy to implement, but also easy to explain to people not familiar with data science or machine learning.

### Disadvantages

1. You have to be aware that you draw **conclusions about the model and not about the data**, since the surrogate model never sees the real outcome.
2. It is not clear what the best **cut-off for R-squared** is in order to be confident that the surrogate model is close enough to the black box model. 80% of variance explained? 50%? 99%?
3. We can measure how close the surrogate model is to the black box model. Let us assume we are not very close, but close enough. It could happen that the interpretable model is very **close for one subset of the dataset, but widely divergent for another subset**. In this case the interpretation for the simple model would not be equally good for all data points.
4. The interpretable model you choose as a surrogate **comes with all its advantages and disadvantages**.

---
## References

- [[Interpretable Machine Learning]]
- [8.6 Global Surrogate | Interpretable Machine Learning (christophm.github.io)](https://christophm.github.io/interpretable-ml-book/global.html)
