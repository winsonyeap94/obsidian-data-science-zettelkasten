---
tags:
  - graphs
  - knowledge-graphs
creation-date: 2024-06-23 09:48
---
## TransE (Translating Embeddings for Modelling Multi-relational Data)

![[Pasted image 20240623105327.png]]

TransE is an algorithm used for **embedding [[Knowledge Graphs|knowledge graphs]]**.
- It represents entities and relationships as vectors in a low-dimensional space, interpreting relationships as **translations between entity embeddings**.

## Algorithm

Intuition: **Translation** 

![[Pasted image 20240623105356.png]]

For a triple $(h, r, t)$, let $\mathbf{h}, \mathbf{r}, \mathbf{t} \in \mathbb{R}^d$ be embedding vectors.
- TransE: $\mathbf{h} + \mathbf{r} \approx \mathbf{t}$ if the given link exists else $\mathbf{h} + \mathbf{r} \neq \mathbf{t}$.
- Entity scoring function: $f_r(h, t) = - \mid\mid \mathbf{h} + \mathbf{r} + \mathbf{t} \mid\mid$

![[Pasted image 20240623105641.png]]

---
## Relationship Patterns in TransE

### Relationships that TransE is able to model

1.  Anti-symmetric Relations
	- $\mathbf{h} + \mathbf{r} = \mathbf{t}$, but $\mathbf{t} + \mathbf{r} \neq \mathbf{h}$
![[Pasted image 20240623105949.png|300]]

2. Inverse Relations
	- $\mathbf{h} + \mathbf{r_2} = t$, we can set $\mathbf{r_1} = -\mathbf{r_2}$
![[Pasted image 20240623110130.png|300]]

3. Composition (Transitive) Relations
	- $\mathbf{r_3} = \mathbf{r_1} + \mathbf{r_2}$
![[Pasted image 20240623110225.png|350]]

### Limitations - Relationships that TransE cannot model

1. Symmetric Relations
	- Only if $\mathbf{r}=0$,  $\mathbf{h} = \mathbf{t}$
![[Pasted image 20240623110339.png|700]]

2. 1-to-N Relations
	- $\mathbf{t_1}$ and $\mathbf{t_2}$ will map to the same vector, although they are different entities.
![[Pasted image 20240623110426.png|700]]


---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 8 Knowledge Graphs]]