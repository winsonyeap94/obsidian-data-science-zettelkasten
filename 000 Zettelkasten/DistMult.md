---
tags:
  - graphs
  - knowledge-graphs
creation-date: 2024-06-23 20:39
---
## DistMult

DistMult is an algorithm which was introduced as an improvement over [[TransE (Translating Embeddings for Modelling Multi-relational Data)|TransE]] and [[TransR (Translation on Relation-specific Spaces)|TransR]]. 
- In TransE and TransR, the scoring function $f_r(h,t)$ is *negative of L1/L2 distance*.
- For DistMult, the idea is to use **bilinear modelling**.
	- Score function: $f_r(h, t) = h \cdot A \cdot t$
	  where $\mathbf{h}, \mathbf{t} \in \mathbb{R}^k$, $\mathbf{A} \in \mathbb{R}^{k\times k}$
	- There is a problem with this approach -- the method is too general and is prone to overfitting as Matrix $A$ is too expressive.
	- Hence, the fix is to **limit $A$ to be diagonal**, and this becomes the DistMult algorithm.

## Algorithm

In DistMult, entities and relations are vectors in $\mathbb{R}^k$.

Score function:
$$
f_r(h,t) = \langle \mathbf{h}, \mathbf{r}, \mathbf{t}\rangle = \sum_i \mathbf{h}_i \cdot \mathbf{r}_i \cdot \mathbf{t}_i
$$
where $\mathbf{h}, \mathbf{r}, \mathbf{t} \in \mathbb{R}^k$.

![[Pasted image 20240623205407.png]]


**Intuition of the score function:**
The intuition of the score function is that it can be viewed as a *cosine similarity* between $\mathbf{h} \cdot \mathbf{r}$ and $\mathbf{t}$, where $\mathbf{h} \cdot \mathbf{r}$ is defined as $[h \cdot r]_i = h_i \cdot r_i$

![[Pasted image 20240623205542.png]]

---
## Relationship Patterns in DistMult

### Relationships that DistMult is able to model

1. 1-to-N Relations:
	- $\langle \mathbf{h}, \mathbf{r}, \mathbf{t_1}\rangle = \langle \mathbf{h}, \mathbf{r}, \mathbf{t}_2\rangle$ 

![[Pasted image 20240623205729.png|400]]

2. Symmetric Relations
	- $f_r(h, t) = \langle\mathbf{h}, \mathbf{r}, \mathbf{t}\rangle = \sum_i \mathbf{h}_i \cdot \mathbf{r}_i \cdot \mathbf{t}_i = \langle\mathbf{t}, \mathbf{r}, \mathbf{h} \rangle = f_r(t,h)$

### Limitations - Relationships that DistMult cannot model

1. Anti-Symmetric Relations
	- $f_r(h, t) = \langle\mathbf{h}, \mathbf{r}, \mathbf{t}\rangle = \langle\mathbf{t}, \mathbf{r}, \mathbf{h}\rangle = f_r(t,h)$ 
	  (this does not satisfy anti-symmetric relations)
	- Because $r(h,t)$ and $r(t,h)$ always have the same score. 
	- As a result, DistMult *cannot differentiate between head entity and tail entity*. This means that all relations are modelled as symmetric regardless, i.e., even anti-symmetric relations will be represented as symmetric.

2. Inverse Relations
	- Assume DistMult does not model inverse relations:
	  $f_{r_2}(h,t) = \langle\mathbf{h}, \mathbf{r}_2, \mathbf{t}\rangle = \langle\mathbf{t}, \mathbf{r}_1, \mathbf{h}\rangle = f_{r_1}(t,h)$
	- For example, $\mathbf{r}_2 = \mathbf{r}_1$ solves this.
	- But semantically, this does not make sense $\rightarrow$ The embedding of "Advisor" relation should not be the same as "Advisee" relation.

3. Composition Relation
	- Because dot-product is commutative ($a \cdot b = b \cdot a$), DistMult does not distinguish between head and tail entities. Therefore, it cannot model composition.


---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 8 Knowledge Graphs]]
- Original Paper: [1412.6575 (arxiv.org)](https://arxiv.org/pdf/1412.6575)