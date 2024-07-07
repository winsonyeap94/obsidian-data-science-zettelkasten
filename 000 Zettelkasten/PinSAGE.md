---
tags:
  - graphs
  - neural-networks
  - recommender-systems
creation-date: 2024-07-07 21:27
---
## PinSAGE

>[!summary] PinSAGE uses GNNs to generate high-quality user/item embeddings that **capture both the rich node attributes and graph structure**.
>- The PinSAGE model is effectively trained using sophisticated **negative sampling strategies.**

![[Pasted image 20240707212951.png]]

Introduction to PinSAGE:
1. Unifies *visual*, *textual*, and *graph* information.
2. The largest industry deployment of a [[Graph Convolutional Networks (GCN)]].
3. Huge adoption across *Pinterest*.
4. <mark style="background: #FFF3A3A6;">Works for fresh content and is available in a few seconds after pin creation</mark>.

Goal:
- **Generate embeddings for nodes in a large-scale Pinterest graph** containing billions of objects.

Key Idea:
- **Borrow information from nearby nodes**.
- e.g., bed rail Pin might look like a garden fence, but gates and beds are rarely adjacent in the graph.![[Pasted image 20240707213253.png]]
- Pin embeddings are essential to various tasks like *recommendation of Pins*, *classification*, *ranking*

![[Pasted image 20240707213336.png]]


The PinSAGE algorithm <mark style="background: #FFF3A3A6;">further resolves embeddings across the Pinterest graph</mark>.
![[Pasted image 20240707213441.png]]
### Methods for Scaling Up

In addition to the GNN model, PinSAGE introduces several methods to scale the GNN to a billion-scale recommender system:
1. **Shared negative samples** across users in a mini-batch
2. **Hard negative samples**
3. **Curriculum learning**
4. **Mini-batch training** of GNNs on a large graph

#### (1) Shared Negative Samples

![[Pasted image 20240707213822.png]]

Recall that in [[GNNs for Recommender Systems#(2) Bayesian Personalised Ranking (BPR) Loss|BPR Loss]], for each user $u^* \in U_{\text{mini}}$, we sample one positive item $v_{pos}$ and a set of sampled negative items $V_{neg} = \{v_{neg}\}$.

**Using more negative samples per user** improves the recommendation performance, but is also expensive.
- We need to generate $|U_{mini}| \cdot |V_{neg}|$ embeddings for negative nodes.
- We need to apply $|U_{mini}| \cdot |V_{neg}|$ GNN computational graphs, which is expensive.

The key idea behind shared negative samples is that we can **share the same set of negative samples $V_{neg} = \{v_{neg}\}$ *across all users* $U_{mini}$** in the mini-batch.
- This way, we only need to generate $|V_{neg}|$ embeddings for the negative nodes.
- This saves the node embedding generation computation by a factor of $|U_{mini}|$.
- Empirically, the performance stays similar to the non-shared negative sampling scheme.

#### (2) Hard Negatives

Challenge: Industrial recommender systems need to make **extremely fine-grained predictions**.
- No. of total items : Up to billions
- No. of items to recommend for each user : 10 to 100

Issue: The shared negative items are randomly sampled from all items.
- Most of them are **"easy negatives"**, i.e., <mark style="background: #FFF3A3A6;">a model does not need to be fine-grained to distinguish them from positive items</mark>.
- Hence, we need a way to sample **"hard negatives"** to force the model to be fine-grained.

For each user node, the hard negatives are **item nodes that are close (but not connected) to the user node in the graph**.
- Hard negatives for a user $u \in U$ are obtained as follows:
	1. Compute random walks from user $u$.
		- Run *random walk with restart* from $u$, obtain *visit counts* for other items/nodes.
	2. Sort items in *the descending order of their visit count*.
	3. Randomly sample items that are ranked high but not too high, e.g., 2000th - 5000th.
		- Item nodes that are close but not too close (connected) to the user node.

The hard negatives for each user are used in addition to the shared negatives.

#### (3) Curriculum Learning

![[Pasted image 20240707214456.png]]

The idea with curriculum learning is to then **use harder and harder negative samples** after each epoch.
- Key Insight: It is effective to make the negative samples *gradually harder* in the training process.

At $n$-th epoch, we add $n-1$ hard negative items.
- No. of hard negatives gradually increases in the training process.
- With this, the model will gradually learn to make finer-grained predictions.


---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 11 GNNs for Recommenders]]