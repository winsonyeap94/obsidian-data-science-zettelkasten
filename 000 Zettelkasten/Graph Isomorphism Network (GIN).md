---
tags:
  - graphs
  - neural-networks
creation-date: 2024-06-16 09:25
---
## Graph Isomorphism Network (GIN)

![[Pasted image 20240616093159.png]]

Graph Isomorphism Network (GIN) is a variant of GNN that generalises the **WL test** and hence achieves maximum discriminative power among GNNs.

To model injective multiset functions for neighbour aggregation, GIN uses a theory of "deep multisets", i.e., parameterising universal multiset functions with neural networks.

> [!info] Read also [[Traditional Feature-based Methods for Graphs#3.2 Weisfeiler-Lehman Kernel|Weisfeiler-Lehman]] for more details.
> 
> The Weisfeiler-Lehman (WL) test is an effective and computationally efficient test that distinguishes a broad class of graphs (to test whether two graphs are *topologically identical*).

### Injective Multi-Set Function

![[Pasted image 20240619212710.png]]

**Proof Intuition:**
- $f$ produces one-hot encodings of colours.
- Summation of the one-hot encodings retains all information about the input multi-set.

![[Pasted image 20240619212746.png]]

How to model $\Phi$ and $f$ in $\Phi\big(\sum_{x\in S}f(x)\big)$?
- We use a **multi-layer perceptron (MLP)**.
- Theorem: Universal Approximation Theorem
	- A 1-hidden-layer MLP with sufficiently large hidden dimensionality and appropriate non-linearity $\sigma(\cdot)$ (including ReLU and Sigmoid) can *approximate any continuous function to an arbitrary accuracy*.
- In practice, MLP hidden dimensionality of 100 to 500 is sufficient.

### GIN and WL Graph Kernel

GIN can be understood as differentiable neural version of the WL graph kernel:

|                 | Update Target                                | Update Function |
| --------------- | -------------------------------------------- | --------------- |
| WL Graph Kernel | Node Colours<br>(one-hot)                    | HASH            |
| GIN             | Node Embeddings<br>(low-dimensional vectors) | GINConv         |
Advantages of GIN over WL Graph Kernel:
1. Node embeddings are **low-dimensional**; hence, they can capture the fine-grained similarity of different nodes.
2. Parameters of the update function can be **learned for the downstream tasks**.



---
## Algorithm

Initialisation:
$$
h_v^{(0)} = x_v \ \ \ \ \ \text{for all } v \in V
$$
- $h_v^{(0)}$ : Node $v$'s initial embedding
- $x_v$ : Node $v$'s original features

And for $k=1,2,..., K$, where $K$ is the total steps,
$$
h_v^{(k)} = f^{(k)} \Bigg(\sum_{u \in \mathcal{N}(v)} h_u^{(k-1)} + (1 + \epsilon^{(k)}) \cdot h_v^{(k-1)}    \Bigg) \ \ \ \ \ \text{for all }v \in V
$$
- $h_v^{(k)}$ : Node $v$'s embedding at step $k$
- $\sum_{u \in \mathcal{N}(v)} h_u^{(k-1)}$ : Sum of $v$'s neighbour's embeddings at step $k-1$.
- $h_v^{(k-1)}$ : Node $v$'s embedding at step $k-1$.

Predictions can be made at each node using the final computed embedding:
$$
\hat{y_v} = \text{PREDICT}(h_v^{(K)})
$$
where $\text{PREDICT}$ is generally another neural network, learnt together with the GIN model.

For each step $k$, the function $f^{(k)}$ and real-valued parameter $\epsilon^{(k)}$ and shared across all nodes.
- This allows the GIN model to scale well, because the *number of parameters in the model is not tied to the size of the graph*.

---
## Algorithm Explanation 2

> [!info] This explanation is based on [[CS224W - Machine Learning with Graphs]]

GIN uses a neural network to model the injective HASH function (similar to the *colour refinement algorithm* in WL kernel).
$$
c^{(k+1)}(v) = \text{HASH}\Big(c^{(k)}(v), \big\{c^{(k)}(u) \big\}_{u\in N(v)} \Big)
$$
Specifically, we will model the injection function over the tuple in the HASH function.

**Theorem**
Any injective function over the tuple can be modelled as
$$
\text{MLP}_\Phi\bigg( (1+\epsilon)\cdot\text{MLP}_f \Big( c^{(k)}(v)\Big) + \sum_{u\in N(v)}\text{MLP}_f\Big(c^{(k)}(u)\Big) \bigg)
$$where $\epsilon$ is a learnable scalar.

If the input feature $c^{(0)}(v)$ is represented as one-hot, **direct summation is injective**.
- Hence, we only need $\Phi$ to ensure the injectivity.
![[Pasted image 20240619214143.png]]
![[Pasted image 20240619214200.png]]


**Algorithm Steps: GIN's Node Embedding Updates**
Given: A graph $G$ with a set of nodes $V$.
1. Assign an *initial vector $c^{(0)}(v)$* to each node $v$.
2. Iteratively update node vectors by
   $$
   c^{(k+1)}(v) = \text{GINConv}\Big(\Big\{ c^{(k)}(v), \big\{ c^{(k)}(u) \big\}_{u\in N(v)} \Big\}\Big)
   $$
   where $\text{GINConv}$ is a *differentiable colour HASH function*, and it maps different inputs to different embeddings.
3. After $K$ steps of GIN iterations, *$c^{(k)}(v)$ summarises the structure of $K$-hop neighbourhood*.



---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 6 Theory of GNNs]]
- Original paper: [arxiv.org/pdf/1810.00826](https://arxiv.org/pdf/1810.00826)