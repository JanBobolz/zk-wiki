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
> A protocol is a *proof of knowledge with knowledge error $\kappa$* if there exists an extractor $E$ such that for all (malicious, [[Probabilistic Polynomial Time|ppt]]) provers $P^*$ and all $x\in\mathcal{L}$, $$\text{if }\Pr[P^*(x)\leftrightarrow V(x) \rightarrow 1] > \kappa(x)\text{, then } \Pr[(x, E^{P^*(x;r;\cdot)}(x)) \in \mathcal{R}] = 1$$
> and the extractor $E$ has expected running time at most $\frac{\mathit{poly}(|x|)}{\Pr[P^*(x)\leftrightarrow V(x) \rightarrow 1] - \kappa(x)}$

where $E^{P^*}$ denotes that $E$ gets [[Oracle Access]] to the function $\vec{m} \mapsto P^*(x;r;\vec{m})$ (which returns the next message $P^*$ sends after having received messages $\vec{m}$).

> [!todo] 
> Not sure that this is the best definition we can find for this. 
> Also, should specify what the probability for runtime and success is over.
> Also: non-1 success rate? (e.g., what if commitment is broken?).

> [!hint] Knowledge error
> The knowledge error $\kappa$ is usually the inverse of challenge space, $\kappa = 1/|C|$. Intuitively, that makes sense: if the prover guesses the challenge correctly, winning the protocol is not difficult anymore. Indeed, if the protocol is [[Zero Knowledge|ZK]], the prover would just run the honest verifier simulator and hope that the actual verifier happens to choose the same challenge). So it's trivial to set up a prover that can win the protocol with probability $\kappa$ without *any* knowledge of the witness, so we can never hope to extract anything from that prover. 
> For any prover that does better than $\kappa$, the above definition says we can extract (using runtime relative to *how much* better than $\kappa$ the prover is).

# A simplified toy definition
> [!todo]
> Simplified definition for provers that can answer challenges correctly with probability 1.

# Resources
- Bellare, Goldreich: [On Defining Proofs of Knowledge](https://www.wisdom.weizmann.ac.il/~oded/PSX/pok.pdf)