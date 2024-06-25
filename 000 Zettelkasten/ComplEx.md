---
tags:
  - graphs
  - knowledge-graphs
creation-date: 2024-06-23 21:03
---
## ComplEx

ComplEx builds upon [[DistMult]] by leveraging the **complex vector space** for embedding entities and relations.
- In practice, we represent the real and imaginary components of these vectors separately.
![[Pasted image 20240623221919.png]]

## Algorithm

ComplEx models entities and relations using vectors in $\mathbb{C}^k$.

![[Pasted image 20240623222042.png]]

![[Pasted image 20240623222108.png]]

>[!info]- ComplEx Score Function Derivation
>![[Pasted image 20240623222146.png]]

---
## Relationship Patterns in ComplEx

### Relationships that ComplEx is able to model

1. Anti-Symmetric Relations
	- The model is expressive enough to learn:
		- High $f_r(h,t) = \text{Re}(\sum_i \mathbf{h}_i \cdot \mathbf{r}_i \cdot \mathbf{\bar{t}}_i)$
		- Low $f_r(t ,h) = \text{Re}(\sum_i \mathbf{t}_i \cdot \mathbf{r}_i \cdot \mathbf{\bar{h}}_i)$
		Due to the asymmetric modelling using complex conjugate.

2. Symmetric Relations
	- When $\text{Im}(\mathbf{r}) = 0$, we have$$
	  \begin{align}
	  f_r(h, t) & = \text{Re}(\sum_i \mathbf{h}_i \cdot \mathbf{r}_i \mathbf{\bar{t}}_i) \\
	  & = \sum_i \text{Re}(\mathbf{r}_i \cdot \mathbf{h}_i \cdot \mathbf{\bar{t}}_i) \\ 
	  & = \sum_i \mathbf{r}_i \cdot \text{Re}(\mathbf{h}_i \cdot \mathbf{\bar{t}}_i) \\ 
	  & = \sum_i \mathbf{r}_i \cdot \text{Re}(\mathbf{\bar{h}}_i \cdot \mathbf{t}_i) \\ 
	  & = \sum_i \text{Re}(\mathbf{r}_i \cdot \mathbf{\bar{h}}_i \cdot \mathbf{t}_i) \\
	  & = f_r(t,h)
	  \end{align}
	  $$

3. Inverse Relations
	- $\mathbf{r}_1 = \mathbf{\bar{r}}_2$
	- Complex conjugate of 
	  $\mathbf{r}_2 = \underset{r}{\text{argmax}}\ \text{Re}(\langle \mathbf{h}, \mathbf{r}, \mathbf{\bar{t}} \rangle)$ is exactly
	  $\mathbf{r}_1 = \text{argmax}\ \text{Re}(\langle\mathbf{t}, \mathbf{r}, \mathbf{\bar{h}}\rangle)$

4. Composition Relations

5. 1-to-N Relations



---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 8 Knowledge Graphs]]
- [Introducing DistMult and ComplEx for PyTorch Geometric | by David Kuo | Stanford CS224W GraphML Tutorials | Medium](https://medium.com/stanford-cs224w/introducing-distmult-and-complex-for-pytorch-geometric-6f40974223d0)