---
tags:
  - explainable-ai
creation-date: 2024-08-18 14:02
---
## Shapley Values

![[Pasted image 20240818141146.png]]

The Shapley value, coined by Shapley (1953)[63](https://christophm.github.io/interpretable-ml-book/shapley.html#fn63), is a method for ***assigning pay-outs** to players depending on their **contribution to the total pay-out***. Players cooperate in a coalition and receive a certain profit from this cooperation.

In the context of machine learning, 
- The **"game"** is the prediction task for a single instance of the dataset.
- The **"gain"** is the actual prediction for this instance minus the average prediction for all instances.
- The **"players"** are the feature values of the instance that collaborate to receive the gain (i.e., predict a certain value).

---
### How do we calculate the Shapley value for one feature?

The Shapley value is the **average marginal contribution** of a feature value across all possible coalitions.

In the following figure we evaluate the contribution of the `cat-banned` feature value when it is added to a coalition of `park-nearby` and `area-50`. We simulate that only `park-nearby`, `cat-banned` and `area-50` are in a coalition by randomly drawing another apartment from the data and using its value for the floor feature. The value `floor-2nd` was replaced by the randomly drawn `floor-1st`. Then we predict the price of the apartment with this combination (€310,000). In a second step, we remove `cat-banned` from the coalition by replacing it with a random value of the cat allowed/banned feature from the randomly drawn apartment. In the example it was `cat-allowed`, but it could have been `cat-banned` again. We predict the apartment price for the coalition of `park-nearby` and `area-50` (€320,000). The contribution of `cat-banned` was €310,000 - €320,000 = -€10,000. This estimate depends on the values of the randomly drawn apartment that served as a “donor” for the cat and floor feature values. We will get better estimates if we repeat this sampling step and average the contributions.

![[Pasted image 20240818141321.png]]

We repeat this computation for all possible coalitions. The Shapley value is the average of all the marginal contributions to all possible coalitions. The computation time increases exponentially with the number of features. One solution to keep the computation time manageable is to compute contributions for only a few samples of the possible coalitions.

The following figure shows all coalitions of feature values that are needed to determine the Shapley value for `cat-banned`. The first row shows the coalition without any feature values. The second, third and fourth rows show different coalitions with increasing coalition size, separated by “|”. All in all, the following coalitions are possible:

- `No feature values`
- `park-nearby`
- `area-50`
- `floor-2nd`
- `park-nearby`+`area-50`
- `park-nearby`+`floor-2nd`
- `area-50`+`floor-2nd`
- `park-nearby`+`area-50`+`floor-2nd`.

For each of these coalitions we compute the predicted apartment price with and without the feature value `cat-banned` and take the difference to get the marginal contribution. The Shapley value is the **(weighted) average of marginal contributions**. We replace the feature values of features that are not in a coalition with random feature values from the apartment dataset to get a prediction from the machine learning model.

![[Pasted image 20240818141438.png]]
If we estimate the Shapley values for all feature values, we get the complete distribution of the prediction (minus the average) among the feature values.

---
### Examples and Interpretation

The interpretation of the Shapley value for feature value $j$ is:
- The value of the $j$-th feature contributed $\phi_j$ to the prediction of this particular instance *compared to the average prediction* for the dataset.

![[Pasted image 20240818141659.png]]

>[!warning] Be careful to interpret the Shapley value correctly.
>The Shapley value is the average contribution of a feature value to the prediction in different coalitions.
>The Shapley value is **NOT the difference in prediction** when we would remove the feature from the model. 

---
### Algorithm

The Shapley value is defined via a **value function $val$** of players in $S$.

The Shapley value of a feature value is its contribution to the pay-out, weighted and summed over all possible feature value combinations.
$$
\phi_j(val) = \sum_{S\subseteq\{1,...,p\} ∖ \{j\}} \frac{|S|! (p-|S| -1)!}{p!} \Big(val(S\cup\{j\}) - val(S)\Big)
$$
- $S$ : subset of the features used in the model
- $x$ : vector of feature values of the instance to be explained
- $p$ : number of features
- $val_x(S)$ : prediction for feature values in set $S$ that are marginalised over features that are not included in set $S$
$$
val_x(S) = \int\hat{f}(x_1, ..., x_p) \ d\mathbb{P}_{x\notin S} - E_X(\hat{f}(X))
$$
We actually perform multiple integrations for each feature that is not contained in $S$. 
- A concrete example: The ML model works with 4 features $x_1$, $x_2$, $x_3$, and $x_4$, and we evaluate the prediction for the coalition $S$ consisting of feature values $x_1$ and $x_3$.
$$
val_x(S) = val_x(\{1, 3\}) = \int_\mathbb{R} \int_\mathbb{R} \hat{f}(x_1, X_2, x_3, X_4)\ d\mathbb{P}_{X_2, X_4} - E_X(\hat{f}(X))
$$
#### Efficiency, Symmetry, Dummy, and Additivity

The Shapley value is the only attribution method that satisfies the properties **Efficiency**, **Symmetry**, **Dummy** and **Additivity**, which together can be considered a definition of a fair pay-out.

>[!info]- Efficiency
>The feature contributions must add up to the difference of prediction for $x$ and the average.
>$$
>\sum^p_{j=1} \phi_j = \hat{f}(x) - E_X(\hat{f}(X))
>$$

>[!info]- Symmetry
>The contributions of two feature values $j$ and $k$ should be the same if they contribute equally to all possible coalitions.
>
>If
>$$
>val(S\cup\{j\}) = val(S\cup\{k\}) \ \ \ \text{for all} \ \ S \subseteq \{1,...,p\} 
∖ \{j, k\}
>$$
>then
>$$
>\phi_j = \phi_k
>$$

>[!info]- Dummy
>A feature $j$ that does not change the predicted value, regardless of which coalition of feature value it is added to, should have a Shapley value of 0. 
>
>If
>$$
>val(S\cup\{j\}) = val(S) \ \ \ \text{for all}\ \ \ S\subseteq\{1, ..., p\}
>$$
>
>then
>$$\phi_j = 0$$

>[!info]- Additivity
>For a game with combined pay-outs $val + val^+$ the respective Shapley values are as follows:
>$$
>\phi_j + \phi_j^+
>$$

#### Estimating the Shapley value

>[!info] Intuition
>An intuitive way to understand the Shapley value is the following illustration: 
>The feature values enter a room in random order. All feature values in the room participate in the game (= contribute to the prediction). The Shapley value of a feature value is the *average change in the prediction that the coalition already in the room receives when the feature value joins them*.

All possible coalitions (sets) of feature values have to be evaluated with and without the $j$-th feature to calculate the exact Shapley value. For more than a few features, the exact solution to this problem becomes problematic as the number of possible coalitions exponentially increases as more features are added. Strumbelj et al. (2014) propose an *approximation with Monte-Carlo sampling*:
$$
\hat{\phi}_j=\frac{1}{M}\sum^M_{m=1}\Big(\hat{f}(x^m_{+j}) - \hat{f}(x^m_{-j})\Big)
$$
- $\hat{f}(x^m_{+j})$ : prediction for $x$, but with a random number of feature values replaced by feature values from a random data point $z$, except for the respective value of feature $j$.
- The $x$-vector $x^m_{-j}$ is almost identical to $x^m_{+j}$, but the value $x^m_j$ is also taken from the sampled $z$.
- For features that appear left of the feature $x_j$, we take the values from the original observations, and for the features on the right, we take the values from a random instance.

##### Approximate Shapley estimation for a single feature value
- Output: Shapley value for the value of the $j$-th feature.
- Required: Number of iterations $M$, instance of interest $x$, feature index $j$, data matrix $X$, and machine learning model $f$.
	- For all $m=1,..., M$:
		1. Draw a random instance $z$ from the data matrix $X$.
		2. Choose a random permutation $o$ of the feature values.
		3. Order instance $x$ : $x_o = (x_{(1)}, ..., x_{(j)}, ..., x_{(p)})$
		4. Order instance $z$: $z_o = (z_{(1)}, ..., z_{(j)}, ..., z_{(p)})$
		5. Construct two new instances
			1. With $j$ : $x_{+j} = (x_{(1)}, ..., x_{(j-1)}, x_{(j)}, z_{(j+1)}, ..., z_{(p)})$
			2. Without $j$ : $x_{-j} = (x_{(1)}, ..., x_{(j-1)}, ..., z_{(j)}, z_{(j+1)}, ..., z_{(p)})$
		6. Compute marginal contribution: $\phi^m_j = \hat{f}(x_{+j}) - \hat{f}(x_{-j})$
	- Compute Shapley value as the average: $\phi_j(x) = \frac{1}{M} \sum^M_{m=1} \phi^m_j$

Averaging implicitly **weighs samples by the probability distribution** of $X$.

The procedure has to be repeated for each of the features to get all Shapley values.

---
### Advantages

1. The difference between the prediction and the average prediction is **fairly distributed** among the feature values of the instance. This property distinguishes the Shapley value from other methods such as [[Local Interpretable Model-agnostic Explanations (LIME)]]. LIME does not guarantee that the prediction is fairly distributed among the features. The Shapley value might be the only method to deliver a full explanation.
2. The Shapley value allows **contrastive explanations**. Instead of comparing a prediction to the average prediction of the entire dataset, you could compare it to a subset or even to a single data point.
3. The Shapley value is the only explanation method with a **solid theory**. The axioms – efficiency, symmetry, dummy, additivity – give the explanation a reasonable foundation.

### Disadvantages

1. The Shapley value requires **a lot of computing time**.
2. The Shapley value **can be misinterpreted**. The Shapley value of a feature value is not the difference of the predicted value after removing the feature from the model training.
3. The Shapley value is the wrong explanation method if you seek sparse explanations (explanations that contain few features). Explanations created with the Shapley value method **always use all the features**.
4. The Shapley value returns a simple value per feature, but **no prediction model** like LIME. This means it cannot be used to make statements about changes in prediction for changes in the input, such as: “If I were to earn €300 more a year, my credit score would increase by 5 points.”
5. **You need access to the data** if you want to calculate the Shapley value for a new data instance. It is not sufficient to access the prediction function because you need the data to replace parts of the instance of interest with values from randomly drawn instances of the data.
6. Like many other permutation-based interpretation methods, the Shapley value method suffers from **inclusion of unrealistic data instances** when features are correlated. To simulate that a feature value is missing from a coalition, we marginalize the feature.

---
## References

- [[Interpretable Machine Learning]]
- [9.5 Shapley Values | Interpretable Machine Learning (christophm.github.io)](https://christophm.github.io/interpretable-ml-book/shapley.html)