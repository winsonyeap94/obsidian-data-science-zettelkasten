---
tags:
  - graphs
  - neural-networks
  - recommender-systems
creation-date: 2024-07-07 17:08
---
## Neural Graph Collaborative Filtering (NGCF)

>[!notes]- Limitations of Conventional Collaborative Filtering
>
>The conventional collaborative filtering model is based on **shallow encoders**.
>- There are *no user/item features*.
>	- Instead, use shallow encoders for users and items:
>- For every $u \in U$ and $v \in V$, we prepare shallow learnable embeddings $\mathbf{u}, \mathbf{v} \in \mathbb{R}^D$.
> - Score function for user $u$ and item $v$ is $f_\theta(\mathbf{u}, \mathbf{v}) \equiv \mathbf{z}_u^T \mathbf{z}_v$
>   
>There are limitations of this approach, where:
>1. The model itself **does not explicitly capture graph structure**.
>	- The graph structure is only *implicitly* captured in the training objective.
>2. Only the **first-order graph structure** (i.e., edges) is captured in the training objective.
>	- High-order graph structure (e.g., $K$-hop paths between two nodes) is not explicitly captured.

![[Pasted image 20240707174242.png]]

NGCF aims to improve on the conventional collaborative filtering model.
- NGCF ***explicitly*** incorporates high-order graph structure when generating user/item embeddings.
- Key idea: Use a GNN to generate graph-aware user/item embeddings.

### NGCF Framework

Given a user-item bipartite graph, the NGCF framework looks like below:
1. Prepare *shallow learnable embedding* for each node.
2. Use multi-layer GNNs to propagate embeddings along the bipartite graph.
	- High-order graph structure is captured.
3. Final embeddings are explicitly graph-aware.

Two kinds of learnable parameters are jointly learned:
1. Shallow user/item embeddings
2. GNN's parameters

![[Pasted image 20240707174556.png]]
![[Pasted image 20240707174700.png]]
![[Pasted image 20240707174715.png]]



---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 11 GNNs for Recommenders]]