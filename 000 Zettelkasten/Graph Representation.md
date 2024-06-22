---
tags:
  - graphs
creation-date: 2024-05-31 22:40
---
# Graph Representation

## Components of a Network

![[Pasted image 20240531224546.png]]

We can also attach **attributes** onto nodes and edges. Possible options:
1. *Weight* (e.g., frequency of communication)
2. *Ranking* (e.g., best friend, second best friend)
3. *Type* (friend, relative, co-worker)
4. *Sign* (Friend vs Foe, Trust vs Distrust)
5. *Properties* depending on the structure of the rest of the graph (e.g., Number of common friends)

## Directed vs Undirected Graphs

![[Pasted image 20240531224609.png]]

## Node Degrees
### Undirected graphs

Node degree, $k_i$ is the **number of edges adjacent** to node $i$.

> [!example]- Node A below would have a node degree of 4.
>  ![[Pasted image 20240531224730.png]]

**Average degree** is the average of all the degrees in the network.
- The reason for $2$ in $2E$ is because each edge is counted twice in a graph.
$$
\bar{k} = \langle k \rangle = \frac{1}{N}\sum^N_{i=1}k_i = \frac{2E}{N}
$$
### Directed graphs

In directed networks, we define an **in-degree** and **out-degree**.

> [!example]- Example of a node degree in a directed graph
> ![[Pasted image 20240531225305.png]]

The (total) degree of a node is the sum of in- and out- degrees.

$$
k^{in}_C = 2 \ \ \ \ \ \ \ \ \ \ \  k^{out}_C = 1 \ \ \ \ \ \ \ \ \ \ \ k_C = 3
$$
$$
\bar{k} = \frac{E}{N} \ \ \ \ \ \ \ \ \ \ \  \bar{k^{in}} = \bar{k^{out}}
$$

## Bipartite Graphs

![[Pasted image 20240531225736.png]]
Bipartite graph is a graph whose nodes can be divided into two disjoint sets $U$ and $V$ such that every link connects a node in $U$ to one in $V$; that is, $U$ and $V$ are *independent sets*.

>[!example]- Examples:
>1. Authors-to-Papers (they authored)
>2. Actors-to-Movies (they appeared in)
>3. Users-to-Movies (they rated)
>4. Recipes-to-Ingredients (they contain)

### Folded / Projected Bipartite Graphs

If we have a bipartite graph, we can project it either on the left size ($U$) or the right size ($V$) to form a new graph.
- Nodes are connected if they have a neighbour in common.

Examples:
1. Author collaboration networks
2. Movie co-rating networks

![[Pasted image 20240531230059.png]]

## Methods of Representing Graphs

### (1) Adjacency Matrix

Adjacency matrices for undirected graphs are symmetric, but not for directed graphs.

![[Pasted image 20240531230143.png]]

With adjacency matrix, we can then think of node degrees as the *summation of rows/columns* along the node in the adjacency matrix.

![[Pasted image 20240531230308.png]]

However, the downside of adjacency matrix is that it results in a very *sparse matrix*.

### (2) Edge List

This is preferred in most deep learning use cases.

However, this is harder to do any analysis on the graph. For example, it's not trivial to compute the degrees of the nodes in this representation.

![[Pasted image 20240531230543.png]]

### (3) Adjacency List

![[Pasted image 20240531230629.png]]




---
# References

- [[CS224W - Machine Learning with Graphs#Chapter 1 Introduction]]