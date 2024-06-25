---
tags:
  - graphs
  - knowledge-graphs
creation-date: 2024-06-23 09:38
---
## Knowledge Graph Completion Tasks

![[Pasted image 20240623094109.png]]

What is a KG Completion Task?
- Given an enormous KG, *can we complete the KG*?
	- For a given **(head, relation)** $\rightarrow$ we predict missing **tails**.
	  (Note: This is slightly different from the link prediction task.)

## KG Representation

Edges in KG are represented as **triples $(h,r,t)$**
- *head $(h)$* has *relation $(r)$* with *tail $(t)$*

Key Idea:
- Model entities and relations in embedding space $\mathbb{R}^d$
	- Associate entities and relations with **shallow embeddings**
	  (Note: We do not learn a GNN here)
- Given a triple $(h,r,t)$, the goal is that the **embedding of $(h,r)$ should be close to the embedding of $t$**.

## KG Embedding Models

There are many KG embedding models:
![[Pasted image 20240623094422.png]]

The different models:
1. are based on different geometric intuitions, and
2. capture different types of relations (have different expressivity).

| Model                                                                           | Score                                      | Embedding                                                                            | Sym. | Antisym. | Inv. | Compos. | 1-to-N |
| ------------------------------------------------------------------------------- | ------------------------------------------ | ------------------------------------------------------------------------------------ | ---- | -------- | ---- | ------- | ------ |
| [[TransE (Translating Embeddings for Modelling Multi-relational Data)\|TransE]] | $-\mid\mid h + r - t \mid\mid$             | $h, t, r \in \mathbb{R}^k$                                                           | No   | Yes      | Yes  | Yes     | No     |
| [[TransR (Translation on Relation-specific Spaces)\|TransR]]                    | $- \mid\mid M_r h + r - M_r t \mid\mid$    | $h,t \in \mathbb{R}^k$<br>$r \in \mathbb{R}^d$<br>$M_r \in \mathbb{R}^{d \times k }$ | Yes  | Yes      | Yes  | Yes     | Yes    |
| [[DistMult]]                                                                    | $\langle h, r, t \rangle$                  | $h, t, r \in \mathbb{R}^k$                                                           | Yes  | No       | No   | No      | Yes    |
| [[ComplEx]]                                                                     | $\text{Re}(\langle h, r, \bar{t})\rangle)$ | $h, t, r \in \mathbb{C}^k$                                                           | Yes  | Yes      | Yes  | No      | Yes    |

### Connectivity Patterns in KG

Relations in a heterogenous KG have different properties:

1. **Symmetric (Anti-symmetric)** Relations:
	- Symmetric: $r(h, t) \Rightarrow r(t,h)\ \ \  \forall h,t$ 
		- e.g., Family, Roommate
	- Anti-symmetric: $r(h, t) \Rightarrow r(t,h)\ \ \  (r(h,t) \Rightarrow \neg\ r(t, h)) \ \ \ \forall h,t$ 
		- e.g., Hypernym (a word with a broader meaning: poodle vs dog)

2. **Inverse** Relations:
	- $r_2(h, t) \Rightarrow r_1(t,h)$
	- e.g., (Advisor, Advisee)

3. **Composition (Transitive)** Relations:
	- $r_1(x, y) \land r_2(y, z) \Rightarrow r_3(x,z) \ \ \ \forall x, y, z$
	- e.g., My mother's husband is my father.

4. **1-to-N** Relations:
	- $r(h, t_1) , r(h, t_2), ..., r(h, t_n)$ are all True
	- e.g., $r$ is "StudentsOf".


---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 8 Knowledge Graphs]]