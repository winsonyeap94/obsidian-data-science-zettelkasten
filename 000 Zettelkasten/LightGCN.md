---
tags:
  - graphs
  - neural-networks
  - recommender-systems
creation-date: 2024-07-07 17:47
---
## LightGCN

The motivation behind LightGCN is to further improve on [[Neural Graph Collaborative Filtering (NGCF)]] by **simplifying the GNN used in NGCF** (e.g., removing its learnable parameters).
- Observation: <mark style="background: #FFF3A3A6;">Shallow learnable embeddings are already quite expressive</mark> (they are learned for every user/item node).
	- Most of the parameter counts are in shallow embeddings when $N$ (# nodes) $>> D$ (embedding dimensionality).
		- Shallow embeddings : $O(ND)$
		- GNN : $O(D^2)$
	- The GNN parameters may not be so essential for performance.

Overview of the idea:
1. Adjacency matrix for a bipartite graph
2. Matrix formulation of GCN
3. Simplification of GCN by removing non-linearity

### Adjacency and Embedding Matrices

![[Pasted image 20240707211210.png]]

### Matrix Formulation of GCN

The **diffusion matrix** is defined as below.
- Let $D$ be the degree matrix of $A$.
- The normalised adjacency matrix $\tilde{A}$ is defined as $$\tilde{A}\equiv D^{-1/2}AD^{-1/2}$$
	- Note: This is different from the original GCN; self-connection is omitted here.
- Let $E^{(k)}$ be the embedding matrix at $k$-th layer.
- Each layer of GCN's aggregation can be written in matrix form: $$E^{(k+1)}=\text{ReLU}(\tilde{A}E^{(k)} W^{(k)})$$
	- where $\tilde{A}E^{(k)}$ is the neighbourhood aggregation, and $W^{(k)}$ is a learnable linear transformation.

### Simplifying GCN

We can simply the GCN by **removing ReLU non-linearity**:
$$
E^{(k+1)} = \tilde{A}E^{(k)}W^{(k)}
$$

The final node embedding matrix is given as:
![[Pasted image 20240707211745.png]]
![[Pasted image 20240707211807.png]]

In essence, **the algorithm is simplified as applying $E \leftarrow \tilde{A}E$ for $K$ times**.
- <mark style="background: #FFF3A3A6;">Each matrix multiplication diffuses the current embeddings to their one-hop neighbours.</mark>
- Note: $\tilde{A}^K$ is dense and never gets materialised. Instead, the above iterative matrix-vector product is used to compute $\tilde{A}^K E$.

#### Multi-Scale Diffusion

We can consider **multi-scale diffusion**.
$$
\alpha_0 E^{(0)} + \alpha_1 E^{(1)} + \alpha_2 E^{(2)} + ... + \alpha_K E^{(K)}
$$
- The above includes embeddings diffused at multiple hop scales.
- $\alpha_0 E^{(0)} = \alpha_0 \tilde{A}^{(0)} E^{(0)}$ acts as a *self-connection* (that is omitted in the definition of $\tilde{A}$).
- The coefficients ($\alpha_0, ..., \alpha_K$) are hyperp-parameters.

For simplicity, LightGCN uses the **uniform coefficient**.
$$
\alpha_k = \frac{1}{K+1} \ \ \ \ \ \text{for } k = 0, ..., K
$$
>[!question]- Why does the simple diffusion propagation work so well?
>Answer: The diffusion *directly encourages the embeddings of similar users/items to be similar*.
>- Similar users share many common neighbours (items) and are expected to have similar future preferences (interact with similar items).

>[!note]- LightGCN vs GCN
>The embedding propagation of LightGCN is closely related to GCN. 
>
>Recall: The neighbourhood aggregation part of GCN
>$$
>h_v^{(k+1)} = \sum_{u\in N(v)}\frac{1}{\sqrt{d_u}\sqrt{d_v}}\cdot h_u^{(k)}
>$$
>- Self-loop is added in the neighbourhood definition.
>  
>LightGCN uses the same equation, except that:
>- Self-loop is *not added* in the neighbourhood definition.
>- Final embedding takes the *average of embeddings from all layers*: $h_v= \frac{1}{K+1}\sum^K_{k=0}h_v^{(k)}$.

### Model Overview

![[Pasted image 20240707212213.png]]
![[Pasted image 20240707212220.png]]
![[Pasted image 20240707212229.png]]
![[Pasted image 20240707212237.png]]








---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 11 GNNs for Recommenders]]