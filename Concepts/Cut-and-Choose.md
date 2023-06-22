---
title: "Cut-and-Choose Protocol"
aliases: []
tags: []
created: 2023-05-03
---

Cut-and-Choose is a general strategy for achieving [[Zero Knowledge|ZK]], which works by having the prover **cut** the statement into multiple parts and then having the verifier **choose** one of those parts to reveal.

> [!hint] The "Cut-and-Choose" naming
> The naming in inspired by the famous "[fair cake-cutting](https://en.wikipedia.org/wiki/Fair_cake-cutting) protocol" in which one person cuts the cake, then the other person chooses which half to get. 

# Intuition
A Cut-and-Choose protocol generally follows these ideas:

## Splitting the statement into parts
The prover splits the statement $x$ and witness $w$ into multiple parts $(x_1,w_1),\dots,(x_n,w_n)$, such that
- Just seeing any *one* part's witness $w_i$ does not reveal anything about the actual witness $w$.
- *All* parts $(x_1,w_1),\dots,(x_n,w_n)$ together reveal the witness $w$
	- If we don't care about [[Proof of Knowledge]], it's sufficient to say that it's difficult to reveal all parts if $x\notin\mathcal{L}$.

> [!info] 
> The first property will enable [[Zero Knowledge]]: a protocol transcript will only contain a single $w_i$.
> The second property will enable [[Proof of Knowledge]]: the extractor can get multiple $w_i$ through [[Rewinding]].

## The protocol
The protocol then works as follows.
- The prover splits $(x,w)$ into $(x_1,w_1),\dots,(x_n,w_n)$.
- The prover sends the split statements $x_1,\dots,x_n$.
- The verifier chooses a random index $c\leftarrow\{1,\dots,n\}$.
- The prover reveals $w_i$.
- The verifier checks that 
	- $x_1,\dots,x_n$ is a valid split of $x$ (details depend on the protocol)
	- $w_i$ is a valid witness for $x_i$.

> [!info] Notation
> For $n=2$, we often zero-base the names of the split $(x_0,w_0), (x_1,w_1)$ so that the challenge is a bit $c\in\{0,1\}$ and points to the part $(x_c, w_c)$ to open.

> [!caution] Challenge space
> Because the challenge space is small for Cut-and-Choose protocols, the [[Soundness#Soundness error|soundness error]] is large. Cut-and-Choose protocols generally have to be repeated multiple times in order to bring the soundness error down to acceptable levels.

# Protocols that follow this idea
## "Pure" Cut-and-Choose
- [[Quadratic Residue Protocol]]
	- Splits "$w$ is a square root of $y$" into (0) "$w_0 = rw$ is a square root of $r^2y$" and (1) "$w_1 = r$ is a square root of $r^2$" for a random $r$.
- - [[Graph Isomorphism Protocol]]
	- Splits "$\sigma$ is an isomorphism between $G_0$ and $G_1$" into (0) "$\gamma$ is an isomorphism between $G_0$ and $\gamma(G_0)$" and (1) "$\gamma\circ\sigma^{-1}$ is an isomorphism between $G_1$ and $\gamma(G_0)$" for some random permutation $\gamma$.

## Using commitments for the split 
(sometimes called "[[Commit-and-Prove]]")

- [[3-Colorable Protocol]]
	- Splits "$\sigma$ is a valid 3-coloration for $G=(V,E)$" into "$\sigma$ assigns the nodes $a,b$ different colors" for each edge $(a,b)\in E$.
- [[Stern-like ZK]]
	- Class of protocols for [[Lattice ZK|ZK proofs for Lattice problems]] with applications to post-quantum secure protocols, e.g. [KTX identification scheme](https://www.iacr.org/archive/asiacrypt2008/53500376/53500376.pdf).
- [[MPC in the Head]]
	- Splits the statement into pairs of views of participants of an [[MPC]] protocol that verifies that statement.

# Resources
- Basic protocols: [Lecture](https://www.youtube.com/watch?v=uchjTIlPzFo) ([slides](https://zk-learning.org/assets/Lecture1-2023-slides.pdf))
- https://doi.org/10.1016/0022-0000(88)90005-0 coins the "Cut-and-Choose" terminology.