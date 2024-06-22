---
tags:
  - graphs
  - neural-networks
creation-date: 2024-06-05 22:06
---
# Graph Convolutional Networks (GCN)

The idea of GCNs is to mimic the benefits of convolutions which we see as part of [[Convolutional Neural Networks (CNN)]].

![[Pasted image 20240605221210.png]]


## Algorithm

Initialisation:
$$
h_v^{(0)} = x_v \ \ \ \ \ \text{for all } v \in V
$$
- $h_v^{(0)}$ : Node $v$'s initial embedding
- $x_v$ : Node $v$'s original features

And for $k=1,2,..., K$, where $K$ is the total steps,
$$
h_v^{(k)} = f^{(k)} \Bigg( W^{(k)} \cdot \frac{\underset{u\in\mathcal{N}(v)}{\sum} h_u^{(k-1)}}{|\mathcal{N}(v)|} + B^{(k)} \cdot h_v^{(k-1)} \Bigg) \ \ \ \ \ \text{for all }v \in V
$$
- $h_v^{(k)}$ : Node $v$'s embedding at step $k$
- $\frac{\underset{u\in\mathcal{N}(v)}{\sum} h_u^{(k-1)}}{|\mathcal{N}(v)|}$ : Mean of $v$'s neighbours embeddings at step $k-1$
- $h_v^{(k-1)}$ : Node $v$'s embedding at step $k-1$.


Predictions can be made at each node using the final computed embedding:
$$
\hat{y_v} = \text{PREDICT}(h_v^{(K)})
$$
where $\text{PREDICT}$ is generally another neural network, learnt together with the GCN model.

For each step $k$, the function $f^{(k)}$, matrices $W^{(k)}$ and $B^{(k)}$ are shared across all nodes.
- This allows the GCN model to scale well, because the *number of parameters in the model is not tied to the size of the graph*.




---
# References

- [[CS224W - Machine Learning with Graphs#Chapter 4 A general perspective on GNNs]]
- [Understanding Convolutions on Graphs (distill.pub)](https://distill.pub/2021/understanding-gnns/)
- [A Gentle Introduction to Graph Neural Networks (distill.pub)](https://distill.pub/2021/gnn-intro/)