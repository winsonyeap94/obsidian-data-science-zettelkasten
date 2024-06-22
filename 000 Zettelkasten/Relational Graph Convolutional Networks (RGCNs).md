---
tags:
  - graphs
  - neural-networks
creation-date: 2024-06-22 10:57
---
## Relational Graph Convolutional Networks (RGCNs)

RGCNs are an extension of [[Graph Convolutional Networks (GCN)]]s designed to handle *relational data* (e.g., knowledge graphs).
- RGCNs extend the basic message passing framework of GCNs to handle multiple types of relations.
	- In a standard GCN, the message passing rule aggregates information from neighbouring nodes to update a model's representation. RGCNs modify this rule to account for *different types of edges* by using relation-specific weight matrices.
	- Read more: [[Heterogenous Graphs]]

## Algorithm

In order to cater for multiple relation types, RGCNs use **different neural network weights for different relation types**.

![[Pasted image 20240622144851.png]]

Equation for node update:
$$
\mathbf{h}_v^{(l+1)} = \sigma \bigg( \sum_{r\in R} \sum_{u \in N_v^r} \frac{1}{c_{v, r}} \mathbf{W}_r^{(l)} \mathbf{h}_u^{(l)} + \mathbf{W}_0^{(l)} \mathbf{h}_{v}^{(l)} \bigg)
$$
In Message + Aggregation format:
- **Message:**
	- Each neighbour of a given relation:$$\mathbf{m}_{u,r}^{(l)} = \frac{1}{c_{v,r}} \mathbf{W}_r^{(l)} \mathbf{h}_u^{(l)}$$
	- Self-loop:$$\mathbf{m}_{v}^{(l)} = \mathbf{W}_0^{(l)}\mathbf{h}_v^{(l)}$$
- **Aggregation:**
	- Sum over messages from neighbours and self-loop, then apply activation:$$\mathbf{h}_v^{(l+1)} = \sigma \bigg( \text{SUM}\Big(\Big\{ \mathbf{m}_{u, r}^{(l)} , u\in N(v) \Big\} \cup \Big\{ \mathbf{m}_v^{(l)}\Big\}  \Big)\bigg)$$

---
## Scalability

As we now have different set of weights for each relationship type, this means that there will be a rapid growth of the number of parameters w.r.t. the number of relations. 
- As such, **overfitting becomes an issue**.
- Each relation has $L$ matrices: $\mathbf{W}_r^{(1)}, \mathbf{W}_r^{(2)}, ..., \mathbf{W}_r^{(L)}$
- The size of each matrix $\mathbf{W}_r^{(l)}$ is $d^{(l+1)} \times d^{(l)}$

There are two methods to regularise the weights $\mathbf{W}_r^{(l)}$:
1. Using block diagonal matrices
2. Basis / Dictionary learning

### (1) Block Diagonal Matrices

![[Pasted image 20240622145841.png]]

Key Idea: **Make the weights sparse**.
- We can do this by using block diagonal matrices for $\mathbf{W}_r$.

If we use $B$ low-dimensional matrices, then the number of parameters reduces from $d^{(l+1)} \times d^{(l)}$ to $B \times \frac{d^{(l+1)}}{B} \times \frac{d^{(l)}}{B}$.

### (2) Basis / Dictionary Learning

Key Idea: **Share weights across different relations**.
- We represent the matrix of each relation as a *linear combination of basis transformations* $\mathbf{W}_r = \sum^{B}_{b=1} a_{rb} \cdot \mathbf{V}_b$, where $\mathbf{V}_b$ is shared across all relations.
	- $\mathbf{V}_b$ : The basis matrices
	- $a_{rb}$ : Importance weight of matrix $\mathbf{V}_b$
- Now each relation only needs to learn $\{a_{rb}\}^{B}_{b=1}$, which is $B$ scalars.

---
## Examples

> [!example]- Entity / Node Classification
> ![[Pasted image 20240622150443.png]]

> [!example]- Link Prediction
> ![[Pasted image 20240622150509.png]]
> ![[Pasted image 20240622150519.png]]
> ![[Pasted image 20240622150537.png]]
> ![[Pasted image 20240622150547.png]]
> ![[Pasted image 20240622150559.png]]
> ![[Pasted image 20240622150610.png]]


---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 7 Heterogenous Graphs]]