---
tags:
  - graphs
  - feature-engineering
creation-date: 2024-06-01 11:11
---
# Traditional Feature-based Methods for Graphs

In general, feature engineering for graphs can be categorised into three types:
1. Node Features
2. Link Features
3. Graph Features

---
## 1. Node Features

**Goal: Characterise the structure and position of a node in the network:**

The various methods can be categorised as:
- Importance-based features:
	- Node degree
	- Node centrality (multiple measures)
- Structure-based features:
	- Node degree
	- Clustering coefficient
	- Graphlets (and GDVs)

### 1.1 Node Degree
![[Pasted image 20240601111322.png]]
- The degree $k_v$ of node $v$ is the **number of edges** (neighbouring nodes) the node has.
- Treats all neighbouring nodes *equally*.

### 1.2 Node Centrality

- Node degree counts the neighbouring nodes without capturing their importance.
- Node centrality $c_v$ instead takes the **node importance** in a graph into account.
- Different ways to model importance:
	- Eigenvector centrality
	- Betweenness centrality
	- Closeness centrality
	- and many others...

#### 1.2.1 Eigenvector Centrality

- A node $v$ is important if <mark style="background: #FFF3A3A6;">surrounded by important neighbouring nodes</mark> $u \in N(v)$.
![[Pasted image 20240601111629.png]]
- The largest eigenvalue $\lambda_{max}$ is always positive and unique (by Perron-Frobenius Theorem).
- The leading eigenvector $c_{max}$ is used for centrality.

#### 1.2.2 Betweenness Centrality

