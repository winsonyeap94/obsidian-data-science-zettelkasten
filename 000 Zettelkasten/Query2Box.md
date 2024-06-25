---
tags:
  - graphs
  - knowledge-graphs
creation-date: 2024-06-25 22:15
---
## Query2Box

> [!info] This topic is closely related to [[Box Embeddings]].

Query2Box is an embedding-based framework for [[Reasoning Over Knowledge Graphs|reasoning over knowledge graphs]] that is capable of handling arbitrary Existential Positive First-Order (EPFO) logical queries (i.e., queries that include any set of logical or ($\lor$), logical and ($\land$) and there exists ($\exists$)) in a scalable manner.


## Training Process

1. Sample a query $q$ from the training graph $G_{train}$, answer $v \in [[q]]_{G_{train}}$, and non-answer $v' \notin [[q]]_{G_{train}}$.
2. Embed the query $\mathbf{q}$.
	- Use current operators to compute query embedding.
3. Calculate the score $f_q(v)$ and $f_q(v')$.
4. Optimise embeddings and operators to minimise the loss $\mathcal{l}$ (maximise $f_q(v)$ while minimising $f_q(v')$.
$$
\mathcal{l} = -\log\sigma\Big(f_q(v)\Big) - \log\Big(1 - \sigma\big(f_q(v')\big)\Big)
$$

---
## Query Generation from Templates

To generate a complex query, we start with a **query template**.
- Query template is an *abstraction of a query*.
- We generate a query by instantiating every variable with a concrete entity and relation from the KG.
	- e.g., instantiate *Anchor1* with *ESR2* (a node on KG).
	- e.g., instantiate *Rel1* with *Assoc* (an edge on KG).

![[Pasted image 20240625222421.png]]

>[!example]- How to instantiate a query template given a KG?
![[Pasted image 20240625222434.png]]
![[Pasted image 20240625222518.png]]
![[Pasted image 20240625222531.png]]
![[Pasted image 20240625222557.png]]
![[Pasted image 20240625222610.png]]
![[Pasted image 20240625222624.png]]
![[Pasted image 20240625222638.png]]
![[Pasted image 20240625222652.png]]



---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 9 Reasoning over knowledge graphs]]
- Original Paper: [query2box-iclr20.pdf (stanford.edu)](https://www-cs.stanford.edu/~jure/pubs/query2box-iclr20.pdf)