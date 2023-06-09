---
aliases: []
tags: []
created: 2023-06-18
---

# Setting
- Interactive proofs
- Standalone setting, i.e. no [[Random Oracle|ROM]], no [[Reference String|CRS]] or anything like that.

# Definition 
> [!definition] Definition: Proof of knowledge
> A protocol is a *proof of knowledge with knowledge error $\kappa$* if there exists an extractor $E$ such that for all (malicious, [[Probabilistic Polynomial Time|ppt]]) provers $P^*$ and all $x\in\mathcal{L}$, $$\text{if }\Pr[P^*(x)\leftrightarrow V(x) \rightarrow 1] > \kappa(x)\text{, then } \Pr[(x, E^{P^*}(x)) \in \mathcal{R}] = 1$$
> and the extractor $E$ has expected running time at most $\frac{\mathit{poly}(|x|)}{\Pr[P^*(x)\leftrightarrow V(x) \rightarrow 1] - \kappa(x)}$

where $E^{P^*}$ denotes that $E$ gets [[Oracle Access]] to the function $(x;r;\vec{m}) \mapsto P^*(x;r;\vec{m})$ (which returns the next message $P^*$ sends when run with input $x$, randomness $r$, and after having received messages $\vec{m}$).

> [!todo] 
> Not sure that this is the best definition we can find for this. 
> Also, should specify what the probability for runtime and success is over.
> Also: non-1 success rate? (e.g., what if commitment is broken?).

> [!hint] Knowledge error
> The knowledge error $\kappa$ is usually the inverse of challenge space, $\kappa = 1/|C|$. Intuitively, that makes sense: if the prover guesses the challenge correctly, winning the protocol is not difficult anymore. Indeed, if the protocol is [[Zero Knowledge|ZK]], the prover would just run the honest verifier simulator and hope that the actual verifier happens to choose the same challenge). So it's trivial to set up a prover that can win the protocol with probability $\kappa$ without *any* knowledge of the witness, so we can never hope to extract anything from that prover. 
> For any prover that does better than $\kappa$, the above definition says we can extract (using runtime relative to *how much* better than $\kappa$ the prover is).

# A simplified toy definition
The following definition is not really useful in practice, but allows for simplified proofs. For this definition, we assume for simplicity that the prover has probability 1 to convince the honest verifier.

> [!definition] Definition: toy definition of proof of knowledge
> A protocol is a *proof of knowledge against perfect provers* if there exists a [[Probabilistic Polynomial Time|ppt]] extractor $E$ such that for all (malicious, [[Probabilistic Polynomial Time|ppt]]) provers $P^*$ and all $x$, $$\text{if }\Pr[P^*(x)\leftrightarrow V(x) \rightarrow 1] = 1\text{, then } \Pr[(x, E^{P^*}(x)) \in \mathcal{R}] = 1$$

> [!warning] Warning: this definition does not actually give any useful guarantees in practice, it’s just a simplification so that we can illustrate some security proofs.

# Typical extractor outline
Consider a protocol of the following form (e.g., [[Graph Isomorphism Protocol]], [[Quadratic Residue Protocol]], [[3-Colorable Protocol]])

> [!protocol] Protocol template
> - **Announcement**: prover sends some random value $a$
> - **Challenge**: The verifier chooses a random challenge $c \gets C$ (from a small challenge space $C$) 
> - **Response**: The prover sends some response $z$
> - The verifier verifies some equation.

The extractor $E^{P^*}(x)$ for (malicious) prover $P^*$ *typically* works in two steps:
1. Find two accepting transcripts, $(a,c,z)$, $(a,c',z')$ with $c\neq c'$ (but the same announcement $a$) by feeding the prover with different inputs. 
2. Compute a witness from the two transcripts (cf. [[Special Soundness]]).

Concretely:
> [!algorithm] Extractor $E^{P^*}(x)$
> 1. $E$ tries to find two accepting transcripts $(a,c,z), (a,c',z')$:
> 	- $E$ chooses randomness $r$ for $P^*$
> 	- $E$ queries $P^*(x;r)$ to receive the prover's announcement $a$
> 	- $E$ queries $P^*(x;r;c)$ for random challenges $c\gets C$ to receive a responses $z_c$, and hopes that in that process, it eventually finds $c,c'$ such that $z_c,z_{c'}$ are both accepting responses according to the honest verifier algorithm.
> 	- After a while, $E$ may give up and try a different $r$ (the details are tedious).
> 2. $E$ computes $w$ from $(a,c,z),(a,c',z')$ 
> 	- This step highly depends on the concrete protocol.

If we're in the [[#A simplified toy definition]] scenario, then the extractor only ever checks two challenges $c,c'$ and both of them will yield accepting responses. 
In other settings, intuitively, 
For a prover that may not answer all challenges correctly *always* (but convinces the verifier with probability $> \kappa$), then $E$ may have to search for accepting responses longer. 

# Resources
- Bellare, Goldreich: [On Defining Proofs of Knowledge](https://www.wisdom.weizmann.ac.il/~oded/PSX/pok.pdf)