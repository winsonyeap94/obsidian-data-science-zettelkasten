---
tags:
  - "#graphs"
  - "#neural-networks"
creation-date: 2024-06-05 22:25
---
# GraphSAGE

![[Pasted image 20240605222630.png]]

GraphSAGE (**SA**mple and aggre**G**at**E**) is an **inductive** framework that leverages node attribute information to efficiently generate representations on previously unseen data.

It can be viewed as a stochastic generalisation of graph convolutions, and it is especially useful for massive, dynamic graphs that contain rich feature information.

>[!warning] GraphSAGE is intended for use on large graphs (>100,000 nodes). The overhead of subsampling will start to outweigh its benefits on smaller graphs.

Instead of training a distinct embedding vector for each node, we train a set of **aggregator functions** that learn to aggregate feature information from a node's local neighbourhood.
- Each aggregator function aggregates information from a different number of hops, or search depth, away from a given node.

## Algorithm

Initialisation:
$$
h_v^{(0)} = x_v \ \ \ \ \ \text{for all } v \in V
$$
- $h_v^{(0)}$ : Node $v$'s initial embedding
- $x_v$ : Node $v$'s original features

And for $k=1,2,..., K$, where $K$ is the total steps,
$$
h_v^{(k)} = f^{(k)} \Bigg( W^{(k)} \cdot \bigg[ \underset{u\in\mathcal{N}(v)}{\text{AGG}}\Big( \Big\{ h_u^{(k-1)} \Big\} \Big)\ , \ h_v^{(k-1)} \bigg] \Bigg) \ \ \ \ \ \text{for all }v \in V
$$
- $h_v^{(k)}$ : Node $v$'s embedding at step $k$
- $\underset{u\in\mathcal{N}(v)}{\text{AGG}}\Big( \Big\{ h_u^{(k-1)} \Big\} \Big)$ : Aggregation of $v$'s neighbour's embeddings at step $k-1$, which is then concatenated with...
- $h_v^{(k-1)}$ : Node $v$'s embedding at step $k-1$.


Predictions can be made at each node using the final computed embedding:
$$
\hat{y_v} = \text{PREDICT}(h_v^{(K)})
$$
where $\text{PREDICT}$ is generally another neural network, learnt together with the GraphSAGE model.

For each step $k$, the function $f^{(k)}$, matrices $W^{(k)}$ and $B^{(k)}$ are shared across all nodes.
- This allows the GCN model to scale well, because the *number of parameters in the model is not tied to the size of the graph*.

The original GraphSAGE paper considers the following choices for aggregation function:
1. **Mean** (similar to [[Graph Convolutional Networks (GCN)]])
$$
W_{\text{pool}}^{(k)} \cdot \frac{h_v^{(k-1)} + \underset{u\in\mathcal{N}(v)}{\sum} h_u^{(k-1)}}{1 + |\mathcal{N}(v)|}
$$
2. **Pooling** : Dimension-wise Maximum or Mean
$$
\underset{u\in\mathcal{N}(v)}{\max}\bigg\{ \sigma\big( W_{\text{pool}}^{(k)} \cdot h_u^{(k-1)} + b \big) \bigg\}
$$
3. **LSTM** (after ordering the sequence of neighbours)
$$
\text{LSTM}\Big( \big[ h_u^{(k-1)} \big] \Big)
$$

Further, the original GraphSAGE paper performs **"neighbourhood sampling"**.
- Irrespective of how large the actual neighbourhood around a node is, a fixed-size random sample of the neighbourhood is taken.
- This increases the variance in the computed embeddings, but *allows GraphSAGE to be used on very large graphs*.


## L2 Normalisation

There is also an option to apply L2 Normalisation to $h_v^{(k)}$ at every layer.
$$
h_v^{(k)} \leftarrow \frac{h_v^{(k)}}{|| h_v^{(k)} ||_2} \ \ \ \forall\ v \in V\ \ \text{where }||u||_2 = \sqrt{\sum_i u_i^2} \ \ \ \text{(L2 norm)}
$$

Without L2 normalisation, the embedding vectors have different scales for vectors.
- In some cases (not always), normalisation of embedding results in performance improvement.
- After L2 normalisation, all vectors will have the same L2 norm.

---
# References

- [[CS224W - Machine Learning with Graphs#Chapter 4 A general perspective on GNNs]]
- [Understanding Convolutions on Graphs (distill.pub)](https://distill.pub/2021/understanding-gnns/)
- [GraphSAGE (stanford.edu)](https://snap.stanford.edu/graphsage/)
- Original paper: [1706.02216 (arxiv.org)](https://arxiv.org/pdf/1706.02216)