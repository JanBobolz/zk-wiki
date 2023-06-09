---
aliases: []
tags: []
created: 2023-06-07
---

For ZK proofs, we are broadly interested in three main properties: 
- Completeness/Correctness: if all parties follow the protocol and we use valid input, the verifier accepts.
- [[Soundness|Soundness / Proof of Knowledge]]: the prover cannot cheat (minimum: cannot create proofs for false statements).
- [[Zero Knowledge]]: the verifier cannot cheat (cannot learn anything about the witness).