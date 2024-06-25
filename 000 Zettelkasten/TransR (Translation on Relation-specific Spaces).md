---
tags:
  - graphs
  - knowledge-graphs
creation-date: 2024-06-23 11:04
---
## TransR (Translation on Relation-specific Spaces)

TransR is an extension of the [[TransE (Translating Embeddings for Modelling Multi-relational Data)|TransE]] algorithm for knowledge graph embedding. It addresses some of the limitations of TransE by introducing **relation-specific spaces for entity projection**.

![[Pasted image 20240623110923.png]]

The basic idea of TransR is illustrated as above. For each triple $(h,r,t)$, entities in the entity space are first projected into $r$-relation space as $h_r$ and $t_r$ with operation $\mathbf{M}_r$, and then $h_r + r \approx t_r$.

## Algorithm

![[Pasted image 20240623110845.png]]

TransR models **entities** as vectors in the entity space $\mathbb{R}^d$ and model each **relation** as a vector in relation space $\mathbf{r}\in\mathbb{R}^k$ with $\mathbf{M}_r \in \mathbb{R}^{k\times d}$ as the projection matrix.
- This means that we use $\mathbf{M}_r$ to *project* from entity space $\mathbb{R}^d$ to relation space $\mathbb{R}^k$.

$$
\begin{align}
\mathbf{h}_\perp & = \mathbf{M}_r\mathbf{h} \\
\mathbf{t}_\perp & = \mathbf{M}_r\mathbf{t}
\end{align}
$$
Score function: $f_r(h, t) = -\mid\mid\mathbf{h}_\perp + \mathbf{r} - \mathbf{t}_\perp \mid\mid$


---
## Relationship Patterns in TransR

### Relationships that TransR is able to model

1. Symmetric Relations
	- $\mathbf{r}=0$ , then $\mathbf{h}_\perp = \mathbf{M}_r\mathbf{h} = \mathbf{M}_r\mathbf{t} = \mathbf{t}_\perp$

![[Pasted image 20240623111711.png]]

2. Anti-Symmetric Relations
	- When $\mathbf{r} \neq 0$, $\mathbf{M}_r\mathbf{h} + \mathbf{r} = \mathbf{M}_r\mathbf{t}$.
	- Then $\mathbf{M}_r\mathbf{t} + \mathbf{r} \neq \mathbf{M}_r\mathbf{h}$

![[Pasted image 20240623111859.png]]

3. 1-to-N Relations
	- We can learn $\mathbf{M}_r$ so that $\mathbf{t}_\perp = \mathbf{M}_r \mathbf{t}_1 = \mathbf{M}_r \mathbf{t}_2$.
	- Note that $\mathbf{t}_1$ does not need to equal to $\mathbf{t}_2$.

![[Pasted image 20240623203459.png]]

4. Inverse Relations
	-  $\mathbf{r}_2 = -\mathbf{r}_1$ , $\mathbf{M}_{r_1} = \mathbf{M}_{r_2}$
	-  Then $\mathbf{M}_{r_1} \mathbf{t} + \mathbf{r}_1 = \mathbf{M}_{r_1} \mathbf{h}$ and $\mathbf{M}_{r_2} \mathbf{h} +\mathbf{r}_2 = \mathbf{M}_{r_2}\mathbf{t}$

![[Pasted image 20240623203658.png]]

5. Composition Relations
	- TransR models a triple with linear functions, and linear functions are chainable.
	- If f(x) and g(x) are linear, then f(g(x)) is also linear.
	- $\mathbf{h} \in \text{Kernel}(\mathbf{M})$ , then $\mathbf{M}\cdot\mathbf{h}=0$.

![[Pasted image 20240623203828.png]]


---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 8 Knowledge Graphs]]
- [Summary of Translate Model for Knowledge Graph Embedding | by Xu LIANG | Towards Data Science](https://towardsdatascience.com/summary-of-translate-model-for-knowledge-graph-embedding-29042be64273)