---
tags:
  - causal-analytics
creation-date: 2024-05-01 12:25
---
# Stable Unit Treatment Value Assumption (SUTVA)

SUTVA is a key assumption in causal inference that ensures the **potential outcomes for a unit depend only on the treatment assigned to that unit, and not on the treatments assigned to other units**.

Specifically, SUTVA has two components:
1. **No interference between units**: The potential outcome of one unit does not depend on the treatment assigned to other units. This rules out spill-over or network effects where the treatment of one unit effects the outcomes of other units.
2. **No hidden variations of the treatment**: There is only one version of each treatment level, and the potential outcomes do not vary with the different versions of the treatment that might be available. This rules out the possibility thwat a unit could receive different "versions" of the same nominal treatment.

SUTVA is an important assumption because it allows researchers to identify causal effects by comparing the observed outcomes of treated and control units. If SUTVA is violated, the potential outcomes may depend on the treatment assignments of other units, making it impossible to isolate the causal effect of the treatment on each individual unit.

Violations of SUTVA can occur in the presence of general equilibrium effects, peer effects, externalities, or measurement error in the treatment or outcome. When SUTVA is violated, researchers may need to modify their study design, change the unit of analysis, or use alternative methods like bounds analysis to identify causal effects. Overall, SUTVA is a critical assumption in causal inference that must be carefully considered and justified in empirical research.


---
# References

[[Casual Inference - The Mixtape#Chapter 4 - Potential Outcomes Causal Model]]