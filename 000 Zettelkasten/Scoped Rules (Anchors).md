---
tags:
  - explainable-ai
creation-date: 2024-08-18 13:32
---
## Scoped Rules (Anchors)

![[Pasted image 20240818140213.png]]

The anchors method explains individual predictions of any black box classification model by **finding a decision rule that “anchors” the prediction** sufficiently. 
- A rule anchors a prediction if *changes in other feature values do not affect the prediction*.
- Anchors utilises *reinforcement learning techniques* in combination with a *graph search algorithm* to reduce the number of model calls (and hence the required runtime) to a minimum while still being able to recover from local optima.

Scoped Rules were introduced by the same authors as the [[Local Interpretable Model-agnostic Explanations (LIME)]] paper.
- Like its predecessor, the anchors approach deploys a _perturbation-based_ strategy to generate _local_ explanations for predictions of black box machine learning models. However, instead of surrogate models used by LIME, the resulting explanations are expressed as easy-to-understand _IF-THEN_ rules, called _anchors_.

The following figure depicts both LIME and anchors locally explaining a complex binary classifier (predicts either **-** or **+**) using two exemplary instances. 
- LIME’s results do not indicate how faithful they are, as LIME solely learns a linear decision boundary that best approximates the model given a perturbation space $D$. 
- Given the same perturbation space, the anchors approach constructs explanations whose coverage is adapted to the model’s behaviour and the approach clearly expresses their boundaries. Thus, they are *faithful by design* and state exactly for which instances they are valid. This property makes anchors particularly intuitive and easy to comprehend.

![[Pasted image 20240818133359.png]]

---
### Example - Titanic

The following simple example illustrates such an anchor. For instance, suppose we are given a bivariate black box model that predicts whether or not a passenger survived the Titanic disaster. Now we would like to know _why_ the model predicts for one specific individual that it survives. The anchors algorithm provides a result explanation like the one shown below.

![[Pasted image 20240818133908.png]]

And the corresponding anchors explanation is:
- IF `SEX = female` AND `Class = first` THEN PREDICT `Survived = true` WITH **PRECISION** 97% AND **COVERAGE** 15%

The example shows how anchors can provide essential insights into a model’s prediction and its underlying reasoning. The result shows which attributes were taken into account by the model, which in this case, is the female sex and first class. 
- The anchor additionally tells us that it <mark style="background: #FFF3A3A6;">applies to 15% of perturbation space’s instances. In those cases the explanation is 97% accurate</mark>, meaning the displayed predicates are almost exclusively responsible for the predicted outcome.

---
### Algorithm

