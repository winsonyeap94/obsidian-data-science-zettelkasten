---
tags:
  - explainable-ai
creation-date: 2024-08-17 23:54
---
## Model-Agnostic XAI Methods

![[Pasted image 20240818000250.png]]

Model-agnostic interpretation methods **separate the explanations from the ML model**.

The advantage of model-agnostic interpretation methods over model-specific ones is their *flexibility* -- ML developers are free to use any ML model when the interpretation methods can be applied to any model.

Desirable aspects of a model-agnostic explanation system are:
1. **Model flexibility** - The interpretation method can work with any ML model.
2. **Explanation flexibility** - Not limited to a certain form of explanation. In some cases it might be useful to have a linear formula, in other cases a graphic with feature importances.
3. **Representation flexibility** - The explanation system should be able to use a different feature representation as the model being explained. For a text classifier that uses abstract word embedding vectors, it might be preferable to use the presence of individual words for the explanation.

### Alternatives

An alternative to model-agnostic interpretation methods is to use only [[interpretable models]], which often has the big disadvantage that predictive performance is lost compared to other ML models as we limit ourselves to one type of model.

Another alternative is to use [[model-specific interpretation methods]]. The disadvantage of this is that it also binds us to one model type and it will be difficult to switch to something else.

### Techniques

1. Global Model-Agnostic Methods
	1. [[Partial Dependence Plot (PDP)]]
	2. [[Accumulated Local Effects (ALE)]]
	3. [[Feature Interaction]]
	4. [[Functional Decomposition]]
	5. [[Permutation Feature Importance]]
	6. [[Global Surrogate]]
	7. [[Prototypes and Criticisms]]
2. Local Model-Agnostic Methods
	1. [[Individual Conditional Expectations (ICE)]]
	2. [[Local Interpretable Model-agnostic Explanations (LIME)]]
	3. [[Counterfactual Explanations]]
	4. [[Scoped Rules (Anchors)]]
	5. [[Shapley Values]]
	6. [[SHAP]]
3. Neural Network Interpretation
	1. [[Learned Features]]
	2. [[Pixel Attribution (Saliency Maps)]]
	3. [[Detecting Concepts]]
	4. [[Adversarial Examples]]
	5. [[Influential Instances]]



---
## References

- [[Interpretable Machine Learning]]
