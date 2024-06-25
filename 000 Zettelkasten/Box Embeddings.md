---
tags:
  - graphs
  - knowledge-graphs
creation-date: 2024-06-24 22:35
---
## Box Embeddings

![[Pasted image 20240624223616.png]]

The idea of box embeddings is to embed queries with **hyper-rectangles (boxes)**.
$$
\mathbf{q} = (\text{Center}(q), \ \text{Offset}(q))
$$

The key insight of this idea is that *intersection of boxes is well-defined*. And boxes are a powerful abstraction, as we can <mark style="background: #FFF3A3A6;">project the centre and control the offset to model the set of entities enclosed in the box</mark>.

Things to figure out with box embedding:
1. **Entity embeddings** (# params: $d|V|$):
	- Entities are seen as zero-volume boxes.
	- $d$ : Out-degree
	- $|V|$ : # Entities
2. **Relation embeddings** (# params: $2d|R|$):
	- Each relation takes a box and produces a new box.
	- $|R|$ : # Relations
3. **Intersection operator** $f$:
	- Models the intersection of boxes, where the inputs are boxes and output is a box.

---
## Projection Operator $\mathcal{P}$

![[Pasted image 20240624224313.png]]

The projection operator $\mathcal{P}$ takes the current box as input and uses the **relation embedding** to **project and expand** the box.
$$
\mathcal{P} : \text{Box} \times \text{Relation} \rightarrow \text{Box}
$$
$$
\begin{align}
\text{Centre}(q') & = \text{Centre}(q) + \text{Centre}(r) \\
\text{Offset}(q') & = \text{Offset}(q) + \text{Offset}(r)
\end{align}
$$

>[!example]- Example of chaining projection operators
>![[Pasted image 20240624224354.png]]
>![[Pasted image 20240624224402.png]]
>![[Pasted image 20240624224414.png]]

---

## Intersection Operator $\mathcal{I}$

![[Pasted image 20240624224931.png]]

The intersection operator $\mathcal{I}$ takes multiple boxes as input and produces the **intersection box**. The intuition is that:
- The centre of the new box should be **"close"** to the centres of the input boxes.
- The offset (box size) should **shrink** (since the size of the intersected set is *smaller* than the size of all the input set).
$$
\mathcal{I} : \text{Box} \times ... \times \text{Box} \rightarrow \text{Box}
$$

### Geometric Intersection Operator

**Centre Calculation:**
$$
\begin{align}
\text{Centre}(q_{intersection}) & = \sum_i \mathbf{w}_i \odot \text{Centre}(q_i) \\
\mathbf{w}_i & = \frac{\exp(f_{cen}(\text{Centre}(q_i)))}{\sum_j \exp(f_{cen}(\text{Centre}(q_j)))}
\end{align}
$$
![[Pasted image 20240624225200.png]]

**Offset Calculation:**
The intuition is that the offset should be *smaller* than the offset of the input box.

Implementation: We first take the *minimum of the offset of the input box*, and then we make the model more expressive by introducing $f_{offset}$ to extract the *representation* of the input boxes with a sigmoid function to guarantee shrinking.

$$
\text{Offset}(q_{intersection}) = \min(\text{Offset}(q_1), ..., \text{Offset}(q_n)) \odot \sigma(f_{offset}(\text{Offset}(q_1), ..., \text{Offset}(q_n)))
$$
- The first term $\min(...)$ helps *guarantee shrinking*.
- $\sigma$ is the sigmoid function which helps squash outputs to $(0, 1)$.
- $f_{offset}$ is a neural network (with trainable parameters) that extracts the representation of the input boxes to increase expressiveness.

>[!example]- Example of intersection operator
>![[Pasted image 20240624225712.png]]

---
## Entity-to-Box Distance

In order to learn our optimal box embedding parameters, we need a score function. One such method is by calculating the entity-to-box distance, which is measured by the score function $f_q(v)$:
$$
f_q(v) : d_{box}(\mathbf{q}, \mathbf{v}) = d_{out}(\mathbf{q}, \mathbf{v}) + \alpha \cdot d_{in}(\mathbf{q}, \mathbf{v})
$$
where $0 < \alpha < 1$.
- Note that $f_q(v)$ captures the **inverse distance** of a node $v$ as answer to question $q$.

![[Pasted image 20240625220159.png]]


### Extending to Union Operation (Embedding AND-OR Queries)

The idea here is to extend our concept so that we can embed complex queries using the **union** operator.
- e.g., "What drug can treat breast cancer *or* lung cancer?"

**Conjunctive queries + disjunction** is called **Existential Positive First-Order (EPFO)** queries. 
- Also referred to as **AND-OR queries**.

The question is then how can we design a disjunction operator and embed AND-OR queries in low-dimensional vector space?
- Unfortunately not, because allowing union over arbitrary queries requires high-dimensional embeddings.
- In the two examples below, we will see that Example 1 is OK, as 3 points will still work fine in 2-dimensions. However, it becomes a problem when we have >3 points as illustrated in Example 2.
- The conclusion is that given any $M$ conjunctive queries ($q_1, ..., q_M$) with non-overlapping answers, we need dimensionality of $\Theta(M)$ to handle all OR queries.

![[Pasted image 20240625220636.png]]
![[Pasted image 20240625220530.png]]

#### Disjunctive Normal Form

A workaround is to take all unions out and *only do union at the last step*.

![[Pasted image 20240625220751.png]]

Any AND-OR query can be transformed into an equivalent **DNF**, i.e., **disjunction of conjunctive queries**.

Given any AND-OR query $q$,
$$
q = q_1 \lor\ q_2\ \lor\ ...\ \lor\ q_m
$$
where $q_i$ is a *conjunctive query*.

With this, we can first embed each $q_i$ and then "aggregate" at the last step.

### Distance Between $q$ and an Entity

Distance between entity embedding and a DNF $q = q_1 \lor q_2 \lor ... \lor q_m$ is defined as:
$$
d_{box}(\mathbf{q}, \mathbf{v}) = \min(d_{box}(\mathbf{q}_1, \mathbf{v}), ..., d_{box}(\mathbf{q}_m, \mathbf{v}))
$$

Intuition:
- As long as $v$ is the answer to one conjunctive query $q_i$, then $v$ should be the answer to $q$.
- As long as $\mathbf{v}$ is close to one conjunctive query $\mathbf{q}_i$, then $\mathbf{v}$ should be close to $\mathbf{q}$ in the embedding space.

The process of embedding any AND-OR query $q$:
1. Transform $q$ to **equivalent DNF** $q_1 \lor ... \lor q_m$.
2. **Embed** $q_1$ to $q_m$.
3. Calculate the box distance $d_{box}(\mathbf{q}_i, \mathbf{v})$.
4. Take the **minimum** of all distance.
5. Calculate the final score $f_q(v) = -d_{box}(\mathbf{q}, \mathbf{v})$.

>[!info] For a more detailed example, refer to [[Query2Box]]

---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 9 Reasoning over knowledge graphs]]