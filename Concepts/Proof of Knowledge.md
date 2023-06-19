---
aliases: [PoK]
tags: []
created: 2023-06-12
---

The proof of knowledge property states that in order to make the verifier $\mathcal{V}(x)$ accept, the (malicious) prover needs to *know* a witness $w$ for $x$.

# Philosophy
How do we model that a machine $P$ *knows* a value $w$? By saying that there must be a machine $E$ (the *extractor*) that can *extract* the value from $P$. The details of how $E$ can extract from $P$ depend on the setting, see details below.

# Formal definitions
## Interactive proofs
- [[Proof of Knowledge (Classical)|Classical definition]]

> [!hint] Advantage of the extractor over the verifier in classical interactive proofs. 
> The extractor gets black-box access to the prover, allowing the extractor to effectively [[Rewinding|rewind]] the prover. In contrast, the actual verifier cannot rewind the prover (which in many protocols simply translates to: the verifier only gets to see one response for a challenge, while the extractor can rewind and see multiple responses for the same challenge).