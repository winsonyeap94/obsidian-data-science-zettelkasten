---
tags:
  - graphs
  - neural-networks
  - recommender-systems
creation-date: 2024-07-07 15:49
---
## GNNs for Recommender Systems

## Recommender Systems: Task and Evaluation

Recommender systems can be naturally modelled as a [[Graph Representation#Bipartite Graphs|bipartite graph]].
- A graph with two node types $\rightarrow$ **users** and **items**
- Edges connect users and items; indicates *user-item interaction* (e.g., click, purchase, review, etc.)

The recommendation task is then defined as:
- Given past user-item interactions, predict new items each user will interact with in the future.
- Can be framed as a **link prediction** problem.
	- For $u \in U, v \in V$ , we need to find a real-valued score $f(u ,v)$.

The challenge with modern recommender systems is that we cannot evaluate $f(u,v)$ for every user-item pair, as it may end up being too computationally expensive.
- This is where **Top-$K$ Recommendation** comes in, where we only recommend $K$ items per user.
	- For recommendation to be effective, $K$ needs to be much smaller than the total number of items (up to billions).
	- $K$ is typically in the order of 10-100.
- The goal is to include as many positive items as possible in the top-$K$ recommended items.

### Evaluation Metric: Recall@$K$ 

![[Pasted image 20240707160811.png]]

$$
\text{Recall}@K = \frac{|P_u \cap R_u|}{|P_u|}
$$
- $P_u$ : Set of positive items the user will interact with in the future.
- $R_u$ : Set of items recommended by the model.
	- In top-$K$ recommendation, $|R_u| = K$.
	- Items that the user has already interacted with are excluded.
- A higher value indicates more positive items are recommended in top-$K$ for user $u$.
- The final Recall@$K$ is computed by averaging the recall values across all users.

---
## Recommender Systems: Embedding-based Models

Notation:
- $U$ : Set of all users
- $V$ : Set of all items
- $E$ : Set of observed user-item interactions
	- $E = \big\{ (u,v) \mid u \in U , v \in V , u \text{ interacted with } v \big\}$

### Embedding-Based Models

We consider embedding-based models for scoring user-item interactions.
- For each user $u \in U$, let $\mathbf{u} \in \mathbb{R}^D$ be its $D$-dimensional embedding.
- For each item $v \in V$, let $\mathbf{v} \in \mathbb{R}^D$ be its $D$-dimensional embedding.
- Let $f_\theta(\cdot, \cdot) : \mathbb{R}^D \times \mathbb{R}^D \rightarrow \mathbb{R}$ be a parameterised function.
- Then, we get the score function
$$
\text{score}(u ,v) = f_\theta(\mathbf{u}, \mathbf{v})
$$

Embedding-based models have three kinds of parameters:
1. An **encoder** to generate user embeddings $\{\mathbf{u}\}_{u \in U}$
2. An **encoder** to generate item embeddings $\{\mathbf{v}\}_{v \in V}$
3. **Score function** $f_\theta(\cdot, \cdot)$

The training objective is then to optimise the model parameters to achieve high [[GNNs for Recommender Systems#Evaluation Metric Recall@$K$|recall@K]] on seen (i.e., training) user-item interactions.
- We hope that this objective would lead to high recall@$K$ on unseen (i.e., test) interactions.

### Surrogate Loss Functions

>[!warning] However, the original training objective (recall@$K$) is **not differentiable**. 
>
>- This means that we cannot apply efficient gradient-based optimisation.
>- As a workaround, we rely on *surrogate loss functions*.

Surrogate losses are differentiable and should align well with the original training objective.

Two surrogate loss functions are widely used to enable efficient gradient-based optimisation:
1. Binary loss
2. Bayesian Personalised Ranking (BPR) loss

#### (1) Binary Loss

Binary loss is a *binary classification* of **positive/negative edges** using a sigmoid function ($\sigma$).
$$
\text{Binary Loss} = -\frac{1}{|E|}\sum_{(u, v)\in E} \log\big(\sigma\big(f_\theta(\mathbf{u}, \mathbf{v})\big)\big) - \frac{1}{|E_{\text{neg}}|} \sum_{(u,v)\in E_{\text{neg}}} \log\big(1- \sigma(f_\theta(\mathbf{u}, \mathbf{v})) \big)
$$
- $E$ : Set of **positive edges** (i.e., observed/training user-item interactions)
- $E_{\text{neg}}$ : Set of **negative edges**, i.e., $\big\{ (u,v) \mid (u,v) \notin E, u\in U, v \in V \big\}$
- During training the terms above can be approximated using mini-batch of positive/negative edges.

Binary loss pushes the scores of positive edges higher than those of negative edges.
- This aligns with the training recall metric since positive edges need to be recalled.

>[!warning]- However, there is an issue with Binary Loss, that is the *scores of **ALL** positive edges are pushed higher than those of **ALL** negative edges*.
>
>This would unnecessarily penalise model predictions even if the training recall metric is perfect.
>
>![[Pasted image 20240707165631.png]]
>
>The key insight here is that binary loss is **non-personalised** in a sense that the positive/negative edges are considered across ***ALL** users at once*.
>- However, the recall metric is inherently **personalised (defined for each user).**
>- This means that the non-personalised binary loss is overly-stringent for the personalised recall metric.
>- This is something we will try to solve with the BPR Loss (below).

#### (2) Bayesian Personalised Ranking (BPR) Loss

BPR Loss is a **personalised** surrogate loss that aligns better with the recall@$K$ metric.

For each user $u^* \in U$, define the **rooted positive/negative edges** as:
- Positive edges rooted at $u^*$ : $E(u^*) \equiv \big\{ (u^*,v) \mid (u^*,v) \in E \big\}$
- Negative edges rooted at $u^*$ : $E_{\text{neg}}(u^*) \equiv \big\{ (u^*,v)\mid(u^*,v)\in E_{\text{neg}} \big\}$

Training objective:
- *For each user* $u^*$, we want the scores of rooted positive edges $E(u^*)$ to be higher than those of rooted negative edges $E_{\text{neg}}(u^*)$.

![[Pasted image 20240707170357.png]]

Final BPR Loss:
$$
\frac{1}{|U|} \sum_{u^* \in U} \text{Loss}(u^*)
$$
![[Pasted image 20240707170441.png]]

### Why Embedding Models Work?

The underlying idea of embedding models are very similar to **collaborative filtering** (i.e., items are recommended by collecting preferences of many other similar users).

Embedding models can capture similarity of users/items.
- Low-dimensional embeddings *cannot simply memorise all user-item interaction data*.
- Instead, embeddings are forced to *capture similarity* between users/items to fit the data.
- This allows the models to make effective prediction on unseen user-item interactions.

### Algorithms

1. [[Neural Graph Collaborative Filtering (NGCF)]]
2. [[LightGCN]]
	- Improve the conventional collaborative filtering models (i.e., shallow encoders) by explicitly modelling graph structure using GNNs.
	- Assumes no user/item features.
3. [[PinSAGE]]
	- Use GNNs to generate high-quality embeddings by simultaneously capturing rich node attributes (e.g., images) and the graph structure.



---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 11 GNNs for Recommenders]]