- A node is important if it lies on many shortest paths between other nodes.
$$
c_v = \sum_{s\neq v\neq t} \frac{\text{\#(shortest paths between s and t that contain v)}}{\text{\#(shortest paths between s and t)}}
$$
![[Pasted image 20240601111749.png]]

#### 1.2.3 Closeness Centrality

- A node is important if it has small shortest path lengths to all other nodes.
$$
c_v = \frac{1}{\sum_{u \neq v} \text{(shortest path length between u and v)}}
$$
![[Pasted image 20240601112005.png]]

### 1.3 Clustering Coefficient

- Measures how connected $v$'s neighbouring nodes are:
![[Pasted image 20240601112226.png]]


### 1.3.1 Graphlets

![[Pasted image 20240601112509.png]]
- Observation: Clustering coefficient counters the #(triangles) in the ego-network
- We can generalise this by counting **#(pre-specified subgraphs)**, i.e., graphlets.
- Graphlets are **rooted** connected non-isomorphic subgraphs.

> [!info]- Definition of different graphlet structures
> ![[Pasted image 20240601112621.png]]

Using graphlets, we can derive another feature called **Graphlet Degree Vector (GDV)**, which is a graphlet-based feature for nodes. Comparing that against other similar degree features:
- *Degree* counts the *#(edges)* that a node touches.
- *Clustering coefficient* counts the *#(triangles)* that a node touches.
- *GDV* counts the *#(graphlets)* that a node touches.

> [!example]- Example of GDV
> Using the GDV helps describes the local behaviour of the network in which the node participates in.
> 
> ![[Pasted image 20240601113139.png]]

Considering graphlets on **2 to 5 nodes**, we get:
- *Vector of 73 coordinates* as a signature of a node that describes the topology of a node's neighbourhood.
- Captures its interconnectivities out to a distance of *4 hops*.

GDV provides a measure of a **node's local network topology**.
- Comparing vectors of two nodes provides a more detailed measure of local topological similarity than node degrees or clustering coefficient.


---
## 2. Link Features

Types of link features:
- Distance-based feature
- Local neighbourhood overlap
- Global neighbourhood overlap

### 2.1 Distance-based Features

**Shortest-path distance between two nodes**
- However, this does not capture the degree of neighbourhood overlap:
	- Node pair $(B, H)$ has 2 shared neighbouring nodes, while pairs $(B, E)$ and $(A, B)$ only have 1 such node.

![[Pasted image 20240601215456.png]]

### 2.2 Local Neighbourhood Overlap

This approach tries to **capture the strength of connection (# of neighbouring nodes) between two nodes** $v_1$ and $v_2$.
- However, the limitation of this approach is that this metric is *always zero if the two nodes do not have any neighbours in common*. The two nodes may still potentially be connected in the future.
![[Pasted image 20240601220549.png|300]]

| No  | Metric                | Formula                                                                    | Example                                                                                                                          |
| --- | --------------------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Common Neighbours     | $\lvert N(v_1) \cap N(v_2) \rvert$                                         | $\lvert N(A) \cap N(B) \rvert = \lvert \{C\} \rvert = 1$                                                                         |
| 2   | Jaccard's Coefficient | $\frac{\lvert N(v_1) \cap N(v_2)\rvert}{\lvert N(v_1) \cup N_(v_2)\rvert}$ | $\frac{\lvert N(A) \cap N(B)\rvert}{\lvert N(A) \cup N_(B)\rvert} = \frac{\lvert\{C\}\rvert}{\lvert\{C,D\}\rvert} = \frac{1}{2}$ |
| 3   | Adamic-Adar Index     | $\sum_{u \in N(v_1) \cap N(v_2)} \frac{1}{\log(k_u)}$                      | $\frac{1}{\log(k_C)}=\frac{1}{\log 4}$                                                                                           |
### 2.3 Global Neighbourhood Overlap

Global neighbourhood overlap metrics resolve the limitation of local neighbourhood overlap by *considering the entire graph*.

![[Pasted image 20240601220909.png]]
### 2.3.1 Katz Index

Katz index count the **number of paths of all lengths** between a given pair of nodes.
- We can compute # of paths between two nodes quickly using *adjacency matrix*.

![[Pasted image 20240601221616.png]]

---
## 3. Graph Features and Graph Kernels

### Background: Kernel Methods

**Idea: Design kernels instead of feature vectors.**
- Kernel methods are widely-used for traditional ML for graph-level prediction.
- A quick introduction to Kernels:
	- Kernel $K(G, G') \in \mathbb{R}$ measures similarity between data.
	- Kernel matrix $\mathbf{K} = (K(G, G'))_{G,G'}$ must always be positive semidefinite (i.e., has positive eigenvalues).
	- There exists a feature representation $\phi(\cdot)$ such that $K(G,G')=\phi(G)^T \phi(G')$
	- Once the kernel is defined, off-the-shelf ML models (e.g., kernel SVM) can be used to make predictions.

**Graph Kernels**: Measure similarity between two graphs. 
- *Goal: Design graph feature vector $\phi(G)$.*
- Examples:
	- Graphlet Kernel
	- Weisfeiler-Lehman Kernel
	- Other kernels are also proposed in literature:
		- Random-walk kernel
		- Shortest-path graph kernel

### 3.1 Graphlet Kernel

**Key idea: Count the number of different graphlets in a graph.**

> [!note]- Definition of graphlets here is slightly different from node-level features
> 
> The two differences are:
> 1. Nodes in graphlets here *do not need to be connected* (allows for isolated nodes).
> 2. The graphlets here are *not rooted*.
> 
> ![[Pasted image 20240601223022.png]]

![[Pasted image 20240601223223.png]]

Given two graphs $G$ and $G'$, graphlet kernel is computed as
$$
K(G,G') = \mathbf{f}_G^T\mathbf{f}_G'
$$
- Problem: If $G$ and $G'$ have different sizes, that will greatly skew the value.
- Solution: *Normalise* each feature vector.
$$
\mathbf{h}_G = \frac{\mathbf{f}_G}{\sum(\mathbf{f}_G)} \ \ \ \ \ \ \ \ \ \ K(G, G') = \mathbf{h}_G^T \mathbf{h}_{G'}
$$

> [!warning]- Limitation: Counting graphlets is expensive!
> - Counting size-$k$ graphlets for a graph with size $n$ by enumeration takes $n^k$.
> - This is unavoidable in the worst-case since a subgraph isomorphism test (judging whether a graph is a subgraph of another graph) is NP-hard.
> - If a graph's node degree is bounded by $d$, an $O(nd^{k-1})$ algorithm exists to count all the graphlets of size $k$.

### 3.2 Weisfeiler-Lehman Kernel

**Goal: Design an efficient graph feature descriptor $\phi(G)$**

Idea: Use <mark style="background: #FFF3A3A6;">neighbourhood structure to iteratively enrich node vocabulary</mark>.
- Generalised version of *bag-of-node degrees* since node degrees are one-hop neighbourhood information.
- Algorithm to achieve this: **Colour refinement**

> [!note]- Colour Refinement
> Given a graph $G$ with a set of nodes $V$.
> - Assign an initial colour $c^{(0)}(v)$ to each node $v$.
> - Iteratively refine node colours by 
>   $$
>   c^{(k+1)}(v) = \text{HASH}( \{ c^{(k)}(v) , \{ c^{(k)}(u) \}_{u \in N(v)} \} )
>   $$
>   where $\text{HASH}$ maps different inputs to different colours.
> - After $K$ steps of colour refinement, $c^{(K)}(v)$ **summarises the structure of $K$-hop neighbourhood**.
>   
> ![[Pasted image 20240601224315.png]]
> ![[Pasted image 20240601224425.png]]
> ![[Pasted image 20240601224517.png]]
> ![[Pasted image 20240601224603.png]]

![[Pasted image 20240601224617.png]]
![[Pasted image 20240601224647.png]]

WL Kernel is **computationally efficient**.
- The time complexity for colour refinement at each step is linear in #(edges), since it involves aggregating neighbouring colours.
- When computing a kernel value, only colours appeared in the two graphs need to be tracked.
	- Thus, #(colours) is at most the total number of nodes.
- Counting colours takes linear-time w.r.t. #(nodes).
- In total, time complexity is *linear in #(edges)*.





---
# References

- [[CS224W - Machine Learning with Graphs#Chapter 1b Traditional Feature-based Methods]]