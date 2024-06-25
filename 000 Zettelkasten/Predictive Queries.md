---
tags:
  - graphs
  - knowledge-graphs
creation-date: 2024-06-24 22:20
---
## Predictive Queries

![[Pasted image 20240624222057.png]]

The general idea of predictive queries in in KG is to **map queries into an embedding space, and then learn to reason in that space**.
- In this process, we embed a query into a single *point* in the Euclidean space, where the *answer nodes are close to the query*.
- **Query2Box** is an algorithm that achieves this.
	- Embeds query into a *hyper-rectangle (box)* in the Euclidean space, where answer nodes are enclosed in the box.

## Embedding Queries using TransE

We can generalise [[TransE (Translating Embeddings for Modelling Multi-relational Data)|TransE]] to multi-hop reasoning.

![[Pasted image 20240624222634.png]]

As a recap, in TransE, we translate $\mathbf{h}$ to $\mathbf{t}$ using $\mathbf{r}$ with score function $f_r(h,t) = -\mid\mid \mathbf{h} + \mathbf{r} - \mathbf{t} \mid\mid$.
- We can interpret this under the context of predictive queries as:
	- **Query embedding**: $\mathbf{q} = \mathbf{h} + \mathbf{r}$
	- And the goal is to get the query embedding $\mathbf{q}$ close to the **answer embedding** $\mathbf{t}$.$$f_q(t)=-\mid\mid\mathbf{q} - \mathbf{t}\mid\mid$$
- The benefit of this approach is that the embedding process *only involves vector addition*. This makes it <mark style="background: #FFF3A3A6;">independent of the number of entities in the KG</mark>.

![[Pasted image 20240624222657.png]]

For example in the two-hop query below, we we simply add the vectors of each embedding to reach our final set of answers.

![[Pasted image 20240624222822.png]]

Insights from this exercise:
1. We can train TransE to optimise knowledge graph completion objective.
2. Since TransE can naturally handle *compositional relations*, it can handle path queries by translating it in the latent space for multiple hops using addition of relation embeddings.
3. For [[DistMult]] and [[ComplEx]], since they cannot handle compositional relations, they cannot be easily extended to handle path queries.


---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 9 Reasoning over knowledge graphs]]