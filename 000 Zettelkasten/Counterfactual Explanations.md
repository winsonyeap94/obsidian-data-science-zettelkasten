---
tags:
  - explainable-ai
creation-date: 2024-08-18 12:30
---
## Counterfactual Explanations

![[Pasted image 20240818123112.png]]

A counterfactual explanation describes a causal situation in the form: “If X had not occurred, Y would not have occurred”. 
- For example: “If I hadn’t taken a sip of this hot coffee, I wouldn’t have burned my tongue”. Event Y is that I burned my tongue; cause X is that I had a hot coffee. 
- Thinking in counterfactuals requires imagining a *hypothetical reality that contradicts the observed facts* (for example, a world in which I have not drunk the hot coffee), hence the name “counterfactual”. 

In interpretable machine learning, counterfactual explanations can be used to **explain predictions of individual instances**. 
- The **“event”** is the *predicted outcome* of an instance, the **“causes”** are the particular *feature values* of this instance that were input to the model and “caused” a certain prediction.

Displayed as a graph, the relationship between the inputs and the prediction is very simple: The feature values cause the prediction.

![[Pasted image 20240818123223.png]]

We simply change the feature values of an instance before making the predictions and we analyze how the prediction changes. We are interested in scenarios in which the prediction changes in a relevant way, like a flip in predicted class (for example, credit application accepted or rejected), or in which the prediction reaches a certain threshold (for example, the probability for cancer reaches 10%). **A counterfactual explanation of a prediction describes the smallest change to the feature values that changes the prediction to a predefined output.**

Unlike [[Prototypes and Criticisms]], counterfactuals do not have to be actual instances from the training data, but can be a new combination of feature values.

### How do we define a good counterfactual explanation?

1. A counterfactual instance **produces the predefined prediction as closely as possible**.
	- It is not always possible to find a counterfactual with the predefined prediction. For example, in a classification setting with two classes, a rare class and a frequent class, the model might always classify an instance as the frequent class.
	- Changing the feature values so that the predicted label would flip from the frequent class to the rare class might be impossible. We therefore want to *relax the requirement* that the prediction of the counterfactual must match the predefined outcome exactly.
	- In the classification example, we could look for a counterfactual where the predicted probability of the rare class is increased to 10% instead of the current 2%. The question then is, what are the minimal changes in the features so that the predicted probability changes from 2% to 10% (or close to 10%)?

2. A counterfactual should be **as similar as possible to the instance regarding feature values**.
	- The distance between two instances can be measured, for example, with the Manhattan distance or the Gower distance if we have both discrete and continuous features.
	- The counterfactual should not only be close to the original instance, but should also **change as few features as possible**.
	- To measure how good a counterfactual explanation is in this metric, we can simply count the number of changed features or, in fancy mathematical terms, measure the $L_0$ norm between counterfactual and actual instance.

3. It is often desirable to generate **multiple, diverse counterfactual explanations** so that the decision-subject gets access to multiple viable ways of generating a different outcome.
	- For instance, continuing our loan example, one counterfactual explanation might suggest only to double the income to get a loan, while another counterfactual might suggest shifting to a nearby city and increase the income by a small amount to get a loan.

4. A counterfactual instance should have **feature values that are likely**.
	- It is even better when the counterfactual is likely according to the joint distribution of the data.

---
### Generating Counterfactual Explanations

First, we define a *loss function* based on the criteria mentioned above. This loss takes as input the instance of interest, a counterfactual and the desired (counterfactual) outcome. Then, we can find the counterfactual explanation that minimizes this loss using an optimization algorithm. Many methods proceed in this way, but differ in their definition of the loss function and optimization method.

#### 1 - Method by Wachter et al.

