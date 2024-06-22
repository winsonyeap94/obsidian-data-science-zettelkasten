---
tags:
  - graphs
  - "#neural-networks"
creation-date: 2024-06-06 11:55
---
## Training Graph Neural Networks (GNN)

![[Pasted image 20240615183426.png]]


## Prediction Heads

Different tasks require different types of prediction heads:
1. Node-level tasks
2. Edge-level tasks
3. Graph-level tasks

### (1) Node-level

For node-level prediction, we can directly make prediction using **node embeddings**.
- After GNN computation, we have *$d$-dimensional node embeddings*: $\{\mathbf{h}_v^{(L)} \in \mathbb{R}^d , \forall v \in G \}$
- Suppose we want to make *$k$-way prediction*.
	- Classification: Classify among $k$ categories
	- Regression: Regress on $k$ targets

$$
\hat{y_v} = \text{Head}_{\text{node}}(\mathbf{h}_v^{(L)}) = \mathbf{W}^{(H)} \mathbf{h}_v^{(L)}
$$
- $\mathbf{W}^{(H)} \in \mathbb{R}^{k \times d}$ : We *map node embeddings* from $\mathbf{h}_v^{(L)} \in \mathbb{R}^d$ to $\hat{y_v} \in \mathbb{R}^k$ so that we can compute the loss.

### (2) Edge-level

For edge-level prediction, we make predictions using **pairs of node embeddings**.

Suppose we want to make $k$-way prediction:
$$
\hat{y_{uv}} = \text{Head}_{\text{edge}}(\mathbf{h}_u^{(L)}, \mathbf{h}_v^{(L)})
$$
![[Pasted image 20240615184258.png]]

Options for $\text{Head}_{\text{edge}}(\mathbf{h}_u^{(L)}, \mathbf{h}_v^{(L)})$:
1. **Concatenation + Linear**
	- $\hat{y_{uv}} = \text{Linear}(\text{Concat}( \mathbf{h}_u^{(L)}, \mathbf{h}_v^{(L)} ))$
	- Here, $\text{Linear}(\cdot)$ will map *$2d$-dimensional embeddings* (since we concatenated embeddings) to *$k$-dimensional embeddings* ($k$-way prediction).
![[Pasted image 20240615184322.png]]
2. **Dot Product**
	- $\hat{y_{uv}} = (\mathbf{h}_u^{(L)})^T \mathbf{h}_v^{(L)}$
	- This approach only applies to *1-way prediction* (e.g., link prediction - predict the existence of an edge)
	- Applying to *$k$-way prediction*:
		- Similar to multi-head attention: $\mathbf{W}^{(1)}, ..., \mathbf{W}^{(k)}$ trainable
			- $\hat{y_{uv}}^{(1)} = (\mathbf{h}_u^{(L)})^T \ \mathbf{W}^{(1)} \ \mathbf{h}_v^{(L)}$
			- $...$
			- $\hat{y_{uv}}^{(k)} = (\mathbf{h}_u^{(L)})^T \ \mathbf{W}^{(k)} \ \mathbf{h}_v^{(L)}$
			- $\hat{y_{uv}} = \text{Concat}(\hat{y_{uv}}^{(1)}, ..., \hat{y_{uv}}^{(k)}) \in \mathbb{R}^k$

### (3) Graph-level

For graph-level prediction, we have to make prediction using **all the node embeddings in the graph**.

Suppose we want to make $k$-way prediction:
$$
\hat{y_G} = \text{Head}_{\text{graph}}(\{ \mathbf{h}_v^{(L)} \in \mathbb{R}^d, \forall v \in G \})
$$
![[Pasted image 20240615185036.png]]


Options for $\text{Head}_{\text{graph}}(\{ \mathbf{h}_v^{(L)} \in \mathbb{R}^d, \forall v \in G \})$:
1. **Global Mean Pooling**
	- $\hat{y_G} = \text{Mean}(\{ \mathbf{h}_v^{(L)} \in \mathbb{R}^d, \forall v \in G \})$
2. **Global Max Pooling**
	- $\hat{y_G} = \text{Max}(\{ \mathbf{h}_v^{(L)} \in \mathbb{R}^d, \forall v \in G \})$
