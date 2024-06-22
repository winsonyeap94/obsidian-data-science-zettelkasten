---
tags:
  - graphs
  - "#neural-networks"
creation-date: 2024-06-04 21:16
---
# Graph Neural Networks (GNN)

## Setup

Assume we have a graph $G$:
- $V$ is the *vertex set*
- $A$ is the *adjacency matrix* (assume binary)
- $X \in \mathbb{R}^{m \times |V|}$ is a matrix of *node features*
- $v$ is a node in $V$
- $N(v)$ is the set of neighbours $v$
- Node features:
	- Social networks: User profile, user image
	- Biological networks: Gene expression profiles, gene functional information
	- When there is no node feature in the graph dataset:
		- Indicator vectors (one-hot encoding of a node)
		- Vector of constant 1: $[1, 1, ..., 1]$

---
## Introduction to GNNs

![[Pasted image 20240604222726.png]]

The approach of graph neural networks can be thought of in two steps:
1. Determine the **node computation graph**.
2. **Propagate** and **transform information**.
	- This is similar to the [[Node Classification#Message Passing|message passing]] concept.

![[Pasted image 20240604223014.png]]
![[Pasted image 20240604222921.png]]
![[Pasted image 20240604222938.png]]


Model can be of **arbitrary depth**:
- Nodes have embeddings at each layer.
- Layer-0 embedding of node $u$ is its *input feature* $x_u$.
- Layer-$k$ embedding gets information from nodes that are *$K$-hop away*.

![[Pasted image 20240604223446.png]]

#### Neighbourhood Aggregation

There are different approaches to neighbourhood aggregation. The key distinctions are in how different approaches aggregate information across the layers.
- Basic approach: *Average information* from neighbours and apply a neural network.

![[Pasted image 20240604223637.png]]
![[Pasted image 20240604223658.png]]

#### Invariance and Equivariance

What are the invariance and equivariance properties for a GCN?
- Given a node, the GCN that computes its embedding is **permutation invariant**.
	- Taking the average of previous layer's embedding is a permutation invariant calculation, i.e., it is not affected by the order of calculation.

![[Pasted image 20240604224242.png]]

Consideration all nodes in a graph, GCN computation is **permutation equivariant**.
- Detailed reasoning:
	1. The rows of input node features and output embeddings are aligned.
	2. We know computing the embedding of a given node with GCN is invariant.
	3. So, after permutation, the location of a given node in the input node feature matrix is changed, and the output embedding of a given node stays the same (the colours of node feature and embedding are matched).
	4. This is permutation equivariant.

![[Pasted image 20240604224300.png]]


#### Training the model

![[Pasted image 20240604224451.png]]

We can feed these embeddings into any loss function and run SGD to train the weight parameters.

**Model parameters:**
1. $h_v^k$ : The hidden representation of node $v$ at layer $l$.
2. $W_k$ : Weight matrix for neighbourhood aggregation.
3. $B_k$ : Weight matrix for transforming hidden vector of self.

>[!note]- Matrix Formulation
>![[Pasted image 20240604224652.png]]
>![[Pasted image 20240604224703.png]]

There are two settings to train a GNN:
1. **Supervised setting**
	- We want to minimise loss $\mathcal{L}$:    $\underset{\theta}{\min} \mathcal{L} (y, f_\theta (z_v))$
	- $y$ : node label
	- $\mathcal{L}$ could be L2 if $y$ is real number, or cross entry of $y$ is categorical.
2. **Unsupervised setting**
	- No node label available.
	- Use the *graph structure* as the supervision.

>[!note]- Further explanation on how Unsupervised Training can be done
>![[Pasted image 20240604225319.png]]

---
## General Perspective on GNNs

### A General GNN Framework

![[Pasted image 20240604230139.png]]

1st Part (Layer 2): **GNN Layer = (1) Message + (2) Aggregation**
- There are different instantiations under this perspective.
- e.g., GCN, GraphSAGE, GAT, etc.

2nd Part (Layer 1): **(3) Layer Connectivity - Connect GNN Layers into a GNN**
- How do we connect the GNN layers together?
- Stack layers sequentially
- Ways of adding skip connections

3rd Part: **(4) Graph Augmentation - Idea: Raw input graph != Computational graph**
- Graph feature augmentation
- Graph structure manipulation

4th Part: **(5) Learning Objective - How do we train the GNN?**
- Supervised / Unsupervised objectives
- Node- / Edge- / Graph-level objectives

### A Single Layer of a GNN

![[Pasted image 20240604230949.png]]

The idea of a single GNN layer:
- Compress a set of vectors into a single vector.
- Two-step process: *(1) Message Computation and *(2) Message Aggregation*.

>[!note] Notice how the input nodes are a "set"; this highlights that the **ordering of the inputs are not important**.

##### (1) Message Computation

![[Pasted image 20240604231334.png]]

Message Function : $\mathbf{m}_u^{(l)} = \text{MSG}^{(l)}\Big( \mathbf{h}_u^{(l-1)} \Big)$
- Intuition: Each node will create a message, which will be sent to other nodes later.
- Example: A linear layer $\mathbf{m}_u^{(l)} = \mathbf{W}^{(l)}\mathbf{h}_u^{(l-1)}$
	- Multiply node features with weight matrix $\mathbf{W}^{(l)}$

##### (2) Message Aggregation

![[Pasted image 20240604231627.png]]

- Intuition: Node $v$ will aggregate the messages from its neighbours $u$:
$$
\mathbf{h}_v^{(l)} = \text{AGG}\Big( \big\{ \mathbf{m}_u^{(l)}, u \in N(v) \big\} \Big)
$$
- Example: $\text{Sum}(\cdot)$, $\text{Mean}(\cdot)$, or $\text{Max}(\cdot)$ aggregator
	- $\mathbf{h}_v^{(l)} = \text{Sum}\big( \{ \mathbf{m}_u^{(l)}, u \in N(v) \} \big)$

Issue with message aggregation:
- **Information from node $v$ itself could get lost**
	- Computation of $\mathbf{h}_v^{(l)}$ does not directly depend on $\mathbf{h}_v^{(l-1)}$
- **Solution: Include $\mathbf{h}_v^{(l-1)}$ when computing $\mathbf{h}_v^{(l)}$.**
	- (1) Message: Compute message from node $v$ itself.
		- Usually, a *different message computation* will be performed. ![[Pasted image 20240604231900.png]]
	- (2) Aggregation: After aggregating from neighbours, we can aggregate the message from node $v$ itself.
		- Via *concatenation* or *summation*.![[Pasted image 20240604231923.png]]

##### Putting Things Together

- **(1) Message**: Each node computes a message
$$
\mathbf{m}_u^{(l)} = \text{MSG}^{(l)}\Big( \mathbf{h}_u^{(l-1)} \Big) \ \ , \ \ u \in \{N(v) \cup v\}
$$
- **(2) Aggregation**: Aggregate messages from neighbours
$$
\mathbf{h}_v^{(l)} = \text{AGG}^{(l)}\Big( \big\{ \mathbf{m}_u^{(l)}\ , \ u \in N(v) \big\} \ , \ \mathbf{m}_v^{(l)} \Big)
$$
- **Non-linearity (activation**: Adds expressiveness
	- Often written as $\sigma(\cdot)$
	- Examples: ReLU, Sigmoid, etc.
	- Can be added to *message* or *aggregation*.

---
## Variations of GNNs

1. [[Graph Convolutional Networks (GCN)]]
2. [[GraphSAGE]]
3. [[Graph Attention Networks (GAT)]]

---
## Incorporating Modern Deep Learning Techniques

Many modern deep learning techniques can be incorporated into a GNN layer.

1. Batch Normalisation - Stabilise neural network training
2. Dropout - Prevent overfitting
	- In GNN, dropout is applied to the *linear layer* in the message function.
3. Attention / Gating - Control the importance of a message
4. Activation functions

---
## Stacking GNN Layers

![[Pasted image 20240605232135.png]]

The simplest way of stacking GNN layers is simply by stacking GNN layers on top of one another **sequentially**.

### Over-smoothing Problem

However, there is a problem if we stack too many GNN layers on top of one another sequentially. The GNN will suffer from the over-smoothing problem.
- This means that all the **node embeddings converge to the same value**.
- This is bad because we want to use node embeddings to differentiate nodes.

To understand why over-smoothing happens, we need to understand the concept of **receptive field**.
- Receptive field is the *set of nodes that determine the embedding of a node of interest*.
- In a $K$-layer GNN, each node has a receptive field of $K$-hop neighbourhood.
	- The shared neighbours quickly grows when we increase the number of hops (number of GNN layers).

![[Pasted image 20240605232355.png]]

### Designing GNN Layer Connectivity

Knowing that over-smoothing can be a problem, we have several techniques to solve it.

#### (1) Lesson 1: Be cautious when adding GNN layers

-  Unlike neural networks in other domains, adding more GNN layers does not always help.
- Step 1: *Analyse the necessary receptive field* to solve our problem (e.g., by computing the diameter of the graph).
- Step 2: Set number of GNN layers to be a bit more than the receptive field we like.

**Solution 1: Increase the expressive power within each GNN layer**
![[Pasted image 20240605233052.png]]
- We can make aggregation / transformation as a *deep neural network* (e.g., 3-layer MLP).

**Solution 2: Add layers that do not pass messages**

![[Pasted image 20240605233035.png]]

- A GNN does not necessarily only contain GNN layers.
	- e.g., we can add *MLP layers* (applied to each node) before and after GNN layers, as *pre-process* layers and *post-process* layers.
		- **Pre-processing layers:** Important when *encoding node features* is necessary (e.g., when node represents images/text)
		- **Post-processing layers:** Important when *reasoning / transformation over node embeddings* are needed (e.g., graph classification, knowledge graphs)
	- In practice, adding these layers work great!

#### (2) Lesson 2: Add skip connections in GNNs

![[Pasted image 20240605233235.png]]

What if my problem still requires many GNN layers?
- Observation from over-smoothing: Node embeddings in earlier GNN layers can sometimes better differentiate nodes.
- Solution: We can *increase the impact of earlier layers* on the final node embeddings by **adding shortcuts (skip connections) in GNN**.
- Intuition: Skip connections create a **mixture of models**
	- $N$ skip connections $\rightarrow$ $2^N$ possible paths
	- Each path could have up to $N$ modules

![[Pasted image 20240605233340.png]]
![[Pasted image 20240605233359.png]]
![[Pasted image 20240605233412.png]]


---
# References

- [[CS224W - Machine Learning with Graphs#Chapter 3 Graph Neural Networks]]
- [[CS224W - Machine Learning with Graphs#Chapter 4 A general perspective on GNNs]]