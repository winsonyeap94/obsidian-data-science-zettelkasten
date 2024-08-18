---
tags:
  - explainable-ai
creation-date: 2024-08-18 12:14
---
## Local Interpretable Model-agnostic Explanations (LIME)

![[Pasted image 20240818122522.png]]

Local interpretable model-agnostic explanations (LIME)[50](https://christophm.github.io/interpretable-ml-book/lime.html#fn50) is a paper in which the authors propose a concrete implementation of **local surrogate models**. Surrogate models are trained to approximate the predictions of the underlying black box model. 
- Instead of training a global surrogate model, LIME focuses on training local surrogate models to **explain individual predictions**.

### Intuition

First, forget about the training data and imagine you only have the black box model where you can input data points and get the predictions of the model. You can probe the box as often as you want. Your goal is to understand why the machine learning model made a certain prediction. LIME tests what happens to the predictions when you give variations of your data into the machine learning model.

LIME <mark style="background: #FFF3A3A6;">generates a new dataset consisting of perturbed samples and the corresponding predictions of the black box model</mark>. On this new dataset LIME then trains an **interpretable model**, which is ***weighted by the proximity** of the sampled instances to the instance of interest*. 
- The interpretable model can be anything from the [interpretable models chapter](https://christophm.github.io/interpretable-ml-book/simple.html#simple), for example [Lasso](https://christophm.github.io/interpretable-ml-book/limo.html#lasso) or a [decision tree](https://christophm.github.io/interpretable-ml-book/tree.html#tree). 
- The learned model should be a good approximation of the machine learning model predictions locally, but it does not have to be a good global approximation. This kind of accuracy is also called **local fidelity**.

---
### Algorithm

Mathematically, local surrogate models with interpretability constraint can be expressed as follows:
$$
\text{explanation}(x) = \underset{g\in G}{\arg \min} L(f, g, \pi_x) + \Omega(g)
$$
The explanation model for instance $x$ is the model $g$ (e.g., linear regression model) that minimises loss $L$, which measures how close the explanation is to the prediction of the original model $f$, while the model complexity $\Omega(g)$ is kept low (e.g., prefer fewer features).
- $\Omega(g)$ : Model complexity.
- $G$ : Family of possible explanations (e.g., all possible linear regression models)
- $\pi_x$ : Proximity measure - defines how large the neighbourhood around instance $x$ is that we consider for explanation.

In practice, LIME only optimises the loss part. <mark style="background: #FFF3A3A6;">The user has to determine the complexity</mark> (e.g., by selecting the maximum number of features that the linear regression model may use).

The recipe for training local surrogate models:
1. Select your instance of interest for which you want to have an explanation of its black box prediction.
2. Perturb your dataset and get the black box predictions for these new points.
3. Weight the new samples according to their proximity to the instance of interest.
4. Train a weighted, interpretable model on the dataset with the variations.
5. Explain the prediction by interpreting the local model.

A good choice is [Lasso](https://christophm.github.io/interpretable-ml-book/limo.html#lasso). 
- A Lasso model with a high regularization parameter $\lambda$ yields a model without any feature. 
- By **retraining the Lasso models with slowly decreasing $\lambda$**, one after the other, the features get weight estimates that differ from zero. If there are $K$ features in the model, you have reached the desired number of features. 

Other strategies are **forward or backward selection of features**. 
- This means you either start with the full model (= containing all features) or with a model with only the intercept and then test which feature would bring the biggest improvement when added or removed, until a model with $K$ features is reached.

---
### LIME for Tabular Data

LIME samples are not taken around the instance of interest, but from the training data’s mass center, which is problematic. But it increases the probability that the result for some of the sample points predictions differ from the data point of interest and that LIME can learn at least some explanation.

![[Pasted image 20240818122608.png]]
>  LIME algorithm for tabular data. A) Random forest predictions given features x1 and x2. Predicted classes: 1 (dark) or 0 (light). B) Instance of interest (big dot) and data sampled from a normal distribution (small dots). C) Assign higher weight to points near the instance of interest. D) Signs of the grid show the classifications of the locally learned model from the weighted samples. The white line marks the decision boundary (P(class=1) = 0.5).

![[Pasted image 20240818122626.png]]
> Explanation of the prediction of instance x = 1.6. The predictions of the black box model depending on a single feature is shown as a thick line and the distribution of the data is shown with rugs. Three local surrogate models with different kernel widths are computed. The resulting linear regression model depends on the kernel width: Does the feature have a negative, positive or no effect for x = 1.6?

---
### Advantages

1. Even if you **replace the underlying machine learning model**, you can still use the same local, interpretable model for explanation.
2. When using Lasso or short trees, the resulting **explanations are short (= selective) and possibly contrastive**. Therefore, they make [human-friendly explanations](https://christophm.github.io/interpretable-ml-book/explanation.html#explanation). This is why I see LIME more in applications where the recipient of the explanation is a lay person or someone with very little time. 
	- It is *not sufficient for complete attributions*, so I do not see LIME in compliance scenarios where you might be legally required to fully explain a prediction. Also for debugging machine learning models, it is useful to have all the reasons instead of a few.
3. The **fidelity measure** (how well the interpretable model approximates the black box predictions) gives us a good idea of how reliable the interpretable model is in explaining the black box predictions in the neighbourhood of the data instance of interest.
4. The explanations created with local surrogate models **can use other (interpretable) features than the original model was trained on.**

### Disadvantages

1. **The correct definition of the neighbourhood is a very big, unsolved problem** when using LIME with tabular data. In my opinion it is the biggest problem with LIME and the reason why I would recommend to use LIME only with great care. For each application you have to try different kernel settings and see for yourself if the explanations make sense. Unfortunately, this is the best advice I can give to find good kernel widths.
2. Sampling could be improved in the current implementation of LIME. Data points are sampled from a Gaussian distribution, **ignoring the correlation between features**. This can lead to unlikely data points which can then be used to learn local explanation models.
3. Another really big problem is the **instability** of the explanations. In an article [51](https://christophm.github.io/interpretable-ml-book/lime.html#fn51) the authors showed that the explanations of two very close points varied greatly in a simulated setting. Also, in my experience, if you repeat the sampling process, then the explanations that come out can be different. Instability means that it is difficult to trust the explanations, and you should be very critical.


---
## References

- [[Interpretable Machine Learning]]
- [9.2 Local Surrogate (LIME) | Interpretable Machine Learning (christophm.github.io)](https://christophm.github.io/interpretable-ml-book/lime.html)