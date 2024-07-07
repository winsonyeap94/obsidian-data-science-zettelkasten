---
tags:
  - graphs
creation-date: 2024-06-27 22:08
---
## Fast Neural Subgraph Matching and Counting

The topic is split into three components:
1. Subgraphs and Motifs
	1. Defining Subgraphs and Motifs
	2. Determining Motif Significance
2. Neural Subgraph Representations
3. Mining Frequent Motifs

---
## (1) Subgraphs and Motifs

### Subgraphs

Subgraphs are the **building blocks of networks**. The have the power to *characterise* and *discriminate* networks.

![[Pasted image 20240627221108.png]]

There are two ways to formalise subgraphs, given a graph $G=(V, E)$.
1. **Definition 1: Node-induced subgraph**
	- Take subset of the *nodes* and all edges induced by the nodes:
		- $G' = (V', E')$ is a node induced subgraph iff
			- $V' \subseteq V$
			- $E' = \{ (u, v) \in E \mid u, v \in V' \}$
			- $G'$ is the subgraph of $G$ *induced* by $V'$.
	- Alternate terminology: **"induced subgraph"**
2. **Definition 2: Edge-induced subgraph**
	- Take subset of the *edges* and all corresponding nodes:
		- $G' = (V', E')$ is an edge induced subgraph iff
			- $E' \subseteq E$
			- $V' = \{u \in V \mid (v, u) \in E' \text{ for some } u \}$
	- Alternate terminology: **"non-induced subgraph"** or just **"subgraph"**

>[!info]- The choice of definition depends on the domain. For example:
>- Chemistry: Node-induced (functional graphs)
>- Knowledge graphs: Often edge-induced (focus is on edges representing logical relations)

The preceding definitions define subgraphs when $V' \subseteq V$ and $E' \subseteq E$, i.e., nodes and edges are taken from the same original graph $G$.
- *What if $V'$ and $E'$ come from a totally different graph?*
- We would say that $G_1$ **is "contained in"** $G_2$.
![[Pasted image 20240627222034.png]]
### Graph Isomorphism

![[Pasted image 20240627222227.png]]

The graph isomorphism problem is to **check whether two graphs are identical**.
- Two graphs $G_1$ and $G_2$ are considered isomorphic if there exists a **bijection** $f: V_1 \rightarrow V_2$ such that $(u, v) \in E_1$ iff $\big(f(u), f(v)\big) \in E_2$.
	- In this case, $f$ is called the **isomorphism**.

#### Subgraph Isomorphism

![[Pasted image 20240627222404.png]]

$G_2$ is considered **subgraph-isomorphic** to $G_1$ if *some* subgraph of $G_2$ is isomorphic to $G_1$.
- We also commonly say $G_1$ is a subgraph of $G_2$.
- We can use either the node-induced or edge-induced definition of subgraph.
- This problem is NP-hard.

>[!example]- Examples
>![[Pasted image 20240627222431.png]]
>![[Pasted image 20240627222439.png]]

### Network Motifs

Network motifs refer to <mark style="background: #FFF3A3A6;">recurring, significant patterns of interconnections</mark>.

They are defined based on:
1. **Pattern:** Small (node-induced) subgraph.
2. **Recurring:** Found many times, i.e., with high frequency.
3. **Significant:** More frequent than expected, i.e., in randomly generated graphs.

![[Pasted image 20240627222637.png]]


Importance of Network Motifs:
1. They help us understand how graphs work.
2. They help us make predictions based on presence (or lack of) in a graph dataset.

Examples:
1. **Feed-forward loops:** Found in network of neurons, where they neutralise "biological noise".
2. **Parallel loops:** Found in food webs.
3. **Single-input modules:** Found in gene control networks.

#### (a) Subgraph Frequency

**Definition of Graph-level Subgraph Frequency:**
Let $G_Q$ be a small graph and $G_T$ be a target graph dataset,
- Frequency of $G_Q$ in $G_T$: <mark style="background: #FFF3A3A6;">Number of unique subsets of nodes</mark> $V_T$ of $G_T$ for which the subgraph of $G_T$ <mark style="background: #FFF3A3A6;">induced</mark> by the nodes $V_T$ is isomorphic to $G_Q$.

![[Pasted image 20240627223039.png]]

**Definition of Node-level Subgraph Frequency:**
Let $G_Q$ be a small graph, $v$ be a node in $G_Q$ (the "anchor"), and $G_T$ be a target graph dataset,
- The number of nodes $u$ in $G_T$ for which some subgraph of $G_T$ is isomorphic to $G_Q$ and the <mark style="background: #FFF3A3A6;">isomorphism maps node</mark> $u$ to $v$.
- $(G_Q, v)$ is called a **node-anchored subgraph**.
This approach is robust to outliers.

![[Pasted image 20240627223218.png]]
#### (b) Motif Significance

![[Pasted image 20240627223503.png]]