An anchor $A$ is formally defined as follows:
$$
\mathbb{E}_{\mathcal{D}_x(z\mid A)}\big[1_{\hat{f}(x)=\hat{f}(z)}\big] \ge \tau, \ \ A(x)=1
$$
- $x$ : the instance being explained
- $A$ : set of predicates (i.e., the resulting rule or anchor, such that $A(x) = 1$ when all feature predicates defined by $A$ correspond to $x$'s feature values)
- $f$ : the *classification model* to be explained
- $D_x(\cdot\mid A)$ : the distribution of neighbours of $x$, matching $A$
- $0 \le \tau \le 1$ : precision threshold. Only rules that achieve a local fidelity of at least $\tau$ are considered a valid result.
#### Finding Anchors

Although anchors’ mathematical description may seem clear and straightforward, constructing particular rules is infeasible.
-  Therefore, the authors propose to introduce the parameter $0 \le \delta \le 1$ to create a *probabilistic definition*. This way, samples are drawn until there is statistical confidence concerning their precision.
- The probabilistic definition reads as follows:
$$
P(prec(A) \ge \tau) \ge 1 - \delta \ \ \text{with}\ \ prec(A) = \mathbb{E}_{\mathcal{D}_x(z\mid A)} \big[1_{\hat{f}(x) = \hat{f}(z)}\big]
$$
The previous two definitions are combined and extended by the notion of **coverage**. Its rationale consists of finding rules that apply to a preferably large part of the model's input space.
- Coverage is formally defined as *an anchor's probability of applying to its neighbours, i.e., its perturbation space*:
$$
cov(A) = \mathbb{E}_{\mathcal{D}_{(z)}}[A(z)]
$$
Including this element leads to anchor’s final definition taking into account the maximization of coverage:
$$
\underset{A\ \ \text{s.t.}\ \ P(prec(A)\ge\tau)\ge 1-\delta}{\max}cov(A)
$$
Thus, the proceeding strives for a rule that has the highest coverage among all eligible rules (all those that satisfy the precision threshold given the probabilistic definition).

>[!note] Trade-off between Precision and Coverage
>Note that rules with *more predicates tend to have higher precision* than rules with fewer predicates. In particular, a rule that fixes every feature of $x$ reduces the evaluated neighbourhood to identical instances. Thus, the model classifies all neighbours equally, and the rule’s precision is $1$. At the same time, a rule that fixes many features is overly specific and only applicable to a few instances. Hence, there is a _trade-off between precision and coverage_.

The anchors approach uses 4 main components to find explanations:
1. **Candidate Generation** - Generates new explanation candidates.
	- In the first round, one candidate per feature of $x$ gets created and fixes the respective value of possible perturbations.
	- In every other round, the best candidates of the previous round are extended by one feature predicate that is not yet contained therein.
2. **Best Candidate Identification** - Compare candidate rules in regard to which rule explains $x$ the best.
	- To this end, perturbations that match the currently observed rule are created and evaluated by calling the model.
	- These calls need to be minimised as to limit computational overhead. This is why, at the core of this component, there is a pure-exploration **[[Multi-Armed-Bandit]]** (_MAB_; _KL-LUCB_[62](https://christophm.github.io/interpretable-ml-book/anchors.html#fn62), to be precise).
3. **Candidate Precision Validation** - Takes more samples in case there is no statistical confidence yet that the candidate exceeds the $\tau$ threshold.
4. **Modified Beam Search** - All of the above components are assembled in a beam search, which is a graph search algorithm and a variant of the breadth-first algorithm.
	- It carries the $B$ best candidates of each round over to the next one ($B$ is the *Beam Width*). These $B$ best rules are then used to create new rules.
	- The beam search conducts at most $featureCount(x)$ rounds, as each feature can only be included in a rule at most once.
	- Thus, at every round $i$, it generates candidates with exactly $i$ predicates and selects the $B$ best thereof.
	- Therefore, by setting $B$ high, the algorithm is more likely to avoid local optima. In turn, this requires a high number of model calls and thereby increases the computational load.

![[Pasted image 20240818135523.png]]






---
### Advantages

1. The algorithm’s output is easier to understand, as rules are **easy to interpret** (even for laypersons).
2. **Anchors are subset-able** and even state a measure of importance by including the notion of coverage.
3. The anchors approach **works when model predictions are non-linear or complex** in an instance’s neighbourhood. As the approach deploys reinforcement learning techniques instead of fitting surrogate models, it is less likely to underfit the model.
4. **Highly efficient as it can be parallelized** by making use of MABs that support batch sampling (e.g. BatchSAR).

### Disadvantages

1. The algorithm suffers from a **highly configurable** and impactful setup, just like most perturbation-based explainers. Not only do hyperparameters such as the *beam width* or *precision threshold* need to be tuned to yield meaningful results, but also does the *perturbation function* need to be explicitly designed for one domain/use case.
2. **Many scenarios require discretisation** as otherwise results are too specific, have low coverage, and do not contribute to understanding the model. While discretisation can help, it may also *blur decision boundaries* if used carelessly and thus have the exact opposite effect.

---
## References

- [[Interpretable Machine Learning]]
- [9.4 Scoped Rules (Anchors) | Interpretable Machine Learning (christophm.github.io)](https://christophm.github.io/interpretable-ml-book/anchors.html)