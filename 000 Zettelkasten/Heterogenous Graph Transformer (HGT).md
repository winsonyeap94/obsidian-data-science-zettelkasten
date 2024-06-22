---
tags:
  - graphs
  - neural-networks
creation-date: 2024-06-22 15:07
---
## Heterogenous Graph Transformer (HGT)

![[Pasted image 20240622150947.png]]

HGTs make use of **node- and edge-type dependent attention mechanisms** to model [[Heterogenous Graphs|heterogenous graphs]].
- Instead of parameterising each type of edges (relations), the heterogenous mutual attention in HGT is defined by breaking down each edge $e=(s, t)$ based on its meta relation triplet, i.e., (node type of starting node $s$, edge type of $e$ between $s$ and $t$, and node type of ending node $t$).
- The idea is to use **meta relations** of heterogenous graphs to parameterise weight matrices for the heterogenous mutual attention, message passing, and propagation steps.

> [!note]- Relationship to Attention Mechanism in Transformers
> HGT uses the **Scaled Dot-Product Attention** (which was proposed in Transformer architecture)
> $$
> \text{Attention}(Q, K, V) = \text{softmax}\Big( \frac{QK^T}{\sqrt{d_k}} \Big) V
> $$

## HGT Architecture

The process HGT uses to aggregate information from $s$ to get a contextualised representation for target node $t$ can be decomposed into three components:
1. Heterogenous Mutual Attention
2. Heterogenous Message Passing
3. Target-Specific Aggregation

### (1) Heterogenous Mutual Attention

As a recap, general attention-based GNNs follow the approach below:
$$
H^{(l)}[t] \leftarrow \underset{\forall s\in N(t), \forall e \in E(s,t)}{\text{Aggregate}} \Big( \text{Attention}(s, t) \cdot \text{Message}(s) \Big)
$$
where there are three basic operators:
1. **Attention** - estimates the importance of each source node
2. **Message** - extracts the message by using only the source node $s$
3. **Aggregate** - aggregates the neighbourhood message by the attention weight

For example, the [[Graph Attention Networks (GAT)]] adopts an additive mechanism as Attention, uses the same weight for calculating Message, and leverages the simple average followed by a nonlinear activate for the Aggregate step.
$$
\begin{align}
\text{Attention}_{GAT}(s,t) & = \underset{\forall s \in N(t)}{\text{Softmax}}\bigg( \overrightarrow{a} \Big(WH^{(l-1)}[t] \mid \mid WH^{(l-1)}[s] \Big) \bigg) \\
\text{Message}_{GAT}(s) & = WH^{(l-1)}[s] \\ 
\text{Aggregate}_{GAT}(\cdot) & = \sigma \Big( \text{Mean}(\cdot) \Big)
\end{align}
$$
- Although GAT is effective to give high attention values to important nodes, it *assumes that $s$ and $t$ have the same feature distributions* by using one weight matrix $W$. This assumption is not valid for heterogenous graphs.

Heterogenous Mutual Attention aims to overcome this limitation of GATs.
- Given a target node $t$, and all its neighbours $s \in N(t)$, which might belong to different distributions, we want to calculate their mutual attention grounded by their meta relations.
- Taking inspiration from the Transformer architecture, we map the target node $t$ into a Query vector, and the source node $s$ into a Key vector, and calculate their dot product as attention.
- The key difference is that the vanilla Transformer uses a single set of projections for all words, whereas in HGT each meta relation should have a *distinct set of projection weights*.
$$
\begin{align}
\text{Attention}_{HGT}(s,e,t) & = \underset{\forall s \in N(t)}{\text{Softmax}}\bigg( \underset{\forall s \in N(t)}{\mid\mid} \text{ATT-head}^i(s,e,t) \bigg) \\
\text{ATT-head}^i(s,e,t) & = \bigg( K^i(s) W_{\phi(e)}^{ATT} Q^i(t)^T\bigg) \cdot \frac{\mu_{\langle \tau(s), \phi(e), \tau(t) \rangle}}{\sqrt{d}} \\
K^i(s) & = \text{K-Linear}^i_{\tau(s)}\Big( H^{(l-1)}[s] \Big) \\ 
Q^i(t) & = \text{Q-Linear}^i_{\tau(t)}\Big( H^{(l-1)}[t] \Big)
\end{align}
$$

### (2) Heterogenous Message Passing

> [!info] Heterogenous Message Passing is done in parallel to the calculation of Mutual Attention.

Similar to the attention process, the Heterogenous Message Passing step aims to incorporate meta relations of edges into the message passing process to *alleviate distribution differences of nodes and edges of different types*.

For a pair of nodes $e=(s,t)$, we calculate its multi-head Message by:
$$
\begin{align}
\text{Message}_{HGT}(s,e,t) & = \underset{i\in[1,h]}{\mid\mid}\text{MSG-head}^i(s,e,t) \\
\text{MSG-head}^i(s,e,t) & = \text{M-Linear}^i_{\tau(s)}\Big( H^{(l-1)}[s]\Big) W_{\phi(e)}^{MSG}
\end{align}
$$

### (3) Target-Specific Aggregation

Finally, with both the Heterogenous Mutual Attention and Message Passing calculated, we then aggregate them from the source nodes to the target node.

Note that the softmax procedure in Heterogenous Mutual Attention has made the sum of each target node $t$'s attention vectors to 1, we can thus simply use the attention vector as the weight to average the corresponding messages from the source nodes and get the updated vector $\tilde{H}^{(l)}[t]$ as:
$$
\tilde{H}^{(l)}[t] = \underset{\forall s \in N(t)}{\oplus}\Big(\text{Attention}_{HGT}(s,e,t) \cdot \text{Message}_{HGT}(s,e,t) \Big)
$$
- This aggregates information to the target node $t$ from all its neighbours (source nodes) of different feature distributions.

The final step is to *map the target node $t$'s vector back to its type-specific distribution*, index by its node type $\tau(t)$. 
- To do so, we apply a linear projection $\text{A-Linear}_{\tau(t)}$ to the updated vector $\tilde{H}^{(l)}[t]$, followed by a non-linear activation and residual connection.
$$
H^{(l)}[t] = \sigma \Big(\text{A-Linear}_{\tau(t)} \tilde{H}^{(l)}[t]\Big) + H^{(l-1)}[t]
$$




---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 7 Heterogenous Graphs]]
- Original Paper: https://arxiv.org/pdf/2003.01332
- https://dl.acm.org/doi/fullHtml/10.1145/3366423.3380027