The idea of defining motif significance is that subgraphs that occur in a real network **much more often than in a random network** have functional significance.

##### Defining Random Graphs

![[Pasted image 20240627223734.png]]

**Erdos-Renyi (ER) Random Graphs:**
- $G_{n, p}$ : Undirected graph on $n$ nodes where each edge $(u, v)$ appears i.i.d. with probability $p$.
- How to generate the graph:
	- Create $n$ nodes, for each pair of nodes $(u ,v)$ flip a biased coin with bias $p$.
	- As a result, the generated graph is a result of a random process.

![[Pasted image 20240627223801.png]]

**Configuration Model:**
- Goal: Generate a random graph with a given **degree sequence $k_1, k_2, ..., k_N$**.
- Useful as a "null" model of networks:
	- We can compare the real network $G^{real}$ and a "random" $G^{rand}$ which has the *same degree sequence* as $G^{real}$.
- An alternative to using spokes is by using **switching**.
	- Starting from a given graph $G$, repeat the switching step for $Q \cdot |E|$ times.
		- Select a pair of edges $A \rightarrow B$, $C \rightarrow D$ at random.
		- *Exchange* the endpoints to give $A \rightarrow D$, $C \rightarrow B$.
			- Edges are only exchanged if <mark style="background: #FFF3A3A6;">no multiple edges or self-edges are generated</mark>.
	- Result: A randomly rewired graph.
		- Same node degrees, randomly rewired edges.
	- $Q$ is chosen large enough (e.g., $Q=100$) for the process to converge.

##### Z-Score for Statistical Significance

We then need to use a statistical measure to evaluate how significant is each motif. One such approach is using Z-Scores.
- $Z_i$ captures the **statistical significance of motif $i$**.
$$
Z_i = \frac{(N_i^{\text{real}} - \bar{N}_i^{\text{rand}})}{\text{std}(N_i^{\text{rand}})}
$$
- $N_i^{\text{real}}$ : # of motif $i$ in graph $G^{\text{real}}$.
- $\bar{N}_i^{\text{rand}}$ : Average # of motif $i$ in random graph instances.

**Network Significance Profile (SP):**
$$
SP_i = \frac{Z_i}{\sqrt{\sum_j Z_j^2}}
$$
- $SP$ is a vector of *normalised Z-scores*.
- The dimension depends on the number of motifs considered.
- $SP$ emphasises <mark style="background: #FFF3A3A6;">relative significance of subgraphs</mark>.
	- This is important for comparison of networks of different sizes.
	- Generally, larger graphs display higher Z-scores.
- The sign of $SP$ indicates:
	- Negative : **under-representation**
	- Positive : **over-representation**

![[Pasted image 20240627224555.png]]

---
## (2) Neural Subgraph Representations

### Subgraph Matching
![[Pasted image 20240707115707.png]]

The task of subgraph matching is to decide whether a **query graph** is a *subgraph* in the **target graph**.
- The target graph can be disconnected.
- The query graph must be connected.

### Isomorphism as an ML Task

We can use GNN to **predict subgraph isomorphism.**
- The intuition is to exploit the *geometric shape of embedding space* to capture the properties of subgraph isomorphism.
- Task Setup: Binary classification

![[Pasted image 20240707115933.png]]

>[!info]- Neural Architecture for Subgraphs
>![[Pasted image 20240707120053.png]]
>![[Pasted image 20240707120105.png]]
>![[Pasted image 20240707120115.png]]

#### Why Anchor?

Recall node-level frequency definition:
- The number of nodes $u$ in $G_T$ for which some subgraph of $G_T$ is isomorphic to $G_Q$ and the isomorphism maps $u$ to $v$.

We can use **GNN to compute the embeddings** for $u$ and $v$.
- The embeddings are then used to decide if neighbourhood of $u$ is isomorphic to subgraph of neighbourhood of $v$.
- With this, we not only predict if there exists a mapping, but also *identify corresponding nodes* ($u$ and $v$).

#### Decomposing $G_T$ into Neighbourhoods

For each node in $G_T$:
1. Obtain a **$k$-hop neighbourhood** around the anchor.
2. Can be performed using **breadth-first search (BFS)**.
3. The depth $k$ is a hyper-parameter (e.g., 3).
	- Larger depth results in a more expensive model.

The same procedure applies to $G_Q$ to obtain the neighbourhoods.

Then, we embed the neighbourhoods using GNN by computing the *embeddings for the anchor nodes* in their respective neighbourhoods.

### Order Embedding Space

The idea is to map graph $A$ to a point $z_A$ into a high-dimensional (e.g., 64-dim) embedding space, such that $z_A$ is *non-negative in all dimensions*.
- The embeddings should also capture a sense of **order**, hence the name (read below).

![[Pasted image 20240707120649.png]]
![[Pasted image 20240707120736.png]]

#### Benefits of Order Embedding Space

![[Pasted image 20240707121026.png]]

