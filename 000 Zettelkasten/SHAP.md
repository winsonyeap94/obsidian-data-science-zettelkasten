---
tags:
  - explainable-ai
creation-date: 2024-08-18 14:46
---
## SHAP

![[Pasted image 20240818144838.png]]

SHAP (SHapley Additive exPlanations) is a method to explain individual predictions. SHAP is based on the game theoretically optimal [[Shapley Values]].

There are two reasons why SHAP is different from Shapley Values:
1. The SHAP authors proposed **KernelSHAP**, an alternative, kernel-based estimation approach for Shapley values inspired by *local surrogate models*. The authors also proposed **TreeSHAP**, an efficient estimation approach for tree-based models.
2. SHAP comes with many **global interpretation methods** based on aggregation of Shapley values.

### Definition

One innovation that SHAP brings to the table is that the Shapley value explanation is represented as an **additive feature attribution method, a linear model**. That view connects LIME and Shapley values. SHAP specifies the explanation as:
$$
g(z') = \phi_0 + \sum^M_{j=1} \phi_j z'_j
$$
- $g$ : explanation model
- $z' \in \{0, 1\}^M$ : coalition vector
- $M$ : maximum coalition size
- $\phi_j \in \mathbb{R}$ : feature attribution for feature $j$, the Shapley values

 To compute Shapley values, we simulate that only some feature values are playing (“present”) and some are not (“absent”). The representation as a linear model of coalitions is a trick for the computation of the $\phi$ s. For $x$, the instance of interest, the coalition vector $x'$ is a vector of all $1$’s, i.e. all feature values are “present”. The formula simplifies to:
$$
g(x') = \phi_0 + \sum^M_{j=1} \phi_j
$$

Shapley values are the only solution that satisfies properties of [[Shapley Values#Efficiency, Symmetry, Dummy, and Additivity|Efficiency, Symmetry, Dummy, and Additivity]]. SHAP also satisfies these, since it computes Shapley values. In the SHAP paper, you will find discrepancies between SHAP properties and Shapley properties. SHAP describes the following three desirable properties:

>[!info]- (1) Local Accuracy
>$$
>\hat{f}(x) = g(x') = \phi_0 + \sum^M_{j=1}\phi_j x'_j
>$$
>If you define $\phi_0 = E_X(\hat{f}(x))$ and set all $x'_j$ to $1$, this is the Shapley efficiency property. Only with a different name and using the coalition vector.
>$$
>\hat{f}(x) = \phi_0 + \sum^M_{j=1}\phi_j x'_j = E_X(\hat{f}(X)) + \sum^M_{j=1}\phi_j
>$$

>[!info]- (2) Missingness
>$$
>x'_j=0 \Rightarrow \phi_j = 0
>$$
>Missingness says that a missing feature gets an attribution of zero.

>[!info]- (3) Consistency
>Let $\hat{f}_x(z') = \hat{f}(h_x(z'))$ and $z'_{-j}$ indicate that $z'_j=0$, for any two models $f$ and $f'$ that satisfy:
>$$
>\hat{f}'_x(z') - \hat{f}'_x(z'_{-j}) \ge \hat{f}_x(z') - \hat{f}_x(z'_{-j})
>$$
>for all inputs $z' \in \{0, 1\}^M$, then:
>$$
>\phi_j(\hat{f}', x) \ge \phi_j(\hat{f}, x)
>$$
>
>The consistency property says that if a model changes so that the marginal contribution of a feature value increases or stays the same (regardless of other features), the Shapley value also increases or stays the same.


---
### KernelSHAP

KernelSHAP estimates for an instance $x$ the contributions of each feature value to the prediction.

KernelSHAP consists of five steps:
1. Sample coalitions $z'_k \in \{0, 1\}^M, \ \ k\in\{1, ..., K\}$ 
   ($1$ = feature present in coalition, $0$ = feature absent).
2. Get prediction for each $z'_k$ by first converting $z'_k$ to the original feature space and then applying model $\hat{f} : \hat{f}(h_x(z'_k))$.
3. Compute the weight for each $z'_k$ with the SHAP kernel.
4. Fit weighted linear model.
5. Return Shapley values $\phi_k$, the coefficients from the linear model.

We can create a random coalition by repeated coin flips until we have a chain of 0’s and 1’s.
- For example, the vector of (1,0,1,0) means that we have a coalition of the first and third features. 
- *The $K$ sampled coalitions become the **dataset** for the regression model. The target for the regression model is the **prediction for a coalition**.*

However, the model has not been trained on these binary coalition data and cannot make predictions for them. To get from coalitions of feature values to valid data instances, we need a function $h_x(z') = z$ where $h_x : \{0, 1\}^M \rightarrow \mathbb{R}^p$.
- The function $h_x$ *maps $1$'s to the corresponding value from the instance $x$ that we want to explain*.
- For tabular data, it maps $0$'s to the values of another instance that we sample from the data.
	- This means that we equate "feature value is absent" with <mark style="background: #FFF3A3A6;">"feature value is replaced by random feature value from data"</mark>.

![[Pasted image 20240818151140.png]]

$h_x$ for tabular data treats feature $X_j$ and $X_{-j}$ (the other features) as **independent** and integrates over the marginal distribution:
$$
\hat{f}(h_x(z')) = E_{X_{-j}}[\hat{f}(x)]
$$

>[!warning] Independency assumption
>Sampling from the marginal distribution means ignoring the dependence structure between present and absent features. KernelSHAP therefore suffers from the same problem as all permutation-based interpretation methods. The estimation puts too much weight on unlikely instances. Results can become unreliable. But it is necessary to sample from the marginal distribution. 
>
>The solution would be to **sample from the conditional distribution**, which changes the value function, and therefore the game to which Shapley values are the solution. As a result, the Shapley values have a different interpretation: For example, *a feature that might not have been used by the model at all can have a non-zero Shapley value when the conditional sampling is used*. **For the marginal game, this feature value would always get a Shapley value of 0**, because otherwise it would violate the Dummy axiom.

For images, the following figure describes a possible mapping function:

![[Pasted image 20240818151600.png]]

#### Difference in Weighting vs LIME

The big difference to [[Local Interpretable Model-agnostic Explanations (LIME)]] is the weighting of the instances in the regression model. 
- LIME weights the instances according to how close they are to the original instance. The more 0’s in the coalition vector, the smaller the weight in LIME. 
- SHAP weights the sampled instances according to **the weight the coalition would get in the Shapley value estimation**. 
	- *Small coalitions* (few 1’s) and *large coalitions* (i.e. many 1’s) get the largest weights. 
	- The intuition behind it is: We learn most about individual features if we can study their effects in isolation. If a coalition consists of a single feature, we can learn about this feature’s isolated main effect on the prediction. If a coalition consists of all but one feature, we can learn about this feature’s total effect (main effect plus feature interactions). <mark style="background: #FFF3A3A6;">If a coalition consists of half the features, we learn little about an individual feature’s contribution, as there are many possible coalitions with half of the features</mark>. 

To achieve Shapley compliant weighting, Lundberg et al. propose the SHAP kernel:
$$
\pi_x(z') = \frac{(M-1)}{{M \choose {|z'|}}|z'| (M - |z'|)}
$$
- $M$ : maximum coalition size
- $|z'|$ : Number of present features in instance $z'$

We have the data, the target and the weights; Everything we need to build our weighted linear regression model:
$$
g(z') = \phi_0 + \sum^M_{j=1}\phi_jz'_j
$$
We train the linear model $g$ by optimising the following **loss function** $L$:
$$
L(\hat{f}, g, \pi_x) = \sum_{z'\in Z}\Big[\hat{f}(h_x(z')) - g(z')\Big]^2 \pi_x(z')
$$
- $Z$ : training data

This is the good old boring sum of squared errors that we usually optimize for linear models. The **estimated coefficients of the model**, the $\phi_j$’s, are the Shapley values.

---
### TreeSHAP

>[!warning] TreeSHAP was introduced as a fast, model-specific alternative to KernelSHAP, but it turned out that it can produce **unintuitive feature attributions**.

TreeSHAP defines the value function using the **conditional expectation** $E_{X_j \mid X_{-j}}(\hat{f}(x) \mid x_j)$ instead of the marginal expectation.
- The problem with the conditional expectation is that *features that have no influence on the prediction function $f$ can get a TreeSHAP estimate different from zero*.
- The non-zero estimate can happen when the <mark style="background: #FFF3A3A6;">feature is correlated with another feature that actually has an influence on the prediction</mark>.
- In other words, it **violates the Dummy axiom**.

#### Intuition

Below is an intuition on how we can compute the expected prediction for a single tree, an instance $x$, and feature subset $S$.
- If we conditioned on all features (if $S$ was the set of all features), then the prediction from the node in which the instance $x$ falls would be the expected prediction.
- If we would not condition the prediction on any feature (if $S$ was empty), we would use the *weighted average of predictions of all terminal nodes*.
- If $S$ contains some, but not all, features, we *ignore predictions of unreachable nodes*. From the remaining terminal nodes, we average the predictions *weighted by node sizes* (i.e., number of training samples in that node).

The problem is that we have to apply this procedure for each possible subset $S$ of the feature values. TreeSHAP computes in polynomial time instead of exponential. The basic idea is to **push all possible subsets $S$ down the tree at the same time**. For each decision node we have to keep track of the number of subsets. This depends on the subsets in the parent node and the split feature.
- For example, when the first split in a tree is on feature $x_3$, then all the subsets that contain feature $x_3$ will go to one node (the one where $x$ goes). Subsets that do not contain feature $x_3$ go to both nodes with reduced weight. Unfortunately, subsets of different sizes have different weights. The algorithm has to keep track of the overall weight of the subsets in each node. This complicates the algorithm.

Thanks to the Additivity property of Shapley values, the Shapley values of a tree ensemble is the (weighted) average of the Shapley values of the individual trees.

---
### Advantages

1. Since SHAP computes Shapley values, all the advantages of Shapley values apply.
	- SHAP has a **solid theoretical foundation** in game theory. The prediction is **fairly distributed** among the feature values. We get **contrastive explanations** that compare the prediction with the average prediction.
2. SHAP **connects LIME and Shapley values**. This is very useful to better understand both methods. It also helps to unify the field of interpretable machine learning.
3. SHAP has a **fast implementation for tree-based models**. I believe this was key to the popularity of SHAP, because the biggest barrier for adoption of Shapley values is the slow computation.
4. The fast computation makes it possible to compute the many Shapley values needed for the **global model interpretations**.

### Disadvantages

1. **KernelSHAP is slow**. This makes KernelSHAP impractical to use when you want to compute Shapley values for many instances. Also all global SHAP methods such as SHAP feature importance require computing Shapley values for a lot of instances.
2. **KernelSHAP ignores feature dependence**. Most other permutation based interpretation methods have this problem. By replacing feature values with values from random instances, it is usually easier to randomly sample from the marginal distribution. However, if features are dependent, e.g. correlated, this leads to putting too much weight on unlikely data points. TreeSHAP solves this problem by explicitly modelling the conditional expected prediction.
3. **TreeSHAP can produce unintuitive feature attributions**. While TreeSHAP solves the problem of extrapolating to unlikely data points, it does so by changing the value function and therefore slightly changes the game. TreeSHAP changes the value function by relying on the conditional expected prediction. With the change in the value function, features that have no influence on the prediction can get a TreeSHAP value different from zero.
4. The disadvantages of Shapley values also apply to SHAP: Shapley values **can be misinterpreted** and access to data is needed to compute them for new data (except for TreeSHAP).
5. It is **possible to create intentionally misleading interpretations** with SHAP, which can hide biases

---
## References

- [[Interpretable Machine Learning]]
- [9.6 SHAP (SHapley Additive exPlanations) | Interpretable Machine Learning (christophm.github.io)](https://christophm.github.io/interpretable-ml-book/shap.html)