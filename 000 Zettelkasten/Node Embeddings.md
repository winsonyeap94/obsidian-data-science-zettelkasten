---
tags:
  - graphs
  - embeddings
creation-date: 2024-06-01 23:37
---
# Node Embeddings

![[Pasted image 20240601233855.png]]

**Task: Map nodes into an embedding space.**
- Similarity of embeddings between nodes indicates their similarity in the network.
	- e.g., Both nodes are close to each other (connected by an edge)
- Encode network information

> [!example]- Example Node Embedding
> ![[Pasted image 20240601234054.png]]

## How Is It Done? - Encoder & Decoder Setup

Assume we have a graph $G$:
- $V$ is the vertex set
- $A$ is the adjacency matrix (assume binary)
- For simplicity, no node features or extra information is used.

![[Pasted image 20240601234308.png]]

The goal is to encode nodes so that **similarity in the embedding space (e.g., dot product)** approximates **similarity in the graph**.
![[Pasted image 20240601234410.png]]

### Steps - Learning Node Embeddings

1. **Encoder** maps from nodes to embeddings.
2. *Define a node similarity function* (i.e., a measure of similarity in the original network).
3. **Decoder** $\text{DEC}$ maps from embeddings to the similarity score.
4. *Optimise the parameters of the encoder* so that the similarity of the embedding is close to the similarity in the original network
$$
\text{similarity}(u,v) \approx \mathbf{z}_v^T \mathbf{z}_u
$$
### "Shallow" Encoding

"Shallow" encoding is the simplest encoding approach: **Encoder is just an embedding-lookup**
$$
\text{ENC}(v)=\mathbf{z}_v=\mathbf{Z}\cdot v
$$
where
- $\mathbf{Z} \in \mathbb{R}^{d \times |V|}$ is a matrix, where each column is a node embedding (what we learn/optimise).
- $v \in \mathbb{I}^{|V|}$ is the *indicator vector*, all all zeroes except a one in column indicating node $v$.
![[Pasted image 20240602000147.png]]

With "shallow" encoding, <mark style="background: #FFF3A3A6;">each node is assigned a unique embedding vector</mark> (i.e., we directly optimise the embedding of each node).
- This is also its limitation, as the size (number of columns) is equal to the #(nodes) of the graph.
- Methods: *DeepWalk*, *node2vec*

---
## Random-Walk Approaches for Node Embeddings
![[Pasted image 20240602000928.png]]
### Notation

- **Vector** $\mathbf{z}_u$:
	- The embedding of node $u$.
- **Probability** $P(v \mid \mathbf{z}_u)$:
	- The (predicted) probability of visiting node $v$ on random walks starting from node $u$.
	- Our model prediction is based on $\mathbf{z}_u$.

Non-linear functions used to produce predicted probabilities:
- **Softmax function**
	- Turns vector of $K$ real values (model predictions) into $K$ probabilities that sum to 1.
- **Sigmoid function**
	- S-shaped function that turns real values into the range of (0, 1).

### Random-Walk Embeddings
![[Pasted image 20240602001011.png]]
![[Pasted image 20240602001048.png]]

### Why Random Walks?

1. **Expressivity**
	- Flexible stochastic definition of node similarity that *incorporates both local and higher-order neighbourhood information*.
	- Idea: If random walk starting from node $u$ visits $v$ with high probability, then $u$ and $v$ are similar (high-order multi-hop information).
2. **Efficiency**
	- Do not need to consider all node pairs when training; *only need to consider pairs that co-occur on random walks*.

### Random Walk Optimisation

Given node $u$, we want to learn feature representations that are predictive of the nodes in its random walk neighbourhood $N_R(u)$.
$$
f(u) = \mathbf{z}_u
$$

Log-likelihood objective:
$$
\max_f \sum_{u \in V} \log P(N_R{(u)} \mid \mathbf{z}_u)
$$
- $N_R{(u)}$ is the neighbourhood of node $u$ by strategy $R$.

Steps:
1. Run **short fixed-length random walks** starting from each node $u$ in the graph using some random walk strategy $R$.
2. For each node $u$ collect $N_R(u)$, the multiset of nodes visited on random walks starting from $u$.
3. Optimise embeddings according to formula below.
$$
\underset{z}{\arg \max} \sum_{u \in V} \log P(N_R(u) \mid \mathbf{z}_u)
$$
	Equivalently,
$$
\underset{z}{\arg \min}\  \mathcal{L} = \sum_{u \in V} \sum_{v \in N_R(u)} -\log P(v\mid \mathbf{z}_u)
$$
	We also parameterise $P(v\mid \mathbf{z}_u)$ using softmax:
$$
P(v\mid \mathbf{z}_u) = \frac{\exp(\mathbf{z}_u^T \mathbf{z}_v)}{\sum_{n \in V}\exp(\mathbf{z}_u^T \mathbf{z}_n)}
$$
![[Pasted image 20240602001852.png]]