Order embedding space allows us to nicely encode subgraph isomorphism relationships. It gives us the following properties:
1. **Transitivity** : If $G_1$ is a subgraph of $G_2$, $G_2$ is a subgraph of $G_3$, then $G_1$ is a subgraph of $G_3$.
2. **Anti-Symmetry** : If $G_1$ is a subgraph of $G_2$, and $G_2$ is a subgraph of $G_1$, then $G_1$ is isomorphic to $G_2$.
3. **Closure under intersection** : The trivial graph of 1 node is a subgraph of any graph.

All the properties above have their counter-parts in the order embedding space.

#### Order Constraint

>[!question] What **loss function** should we use, so that the learned order embedding reflects the subgraph relationship?

We design loss functions based on the **order constraint**:
- Order constraint specifies the ideal order embedding property that reflects subgraph relationships.

![[Pasted image 20240707121200.png]]

GNN embeddings are learned by minimising a **max-margin loss**. The margin between two graphs $G_q$ and $G_t$ is defined as:
$$
E(G_q, G_t) = \sum^D_{i=1} \big( \max(0, z_q[i] - z_t[i]) \big)^2
$$
- To learn the correct order embeddings, we want to learn $z_q$ and $z_t$ such that:
	- $E(G_q, G_t) = 0$ when $G_q$ *is* a subgraph of $G_t$, and
	- $E(G_q, G_t) > 0$ when $G_q$ *is not* a subgraph of $G_t$.
![[Pasted image 20240707121321.png]]

### Training Neural Subgraph matching

- To learn such embeddings, we construct training examples $(G_q, G_t)$ where *half the time* $G_q$ is a subgraph of $G_t$, and the other half it is not.
- Then, we train on these examples by minimising the **max-margin loss**.
	- *For positive examples*: Minimise $E(G_q, G_t)$ when $G_q$ is a subgraph of $G_t$.
	- *For negative examples*: Minimise $\max(0, \alpha - E(G_q, G_t))$
		- Max-margin loss prevents the model from learning the degenerate strategy of moving embeddings further and further apart forever.

![[Pasted image 20240707121655.png]]

#### Training Details

1. How many training examples to sample?
	- At every iteration, we sample new training pairs.
	- Benefit: Every iteration, the model sees different subgraph examples.
	- This improves performance and avoids overfitting, since there are exponential number of possible subgraphs to sample from.

2. How deep is the BFS sampling?
	- A hyper-parameter that trades-off runtime and performance.
	- Usually use 3-5, depending on the size of the dataset.

---
## (3) Mining Frequent Subgraphs

![[Pasted image 20240707122005.png]]

Generally, finding the most frequent size-$k$ motifs requires solving two challenges:
1. **Enumerating** all size-$k$ connected subgraphs.
2. **Counting** #(occurrences of each subgraph type).

However, this is a very difficult problem.
1. Just knowing if a certain subgraph exists in a graph is a **hard computational problem (NP-complete)**.
2. Computation time grows exponentially as the size of the subgraphs increases.
	- Feasible motif size for traditional methods is relatively small (3 to 7).

### Representation Learning

Representation learning can help tackle these challenges.
1. Counting #(occurrences of each subgraph type)
	- Solution: Use GNN to **predict the frequency of the subgraph**.
2. Enumerating all size-$k$ connected subgraphs
	- Solution: Don't enumerate subgraphs but **construct a size-$k$ subgraph incrementally**.
	- Note: We are only interested in *high-frequency subgraphs*.

#### Problem Setup: Frequent Motif Mining

Given:
- Target graph $G_T$
- Size parameter $k$
- Desired number of results $r$

Goal:
- Identify, among *all* possible graphs of $k$ nodes, the $r$ graphs with the highest frequency in $G_T$.

We use the node-level definition:
- The number of nodes $u$ in $G_T$ for which some subgraph of $G_T$ is isomorphic to $G_Q$ and the isomorphism maps $u$ to $v$.

### SPMiner

SPMiner is a neural model to identify frequent motifs.

![[Pasted image 20240707122527.png]]

**Motif Frequency Estimation:**
- Given: Set of subgraphs ("node-anchored neighbourhoods") $G_{N_i}$ of $G_T$ (sampled randomly).
- **Key idea: Estimate frequency of $G_Q$ by counting the number of $G_{N_i}$ such that their embeddings $z_{N_i}$ satisfy $z_Q \leq z_{N_i}$.**
	- This is a consequence of the *order embedding space* property.

![[Pasted image 20240707122749.png]]


>[!notes]- SPMiner Search Procedure
>![[Pasted image 20240707122811.png]]
>![[Pasted image 20240707122836.png]]
>![[Pasted image 20240707122847.png]]
>![[Pasted image 20240707122905.png]]
>![[Pasted image 20240707122915.png]]
>![[Pasted image 20240707122928.png]]


---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 10 Fast neural subgraph matching]]]