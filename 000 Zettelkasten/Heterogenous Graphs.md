---
tags:
  - graphs
creation-date: 2024-06-22 10:48
---
## Heterogenous Graphs

![[Pasted image 20240622104952.png]]

A heterogenous graph is defined as
$$
G = (V, E, \tau, \phi)
$$
- Nodes with **node types** $v \in V$
	- Node type for node $v$ : $\tau(v)$
- Edges with **edge types** $(u, v) \in E$
	- Edge type for edge $(u, v): \phi(u, v)$
	- Relation type for edge $e$ is a tuple: $r(u, v) = (\tau(u), \phi(u,v), \tau(v) )$

In heterogenous graphs, we use **relation type** to *describe an edge* (as opposed to edge type), because we now have different edge and node types.
- Relation types better capture the interaction between different types of nodes and edges.
- Relation type is defined as a function of *(node start, edge, node end)*.

>[!example]- Many graphs are heterogenous graphs
>![[Pasted image 20240622104910.png]]
>![[Pasted image 20240622104918.png]]
>![[Pasted image 20240622104927.png]]

### When do we need heterogenous graphs?

1. Case 1: Different node/edge types **have different shapes of features**
	- e.g., an "author node" has 4-dim feature, but a "paper node" has 5-dim feature.

2. Case 2: We know different relation types represent **different types of interaction**
	- e.g., (English, translate, French) and (English, translate, Chinese) require different models.

Ultimately, heterogenous graphs are a *more expressive graph representation*. It is able to capture different types of interactions between entities.

However, it *comes with costs*:
1. More expensive (computation, storage)
2. More complex implementation
(That said, there are ways to convert a heterogenous graph into a standard graph, i.e., a homogenous graph)

### Models for handling heterogenous graphs

- [[Relational Graph Convolutional Networks (RGCNs)]]


---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 7 Heterogenous Graphs]]