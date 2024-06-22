---
tags:
  - graphs
  - "#neural-networks"
  - augmentation
creation-date: 2024-06-06 11:23
---
# Graph Augmentation for GNNs

![[Pasted image 20240606112731.png]]

### Why Augment Graphs?

Our assumption so far has been that $\text{Raw Input Graph} = \text{Computational Graph}$
- Reasons for breaking this assumption:
	1. Features:
		1. The input graph **lacks features**
	2. Graph Structure
		1. The graph is **too sparse** $\rightarrow$ inefficient message passing
		2. The graph is **too dense** $\rightarrow$ message passing is too costly
		3. The graph is **too large** $\rightarrow$ cannot fit the computational graph into a GPU
	3. It's **unlikely that the input graph happens to be the optimal computation graph** for embeddings

---
### Graph Augmentation Approaches

1. **Graph Feature Augmentation**
	1. The input graph lacks features $\rightarrow$ *feature augmentation*
2. **Graph Structure Augmentation**
	1. The graph is too sparse $\rightarrow$ *add virtual nodes / edges*
	2. The graph is too dense $\rightarrow$ *sample neighbours when doing message passing*
	3. The graph is too large $\rightarrow$ *sample subgraphs to compute embeddings*

#### (1) Feature Augmentation

Why do we need feature augmentation?
1. **Input graph does not have node features**
	- This is common when we only have the adjacency matrix
	- Standard approaches:
		1. **Assign constant values to nodes**
		   ![[Pasted image 20240606113203.png|300]]
		2. **Assign unique IDs to nodes**
			- These IDs are converted into *one-hot vectors*![[Pasted image 20240606113233.png]]

2. **Certain structures are hard to learn by GNN**
	- Example: Cycle count feature
		- Can GNN learn the length of a cycle that $v_1$ resides in? Unfortunately, no.![[Pasted image 20240606113651.png]]
	- Solution: We can use *cycle count as augmented node features*.![[Pasted image 20240606113810.png]]
	- Other commonly used augmented features:
		1. Node degree
		2. Clustering coefficient
		3. PageRank
		4. Centrality


>[!info]- Comparison of **constant** vs **one-hot** node feature augmentation approaches
>
>
>|  | Constant node feature | One-hot node feature |
>| --- | --- | --- |
>| Expressive power | **Medium**. All the nodes are identical, but GNN can still learn from the graph structure. | **High**. Each node has a unique ID, so *node-specific information can be stored*. |
>| Inductive learning (Generalise to unseen nodes) | **High**. Simple to generalise to new nodes: we assign constant feature to them, then apply our GNN. | **Low**. *Cannot generalise to new nodes*: new nodes introduce new IDs, GNN doesn't know how to embed unseen IDs. |
>| Computational cost | **Low**. Only 1-dimensional feature. | **High**. High-dimensional feature, cannot apply to large graphs. |
>| Use cases | Any graph, inductive settings (generalise to new nodes). | Small graph, transductive settings (no new nodes). |

#### (2) Structure Augmentation 

##### Adding Virtual Nodes / Edges

>[!info] Motivation: Augment sparse graphs

1. **Add virtual edges**
	- Common approach: *Connect 2-hop neighbours via virtual edges*.
	-  Intuition: Instead of using adjacency matrix $A$ for GNN computation, use $A + A^2$.![[Pasted image 20240606114932.png]]

2. **Add virtual nodes**
	- The virtual node will *connect to all the nodes in the graph*.
		- Suppose in a sparse graph, two nodes have shortest path distance of 10.
		- After adding the virtual node, all the nodes will have a distance of 2.
			- Node A -- Virtual Node -- Node B
	- Benefits: Greatly *improves message passing in sparse graphs*.
		![[Pasted image 20240606115128.png|150]]

##### Node Neighbourhood Sampling

>[!info] Motivation: Overcome the problem when the graph is too large/dense.

Previous, we use all the nodes for message passing. If the graph is too large, then instead we **(randomly) sample a node's neighbourhood** for message passing.
- For example, we can randomly choose 2 neighbours to pass messages.
	- Only nodes $B$ and $D$ will pass message to $A$.![[Pasted image 20240606115349.png]]
- Next time when we compute the embeddings, we can sample different neighbours.
	- Only nodes $C$ and $D$ will pass message to $A$.![[Pasted image 20240606115411.png]]
- In expectation, we can get embeddings similar to the case where all the neighbours are used.
	- Benefits: **Greatly reduce computational cost**
		- Allows for scaling to large graphs


---
# References

- [[CS224W - Machine Learning with Graphs#Chapter 5 GNN Augmentation and Training]]
