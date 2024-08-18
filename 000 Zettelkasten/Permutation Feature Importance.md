---
tags:
  - explainable-ai
creation-date: 2024-08-18 11:39
---
## Permutation Feature Importance

![[Pasted image 20240818114515.png]]

Permutation feature importance *measures the **increase in the prediction error** of the model after we **permuted the feature’s values***, which breaks the relationship between the feature and the true outcome.

The concept is really straightforward: We measure the importance of a feature by calculating the increase in the model’s prediction error after permuting the feature. A feature is “important” if shuffling its values increases the model error, because in this case the model relied on the feature for the prediction.

### Algorithm

The permutation feature importance algorithm based on Fisher, Rudin, and Dominici (2018):
- Input: Trained model $\hat{f}$, feature matrix $X$, target vector $y$, error measure $L(y, \hat{f})$.
1. Estimate the original model error $e_{\text{orig}} = L(y, \hat{f}(X))$.
2. For each feature $j \in \{1, ..., p\}$, do:
	1. Generate feature matrix $X_{\text{perm}}$ by permuting feature $j$ in the data $X$. This breaks the association between feature $j$ and the true outcome $y$.
	2. Estimate error $e_{\text{perm}} = L(Y, \hat{f}(X_\text{perm}))$ based on the predictions of the permuted data.
	3. Calculate permutation feature importance as *quotient* $FI_j = \frac{e_{\text{perm}}}{e_{\text{orig}}}$ or the *difference* $FI_j=e_{\text{perm}} - e_{\text{orig}}$.
3. Sort features by descending $FI$.

Fisher, Rudin, and Dominici (2018) suggest in their paper to *split the dataset in half and swap the values of feature $j$ of the two halves instead of permuting feature $j$*. This is exactly the same as permuting feature $j$, if you think about it. 
- If you want a more accurate estimate, you can estimate the error of permuting feature $j$ by pairing each instance with the value of feature $j$ of each other instance (except with itself).

---
## Advantages

1. **Nice interpretation**.
2. Feature importance provides a **highly compressed, global insight** into the model’s behaviour.
3. A positive aspect of using the error ratio instead of the error difference is that the feature importance measurements are **comparable across different problems**.
4. The importance measure automatically **takes into account all interactions** with other features. By permuting the feature you also destroy the interaction effects with other features. This means that the permutation feature importance takes into account both the main feature effect and the interaction effects on model performance.
5. Permutation feature importance **does not require retraining the model**.

---
## Disadvantages

1. Permutation feature importance is **linked to the error of the model**.
2. You **need access to the true outcome**. If someone only provides you with the model and unlabelled data – but not the true outcome – you cannot compute the permutation feature importance.
3. The permutation feature importance depends on shuffling the feature, which adds randomness to the measurement. When the permutation is repeated, the **results might vary greatly**. Repeating the permutation and averaging the importance measures over repetitions stabilizes the measure, but increases the time of computation.
4. If features are correlated, the permutation feature importance **can be biased by unrealistic data instances**. The problem is the same as with [partial dependence plots](https://christophm.github.io/interpretable-ml-book/pdp.html#pdp): The permutation of features produces unlikely data instances when two or more features are correlated.
5. **Adding a correlated feature can decrease the importance of the associated feature** by splitting the importance between both features.

---
## References

- [[Interpretable Machine Learning]]
- [8.5 Permutation Feature Importance | Interpretable Machine Learning (christophm.github.io)](https://christophm.github.io/interpretable-ml-book/feature-importance.html)