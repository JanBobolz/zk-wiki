---
aliases: []
tags: []
created: 2023-06-18
---

# Setting
- Interactive proofs
- Standalone setting, i.e. no [[Random Oracle|ROM]], no [[Reference String|CRS]] or anything like that.

# Definition 
> [!definition] Proof of knowledge
> A protocol is a *proof of knowledge with knowledge error $\kappa$* if there exists an extractor $E$ such that for all (malicious, [[Probabilistic Polynomial Time|ppt]]) provers $P^*$ and all $x\in\mathcal{L}$, $$\text{if }\Pr[P^*(x)\leftrightarrow V(x) \rightarrow 1] > \kappa(x)\text{, then } (x, E^{P^*(x;r;\cdot)}(x)) \in \mathcal{R}$$
> and the extractor $E$ has expected running time at most $\frac{\mathit{poly}(|x|)}{\Pr[P^*(x)\leftrightarrow V(x) \rightarrow 1] - \kappa(x)}$

where $E^{P^*}$ denotes that $E$ gets [[Oracle Access]] to the function $\vec{m} \mapsto P^*(x;r;\vec{m})$ (which returns the next message $P^*$ sends after having received messages $\vec{m}$).

> [!todo] 
> Not sure that this is the best definition we can find for this. 

# Resources
- Bellare, Goldreich: [On Defining Proofs of Knowledge](https://www.wisdom.weizmann.ac.il/~oded/PSX/pok.pdf)