---
tags:
  - causal-analytics
creation-date: 2024-05-15 23:05
---
# Unobserved Confounding

The "No Unobserved Confounding" is unrealistic, hence it is worth studying ways to minimise the effect of unobserved confounders.

![[Pasted image 20240515230552.png]]

When we assume unconfoundedness, then we are focusing on identifying a point. However, as we look into unobserved confounding, we have to make *weaker assumptions*.
- The trade-off is that we will result in less confidence (i.e., estimating *intervals*, instead of making point estimates).

---
## Bounds

![[Pasted image 20240515231050.png]]
![[Pasted image 20240515231208.png]]

### No-assumptions bound

![[Pasted image 20240515231325.png]]
![[Pasted image 20240515231404.png]]
![[Pasted image 20240515232042.png]]

### Monotone treatment response

In this case, we **assume treatment always helps**. This means that every ITE is non-negative.
- This can also be flipped around so that ITE is always non-positive.

![[Pasted image 20240515231713.png]]
![[Pasted image 20240515231808.png]]

### Monotone treatment selection

![[Pasted image 20240515231916.png]]
![[Pasted image 20240515231959.png]]

### Optimal treatment selection

We assume that **individuals always receive the treatment that is best for them**.

![[Pasted image 20240515232232.png]]
![[Pasted image 20240515232306.png]]
![[Pasted image 20240515232334.png]]
![[Pasted image 20240515232357.png]]
![[Pasted image 20240515232538.png]]
![[Pasted image 20240515232556.png]]
![[Pasted image 20240515232609.png]]
![[Pasted image 20240515232645.png]]


---
## Sensitivity Analysis

### Linear Single Confounder

![[Pasted image 20240515232840.png]]
![[Pasted image 20240515232940.png]]
![[Pasted image 20240515233159.png]]

### Towards more general settings

![[Pasted image 20240515233345.png]]





---
# References

- [[Casual Inference by Brady Neal#Chapter 7 Unobserved Confounding; Bounds; Sensitivity Analysis]]