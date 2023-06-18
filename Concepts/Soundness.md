---
title: "Soundness"
aliases: [sound]
tags: []
created: 2023-05-03
---

Soundness states that **the prover cannot make the verifier accept for false statements**. It is the most basic security requirement protecting honest verifiers. 

# Basic formal definition
For all $x\notin \mathcal{L}$ (i.e. there exists no $w$ s.t. $(x,w)\in\mathcal{R}$) and for all (ppt^[this notion also makes sense without the prover efficiency requirement]) provers $\mathcal{P}^*$, we have $$\Pr[\mathcal{P}^* \leftrightarrow \mathcal{V}(x) \rightarrow 1] \text{ is negligible}$$

## Definition alternatives
We can also imagine that the adversary $\mathcal{P}^*$ first outputs an $x$, then communicates with the verifier, then wins if the verifier accepts and $x\notin\mathcal{L}$. This variant is equivalent to the above, but can be stronger for proof systems with setup.

## Soundness error
TODO!

# Relations to other notions
- Soundness is implied by any [[Proof of Knowledge]] property.