Wachter et al. suggest minimising the following loss:
$$
L(x, x', y', \lambda) = \lambda \cdot (\hat{f}(x') - y')^2 + d(x, x')
$$
- The first term is the quadratic distance between the model prediction for the counterfactual $x'$ and the desired outcome $y'$, which the user must define in advance. 
- The second term is the distance $d$ between the instance $x$ to be explained and the counterfactual $x'$. 
- The loss measures how far the predicted outcome of the counterfactual is from the predefined outcome and how far the counterfactual is from the instance of interest. 
- The distance function $d$ is defined as the ***Manhattan distance** weighted with the inverse median absolute deviation (MAD) of each feature*.
$$
d(x,x') = \sum^p_{j=1}\frac{|x_j - x'_j|}{MAD_j}
$$
The feature-wise distances are scaled by the inverse of the median absolute deviation of feature $j$ over the dataset defined as:
$$
MAD_j = \underset{i\in\{1, ...,n\}}{\text{median}} \big(| x_{i,j} - \underset{l\in{\{1, ..., n\}}}{\text{median}}(x_{l,j}) | \big)
$$
The MAD is the equivalent of the variance of a feature, but instead of using the mean as the center and summing over the square distances, we use the median as the center and sum over the absolute distances. The proposed distance function has the advantage over the Euclidean distance that it is more robust to outliers.

The parameter $\lambda$ **balances the distance in prediction (first term) against the distance in feature values (second term)**. 
- A higher value of $\lambda$ means that we prefer counterfactuals with predictions close to the desired outcome y’, a lower value means that we prefer counterfactuals $x'$ that are very similar to $x$ in the feature values. If $\lambda$ is very large, the instance with the prediction closest to $y'$ will be selected, regardless how far it is away from $x$.
- The authors of the method suggest instead of selecting a value for $\lambda$ to select a tolerance $\epsilon$ for how far away from $y'$ the prediction of the counterfactual instance is allowed to be. This constraint can be written as:
$$
|\hat{f}(x') - y'| \le \epsilon
$$

Overall, the recipe for producing the counterfactuals is simple:
1. Select an instance $x$ to be explained, the desired outcome $y'$, a tolerance $\epsilon$ and a (low) initial value for $\lambda$.
2. Sample a random instance as initial counterfactual.
3. Optimize the loss with the initially sampled counterfactual as starting point.
4. While $|\hat{f}(x') - y'| > \epsilon$:
    - Increase $\lambda$.
    - Optimize the loss with the current counterfactual as starting point.
    - Return the counterfactual that minimizes the loss.
5. Repeat steps 2-4 and return the list of counterfactuals or the one that minimizes the loss.

>[!warning] Disadvantages of this specific method
>1. It **only takes the first and second criteria into account** not the last two (“produce counterfactuals with only a few feature changes and likely feature values”). $d$ does not prefer sparse solutions since increasing 10 features by 1 will give the same distance to $x$ as increasing one feature by 10. **Unrealistic feature combinations are not penalised**.
> 2. The method does not handle **categorical features** with many different levels well. The authors of the method suggested running the method separately for each combination of feature values of the categorical features, but this will lead to a combinatorial explosion if you have multiple categorical features with many values. For example, six categorical features with ten unique levels would mean one million runs.

#### 2 - Method by Dandl et al.

Dandl et al. suggest to simultaneously minimise a four-objective loss:
$$
L(x, x', y', X^{obs}) = \Big( o_1(\hat{f}(x'), y'),\ o_2(x, x'),\ o_3(x, x'),\ o_4(x', X^{obs}) \Big)
$$
Each of the four objectives $o_1$ to $o_4$ corresponds to one of the four criteria mentioned above.

The first objective $o_1$ reflects that the prediction of **our counterfactual $x'$ should be as close as possible to our desired prediction $y'$**. We therefore want to minimise the distance between $\hat{f}(x')$ and $y'$, here calculated by the Manhattan metric ($L_1$ norm):
$$
o_1(\hat{f}(x'), y') = 
\begin{cases}
0 & \text{if}\ \hat{f}(x')\in y' \\
\underset{y'\in y'}{\inf} |\hat{f}(x') - y'| & \text{otherwise}
\end{cases}
$$

The second objective $o_2$ reflects that **our counterfactual should be as similar as possible to our instance $x$**. It quantifies the distance between $x'$ and $x$ as the Gower distance:
$$
o_2(x, x') = \frac{1}{p}\sum^p_{j=1}\delta_G(x_j, x'_j)
$$
- $p$ : Number of features

The Gower distance can handle both numerical and categorical features, but does not count **how many features were changed**. Therefore, we count the number of features in a third objective $o_3$ using the $L_0$ norm:
$$
o_3(x, x') = || x-x' ||_0 = \sum^p_{j=1} \mathbb{I}_{x'_j \neq x_j}
$$
By minimizing $o_3$ we aim for our third criterion – sparse feature changes.

The fourth objective $o_4$ reflects that our **counterfactuals should have likely feature values/combinations**. 
- We can infer how “likely” a data point is using the training data or another dataset. We denote this dataset as $X_{obs}$. As an approximation for the likelihood, $o_4$ measures the average Gower distance between $x'$ and the nearest observed data point $x^{[1]} \in X^{obs}$.
$$
o_4(x', X^{obs}) = \frac{1}{p}\sum^p_{j=1}\delta_G(x'_j, x_j^{[1]})
$$

Compared to [[Counterfactual Explanations#1 - Method by Wachter et al.|the method by Wachter et al.]], this method *has no balancing/weighting terms like $\lambda$*. We do not want to collapse the four objectives $o_1$, $o_2$, $o_3$ and $o_4$ into a single objective by summing them up and weighting them, but we want to optimize all four terms simultaneously.
- We use the **Nondominated Sorting Genetic Algorithm (NSGA-II)** to do this.
- The algorithm consists of four steps that are repeated until a stopping criterion is met, for example, a maximum number of iterations/generations. The following figure visualizes the four steps of one generation.

![[Pasted image 20240818133149.png]]

---
### Advantages

1. **The interpretation of counterfactual explanations is very clear**. If the feature values of an instance are changed according to the counterfactual, the prediction changes to the predefined prediction. There are no additional assumptions and no magic in the background. This also means it is not as dangerous as methods like [[Local Interpretable Model-agnostic Explanations (LIME)]], where it is unclear how far we can extrapolate the local model for the interpretation.
2. The counterfactual method creates a new instance, but we can also summarize a counterfactual by reporting which feature values have changed. This gives us **two options for reporting our results**. You can either report the counterfactual instance or highlight which features have been changed between the instance of interest and the counterfactual instance.
3. The **counterfactual method does not require access to the data or the model**. It only requires access to the model’s prediction function, which would also work via a web API, for example.
4. The method **works also with systems that do not use machine learning**. We can create counterfactuals for any system that receives inputs and returns outputs.
5. **The counterfactual explanation method is relatively easy to implement**, since it is essentially a loss function (with a single or many objectives) that can be optimized with standard optimizer libraries.

### Disadvantages

1. **For each instance you will usually find multiple counterfactual explanations (Rashomon effect)**. This is inconvenient - most people prefer simple explanations over the complexity of the real world. It is also a practical challenge. Let us say we generated 23 counterfactual explanations for one instance. Are we reporting them all? Only the best? What if they are all relatively “good”, but very different? These questions must be answered anew for each project.

---
## References

- [[Interpretable Machine Learning]]
- [9.3 Counterfactual Explanations | Interpretable Machine Learning (christophm.github.io)](https://christophm.github.io/interpretable-ml-book/counterfactual.html)