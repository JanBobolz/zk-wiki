---
aliases: []
tags: []
created: 2023-06-07
---

# The typical setting of a (ZK) proof
At the center of a ZK proof is a statement of the form "$(x,w)$ has some property" (e.g., "$x$ is a boolean formula and $x(w) = 1$" or "$w$ is the secret key corresponding to the public key $x$"). 
We have a **prover** $\mathcal{P}(x,w)$, who knows both the public *statement* $x$ and secret *witness* $w$^[When looking at ZK proofs information-theoretically, see [[01 History and Basics]] and [[Interactive Protocol|IP]], the prover is not constrained to polynomial time, so we don't need to pass a witness $w$. The prover can compute a witness itself, if needed. (Bonus: this allows us to talk about languages that may not even *have* witnesses, which is good because $\mathbf{IP} = \mathbf{PSPACE}$. However, for *practical* purposes, ZK proofs are for $\mathrm{NP}$ languages and hence *have* witnesses)], and a **verifier** $\mathcal{V}(x)$ who only knows the public statement $x$ (but not the secret $w$). 

> [!info] The magic of ZK 🪄
> The **goal** of the prover is to **convince** the verifier that there *is* some $w$ such that $(x,w)$ has the desired property **without revealing $w$**. 

For example, this would enable the prover to prove that $x$ is a satisfying boolean formula without revealing its satisfying assignment $w$, or to prove possession of her secret key secret key $w$ corresponding to the public key $x$.

Our goal is to design protocols/algorithms that enable the prover to do this.

## Languages, NP relations, etc.
- **Relation**: We call the set of all $(x,w)$ that *have* the desired property the *relation* $\mathcal{R}$ (
	- Example: $\mathcal{R}_\mathrm{SAT} = \{(x,w) \mid x \text{ is a boolean formula and } x(w) = 1\}$. 
	- Requirement: given $(x,w)$, one can *efficiently* check that $(x,w)\in \mathcal{R}$.
	- Typical: given only some $x$, it's generally difficult to compute a fitting $w$.
- **Language**: The set of all $x$ for which there *exists* a fitting $w$, i.e. $\mathcal{L} = \{x \mid \exists w:\ (x,w) \in \mathcal{R}\}$. 
	- Example: $\mathcal{L}_\mathrm{SAT} = \{x \mid x\text{ is a satisfiable boolean formula}\}$.

## Completeness/Correctness
[[Completeness]] means: If prover $\mathcal{P}$ and verifier $\mathcal{V}$ follow the protocol honestly, then the verifier accepts. 

# Security notions

## Main notions
For ZK proofs, we are broadly interested in two main properties: 
- [[Soundness|Soundness / Proof of Knowledge]]: the prover cannot cheat (minimum: cannot create proofs for false statements).
- [[Zero Knowledge]]: the verifier cannot cheat (cannot learn anything about the witness).
For these properties, there is a huge diversity of concrete definitions, suitable for different scenarios/constructions.

## Honorable mentions
- [[Witness Indistinguishability]]: a weaker version of security against cheating verifiers, which states that the verifier cannot distinguish a prover using valid witness $w_1$ from a prover using some other valid witness $w_2$.