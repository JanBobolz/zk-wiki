---
aliases: [Micali-style ZK]
tags: []
created: 2023-06-18
---

# Setting
- Interactive proofs
- Standalone setting, i.e. no [[Random Oracle|ROM]], no [[Reference String|CRS]] or anything like that.

# Formal definition
As usual, the definition requires the existence of a simulator that can, without the witness, simulate anything a (malicious) verifier could learn from observing an honest proof.

Formally,
> [!definition] Definition of classical ZK
> A protocol is zero knowledge if for all (malicious) [[Probabilistic Polynomial Time|ppt]] verifiers $V^*$, there exists an [[Expected Polynomial Time|ept]] simulator $S$ such that for all $(x,w)\in \mathcal{R}$
$$\textrm{view}_{V^*}(P(x,w) \leftrightarrow V^*(x))\approx S(x)$$

where $\textrm{view}_{V^*}(P(x,w) \leftrightarrow V^*(x)) = (x,r,m_1,\dots,m_n)$ denotes the distribution of the *view* of $V^*$ during execution of $P\leftrightarrow V^*$, i.e., 
- Input $x$ and randomness $r$ given to the machine $V^*$ 
- all messages $m_1,\dots,m_n$ sent by $P$ to $V^*$

> [!todo] Reference and quantifier order
> Find where this definition originally came from, cite that. Also, check the quantifier order. I've changed it relative to [[01 History and Basics#Zero Knowledge]] so that the simulator has to be the same for all $x$.

> [!info] Expected polynomial time
> We allow the simulator to run in expected polynomial time, which is more time than the (strict) polytime we allow the malicious verifier. 
> However, for most scenarios and security proofs, [[Expected Polynomial Time|ept]] is just as good [[Probabilistic Polynomial Time|ppt]]. See [[Expected Polynomial Time]].

# Typical simulator outline
Consider a protocol of the following form (e.g., [[Graph Isomorphism Protocol]], [[Quadratic Residue Protocol]], [[3-Colorable Protocol]])

> [!protocol] Protocol template
> - **Announcement**: prover sends some random value $a$
> - **Challenge**: The verifier chooses a random challenge $c \gets C$ (from a small challenge space $C$) 
> - **Response**: The prover sends some response $z$
> - The verifier verifies some equation of the form $f_c(z) = a$

Then the simulator $S(x)$ for (malicious) verifier $V^*$ *typically* works in two steps:
1. Generate a random transcript for an honest protocol (strategy: generate challenge $c$, generate random response $z$, then look at the verifier check to come up with a good announcement $a$ for $c,z$)
2. Do rejection sampling, checking whether $V^*$ would have chosen the same challenge as we did.

Concretely:
> [!algorithm] Simulator $S(x)$
> 1. $S$ computes a random transcript of the protocol:
> 	- $S$ chooses a random challenge $c \gets C$
> 	- $S$ chooses a random response $z$
> 	- $S$ computes the unique fitting announcement $a = f_c(z)$
> 2. $S$ rejects challenges $c$ not consistent with what $V^*$ would output:
> 	- $S$ chooses randomness $r$ and computes $c' = V^*(x;r;a)$ (where $V^*(x;r;a)$ is the challenge output by $V^*$ when started with input $x$, randomness $r$, after seeing announcement $a$)
> 	- If $c' = c$, then output the view $$(x,r,a,z)$$ Otherwise, start from the beginning and try again. 

The first bullet point enables the simulator to come up with random transcripts $(a,c,z)$ with random $c$. 

The rest of the simulator deals with the fact that the actual malicious verifier $V^*$ may not choose $c$ uniformly and independently at random, but may instead choose $c$ some other way. Essentially, this part of the simulator follows a rejection sampling approach: The simulator simply hopes that the challenge $c$ it generated in the first part happens to be the one that the verifier would also output. This happens with probability $1/|C|$, since $c$ chosen by the simulator is not shown to $V^*$ in any way. 
Hence we expect the simulator to repeat about $|C|$ times.