However, implementing the formula above naively is too expensive due to the nested sum over nodes (this results in $O(|V|^2)$ complexity. This is something we will solve using negative sampling. 

#### Negative Sampling
$$
-\log(\frac{\exp(\mathbf{z}_u^T \mathbf{z}_v)}{\sum_{n\in V}\exp(\mathbf{z}_u^T \mathbf{z}_n)}) 
\approx 
\log(\sigma(\mathbf{z}_u^T \mathbf{z}_v)) + \sum^k_{i=1} \log(\sigma(-\mathbf{z}_u^T \mathbf{z}_{n_i})) \ \ , \ \ n_i \sim P_V
$$
Negative sampling allows for quick likelihood calculation.
- Instead of normalising w.r.t. all nodes, just normalise against $k$ random "**negative samples**" $n_i$.
- We sample $k$ negative nodes $n_i$ each with *probability proportion to its degree*.
- Term definitions:
	-  $\sigma$ : **sigmoid function**.
	- $n_i \sim P_V$ : random ditribution over nodes
- Two considerations for $k$ (# negative samples):
	1. Higher $k$ gives more robust estimates.
	2. Higher $k$ corresponds to higher bias on negative events.
- In practice, $k = 5 \text{ to } 20$.

We then solve the likelihood calculation using stochastic gradient descent.

## node2vec

Develop **biased 2nd order random walk** $R$ to generate network neighbourhood $N_R(u)$ of node $u$.
- Idea: Use flexible, biased random walks that can *trade off between local and global views* of the network.

![[Pasted image 20240602003439.png]]

Two parameters:
1. **Return parameter $p$:
	- Return back to the previous node
2. **In-out parameter $q$:
	- Moving outwards (DFS) vs inwards (BFS) from the previous node
	- Intuitively, $q$ is the "ratio" of BFS vs DFS.

Steps:
1. Compute edge transition probabilities.
	- For each edge $(s_1, w)$ we compute edge walk probabilities (based on $p$, $q$) of edges $(w,\cdot)$.
2. Simulate $r$ random walks of length $l$ starting from each node $u$.
3. Optimise the node2vec objective using stochastic gradient descent.

The node2vec algorithm is **linear-time** complexity, and all 3 steps are individually parallelisable.

### Example of one step of the biased random walk

![[Pasted image 20240602003719.png]]
![[Pasted image 20240602003810.png]]
![[Pasted image 20240602003827.png]]


---
## Embedding Entire Graphs

![[Pasted image 20240602133756.png]]

**Goal: Embed a subgraph or an entire graph $G$ to create a graph embedding $\mathbf{z}_G$.**

Tasks:
1. Classifying toxic vs non-toxic molecules
2. Identifying anomalous graphs

### Approach #1 (Simple idea): Embed nodes and then sum/average them

- Run a standard graph embedding technique on the (sub)graph $G$.
- Then, just **sum (or average) the *node embeddings*** in the (sub)graph $G$.
$$
\mathbf{z}_G = \sum_{v\in G} \mathbf{z}_v
$$
- Used to classify molecules based on their graph structure.

### Approach #2: Create super-node that spans the entire (sub)graph and then embed that node

- Introduce a **"virtual node"** to represent the (sub)graph and run a standard graph embedding technique.
![[Pasted image 20240602133927.png]]

### Approach #3: (Anonymous) Random Walk Embeddings

In the anonymous walk approach, we ignore the actual "node" itself (i.e., A, B, C) and instead focus on the path (sequence of nodes) itself (i.e., 1, 2, 3).
- This means that it is *agnostic to the identity of the nodes visited* (hence the name "anonymous").

> [!warning]- The number of anonymous walks grows exponentially.
> ![[Pasted image 20240602141319.png]]

![[Pasted image 20240602134319.png]]

![[Pasted image 20240602141205.png]]

#### Simple use of anonymous walks

- Simulate anonymous walks $w_i$ of $l$ steps and record their counts.
- Represent the graph as a **probability distribution over these walks**.
- For example:
	- Set $l=3$.
	- Then, we can represent the graph as a 5-dimensional vector.
		- Since there are 5 anonymous walks $w_i$ of length 3: $111$, $112$, $121$, $122$, $123$.
	- $\mathbf{Z}_G[i]$ = Probability of anonymous walk $w_i$ in $G$.
#### Sampling Anonymous Walks

- To sample anonymous walks, we generate independently a set of $m$ random walks, and then represent the graph as a probability distribution over these walks.
- How many random walks $m$ do we need?
	- We want the distribution to have error of more than $\epsilon$ with probability less than $\delta$:
$$
m = \bigg[\frac{2}{\epsilon^2}\big( \log(2^\eta - 2) - \log(\delta)  \big)\bigg]
$$
where $\eta$ is the total number of anonymous walks of length $l$.

#### New Idea: Learn Walk Embeddings

Rather than simply represent each walk by the fraction of times it occurs, we **learn embedding $\mathbf{z}_i$ of anonymous walk $w_i$**.
- **Learn a graph embedding $\mathbf{Z}_G$ together with all the anonymous walk embeddings $\mathbf{z}_i$.**
- $Z = \{z_i: i = 1 ... \eta\}$ , where $\eta$ is the number of sampled anonymous walks.

How to embed walks?
- Idea: Embed walks s.t. the next walk can be predicted.
![[Pasted image 20240602142430.png]]
![[Pasted image 20240602142625.png]]
![[Pasted image 20240602142818.png]]

### Approach #4: Hierarchical Embeddings

![[Pasted image 20240602142919.png]]





---
# References

- [[CS224W - Machine Learning with Graphs#Chapter 2 Node Embeddings]]