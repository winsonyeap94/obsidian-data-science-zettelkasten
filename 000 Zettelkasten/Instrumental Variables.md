---
tags:
  - causal-analytics
creation-date: 2024-05-16 20:48
---
# Instrumental Variables

![[Pasted image 20240516205515.png]]

Instrumental variables allows us to use the variable ($Z$) to **isolate the causal association flowing from $T$ to $Y$.**
- The intuition is that changes in $Z$ will be reflected in $T$ and lead to corresponding changes in $Y$. And these specifically $Z$-focused changes are unconfounded (unlike the changes to $T$ induced by the unobserved confounder $U$), so they allow us to isolate the causal association that flows from $T$ to $Y$.

## What is an Instrument?

There are a few assumptions that a variable must satisfy in order to be considered an instrumental variable.

**Assumption 1: Relevance**
- $Z$ has a causal effect on $T$

![[Pasted image 20240516205241.png]]

**Assumption 2: Exclusion Restriction**
- The causal effect of $Z$ on $Y$ is fully mediated by $T$.
- In the example below, in order for $Z$ to be considered an instrument, the edge in red must be excluded.

![[Pasted image 20240516205300.png]]

**Assumption 3: Instrumental Unfoundedness**
- The effect of $Z$ on $Y$ is unconfounded (no unblockable backdoor paths to $Y$)
- In the example below, in order for $Z$ to be considered an instrument, the edge in red must be excluded.
- There is a slightly weaker version of Assumption 3, where unconfoundedness is achieved after conditioning on observed variables. This is called **conditional instruments**.

![[Pasted image 20240516205205.png]]

## No Non-parametric Identification of the ATE

*"If instrumental variables allow us to identify causal effects, then why don't we just use them?"*

The reason is because instrumental variables *don't non-parametrically identify the causal effect*. 
- With instrumental variables, we must make assumptions about the parametric form (e.g., linear) to identify causal effects.

### Example: Linear Setting

$$
Y := \delta T + \alpha_u U
$$
![[Pasted image 20240516210056.png]]
![[Pasted image 20240516210109.png]]
![[Pasted image 20240516210206.png]]

## Non-parametric Identification of the Local ATE

### Principal Strata

We break the data into 4 strata (groups) based on how the instrument affects the treatment they make.
- Compliers       : $T(Z=1)=1$, $T(Z=0)=0$
- Defiers            : $T(Z=1)=0$, $T(Z=0)=1$
- Always-takers : $T(Z=1) = 1$, $T(Z=0)=1$
- Never-takers   : $T(Z=1) = 0$, $T(Z=0)=0$

However, if we only know one of the treatment given a $Z$ value, we can only narrow down it into 2 of the 4 groups.
- One technique is to be aware that the "Always-takers" and "Never-takers" group actually do not form an edge from $Z \rightarrow T$, since they will always or never take the treatment, regardless of $Z$.

### Monotonicity Assumption (No Defiers)

And with the **monotonicity assumption (no defiers)**, we eliminate the "Defiers" group.

![[Pasted image 20240516210855.png]]

### Local ATE (LATE) Identification

With that, we can then now derive the **Local ATE** identification.

![[Pasted image 20240516211209.png]]
![[Pasted image 20240516211257.png]]

## More general settings for the ATE





---
# References

- [[Casual Inference by Brady Neal#Chapter 8 Instrumental Variables]]