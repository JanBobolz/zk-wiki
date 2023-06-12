---
aliases: [complete, correct]
tags: []
created: 2023-06-12
---

Basic idea: if $(x,w)\in\mathcal{R}$ and everyone follows the protocol, then the verifier will be convinced.

# Definition
Formally:
If $(x,w)\in\mathcal{R}$, then $$\Pr[\mathcal{P}(x,w) \leftrightarrow \mathcal{V}(x) \rightarrow 1] = 1$$
This property just makes sure that the protocol actually works day-to-day in the absence of any attacks. It serves as a nontriviality condition (e.g., a protocol that has the prover send nothing and the verifier just output $0$ every time would be considered [[Zero Knowledge|ZK]] and [[Soundness|sound]]).