3. **Global Sum Pooling**
	- $\hat{y_G} = \text{Sum}(\{ \mathbf{h}_v^{(L)} \in \mathbb{R}^d, \forall v \in G \})$


---
## Hierarchical Pooling

Hierarchical pooling is introduced as a way of overcoming the issue of global pooling.

### Issue of Global Pooling

The issue with global pooling is that global pooling over a (large) graph will lose information.

> [!example]- Toy Example 
> Using 1-dimensional node embedding as an example,
> - Node embeddings for $G_1$ : $\{ -1, -2, 0, 1, 2\}$
> - Node embeddings for $G_2$: $\{ -10, -20, 0, 10, 20 \}$
> - Clearly $G_1$ and $G_2$ have very different node embeddings, hence their structure should be different.
>   
>If we do global sum pooling:
>- Prediction for $G_1$ = 0
>- Prediction for $G_2$ = 0
>
>**We cannot differentiate $G_1$ and $G_2$!**

### Methodology

The approach of hierarchical pooling is to aggregate the node embeddings **hierarchically**.
- We first separately aggregate the nodes as a sub-group, and then aggregate again to make the final prediction.

![[Pasted image 20240615213429.png]]


---
## Supervised vs Unsupervised Learning on Graphs

|                      | Supervised Learning                                              | Unsupervised Learning<br>(sometimes called *"self-supervised"*)                      |
| -------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| Description          | Labels come from **external sources**.                           | Signals come from **graphs** themselves.                                             |
| Generic Examples     | Predict drug likeness of a molecular graph.                      | Link prediction (predict if two nodes are connected).                                |
| Node-level Examples  | In a citation network, which subject area does a node belong to. | Node statistics (e.g., clustering coefficient, PageRank)                             |
| Edge-level examples  | In a transaction network, whether an edge (link) is fraudulent.  | Link prediction (hide the edge between two nodes, predict if there should be a link) |
| Graph-level examples | Among molecular graphs, the drug likeness of graphs.             | Graph statistics (e.g., predict of two graphs are isomorphic)                        |

---
## Train/Validation/Test Split for Graph Networks

Splitting the data for graphs is special, because the data points in a graph are **dependent** to each other.

![[Pasted image 20240615214031.png]]

### Solution 1: Transductive Setting

Transductive setting: Training/validation/test sets are *on the same graph*.
- The input graph *can be observed in all the dataset splits* (train/validation/test).
- The entire graph can be observed in all dataset splits, we only split the labels.
- Only applicable to *node/edge* prediction tasks.

In this setting, we will **only split the (node) labels**.
- At training time, we compute embeddings using the entire graph, and train using node 1 and 2's labels.
- At validation time, we compute embeddings using the entire graph, and evaluate on node 3 and 4's labels.
![[Pasted image 20240615214211.png]]

### Solution 2: Inductive Setting

Inductive setting: Training/validation/test sets are *on different graphs*.
- The dataset contains of multiple graphs.
- Each split can only observe the graph(s) within the split. A successful model should be able to generalise to unseen graphs.
- Applicable to *node/edge/graph* tasks.

In this setting, we **break the edges** between splits **to get multiple graphs**.
- Now we have 3 graphs that are independent. Node 5 will not affect our prediction on node 1 anymore.
- At training time, we compute embeddings using the graph over node 1 and 2, and train using node 1 and 2's labels.
- At validation time, we compute embeddings using the graph over node 3 and 4, and evaluate on node 3 and 4's labels.
![[Pasted image 20240615214518.png]]

Downside:
- We are throwing away some potentially useful edge information.
- This is not recommended for small graphs.

> [!example]- Example: Node Classification
> ![[Pasted image 20240615214551.png]]

> [!example]- Example: Graph Classification
> ![[Pasted image 20240615214608.png]]

> ![example]- Example: Link Prediction
> ![[Pasted image 20240615214954.png]]
> ![[Pasted image 20240615215007.png]]
> ![[Pasted image 20240615215036.png]]
> ![[Pasted image 20240615215048.png]]
> ![[Pasted image 20240615215057.png]]
> ![[Pasted image 20240615215116.png]]
> ![[Pasted image 20240615215130.png]]
> ![[Pasted image 20240615215145.png]]
> ![[Pasted image 20240615215153.png]]
> 



---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 5 GNN Augmentation and Training]]
