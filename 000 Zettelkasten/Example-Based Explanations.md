---
tags:
  - explainable-ai
creation-date: 2024-08-18 00:03
---
## Example-Based Explanations

Example-based explanation methods **select particular instances of the dataset to explain the behaviour of ML models** or to explain the underlying data distribution.

Example-based explanations are mostly [[Model-Agnostic XAI Methods|model-agnostic methods]], because they make any ML model more interpretable. 
- The difference to model-agnostic methods is that example-based methods explain a model by selecting instances of the dataset and *not by creating summaries of features* (e.g., [[Permutation Feature Importance|feature importance]] or [[Partial Dependence Plot (PDP)|partial dependence]]).

Example-based explanations only make sense if we can represent an instance of the data in a *human understandable way*. This works well for images, because we can view them directly.

The blueprint of example-based explanations is: 
"Thing B is similar to thing A, and A caused Y, so I predict that B will cause Y as well."
- Implicitly, some ML approaches work example-based. For example:
	1. Decision trees partition data into nodes based on similarities of the data points.
	2. K-nearest neighbours (KNN).

### Techniques

The following interpretation methods are all example-based:
1. [[Counterfactual Explanations]] tells us how an instance has to change to significantly change its prediction. By creating counterfactual instances, we learn about how the model makes its predictions and can explain individual predictions.
2. [[Adversarial Examples]] are counterfactuals used to fool ML-models. The emphasis on flipping the prediction and not explaining it.
3. [[Prototypes and Criticisms|Prototypes]] are a selection of representative instances from the data and criticisms are instances that are not well represented by those prototypes.
4. [[Influential Instances]] are the training data points that were the most influential for the parameters of a prediction model or the predictions themselves. Identifying and analysing influential instances helps to find problems with the data, debug the model, and understand the model's behaviour better.



---
## References

- [[Interpretable Machine Learning]]