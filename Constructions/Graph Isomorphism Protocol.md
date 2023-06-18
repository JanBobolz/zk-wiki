---
aliases: [Graph Isomorphism]
tags: []
created: 2023-06-12
---

A [[Cut-and-Choose]] protocol for proving that two graphs are isomorphic. $$\mathcal{R} = \{((G_0,G_1),\sigma) \mid \sigma\text{ is an isomorphism and } \sigma(G_0) = G_1\}$$
# The main idea
Following the [[Cut-and-Choose]] paradigm, the protocol splits $x = (G_1,G_2)$ and homomorphism $w = \sigma$ into 
- $x_0 = (G_0,\gamma(G_0)), w_0 = \gamma$ 
- $x_1 = (G_1,\gamma(G_0)), w_1 = \gamma\circ\sigma^{-1}$
for a random permutation $\gamma$. 

> [!info] Intuition
> For $H = \gamma(G_0)$, the two parts read as
> - $G_0$ is isomorphic to $H$ (with random isomorphism $\gamma$) 
> - $G_1$ is isomorphic to $H$ (with random isomorphism $\gamma\circ\sigma^{-1}$)

Then we get the two required properties (see [[Cut-and-Choose#Splitting the statement into parts]]):
- Neither $w_0 = \gamma$ nor $w_1 = \gamma\circ\sigma^{-1}$ on their own reveal anything about $\sigma$ (given that $\gamma$ is a random isomorphism)
- Together, $w_0 = \gamma$ and $w_1 = \gamma\circ\sigma^{-1}$ reveal $\sigma$.

# The protocol
Following the [[Cut-and-Choose#The protocol]] template:

> [!protocol] Protocol between $P(G_0,G_1,\omega)$ and $V(G_0,G_1)$
> - **Announcement**: Prover chooses a random graph isomorphism $\gamma$ and sends $H = \gamma(G_0$).
> - **Challenge**: The verifier chooses a random challenge $c\gets\{0,1\}$ (challenging the prover to reveal an isomorphism between $G_c$ and $H$).
> - **Response**: The prover reveals $\omega = \begin{cases} \gamma & c=0 \\ \gamma\circ\sigma^{-1}  & c = 1\end{cases}$
> - The verifier checks that $\omega$ is an isomorphism and $\omega(G_c) = H$.

> [!info] Optimization of the [[Cut-and-Choose]] template
> Instead of sending both "split" instances $x_0 = (G_0,H)$ and $x_1 = (G_1,H)$, we simply send $H$, given that the verifier already knows $G_0,G_1$. 

## The [[Zero Knowledge (Classical)|ZK]] property
This follows [[Zero Knowledge (Classical)#Typical simulator outline]]. 

The [[Zero Knowledge (Classical)]] simulator $S(x)$ for (malicious) verifier $V^*$ works as follows:
> [!algorithm] Simulator $S(G_1,G_2)$
> 1. $S$ computes a random transcript of the protocol:
> 	- $S$ chooses a random challenge $c \gets \{0,1\}$
> 	- $S$ chooses a random response $\omega \gets \mathsf{Perm}$
> 	- $S$ computes the unique fitting announcement $H = \omega(G_c)$
> 2. $S$ rejects challenges $c$ not consistent with what $V^*$ would output:
> 	- $S$ chooses randomness $r$ and computes $c' = V^*(G_1,G_2;r;H)$
> 	- If $c' = c$, then output the view $$(x,r,H,\omega)$$ Otherwise, start from the beginning and try again. 

## The [[Proof of Knowledge (Classical)|PoK]] property
For simplicity, we assume that the prover has probability 1 to convince an honest verifier, which puts us into the [[Proof of Knowledge (Classical)#A simplified toy definition]] scenario. 
The extractor for prover $P^*$ works as follows:
> [!algorithm] Extractor $E^{P^*}(G_0,G_1)$
> - $E$ retrieves the prover’s announcement $H = P^*(G_0,G_1;r;())$ (for some random $r$)
> - $E$ retrieves the prover’s response for both challenges $c\in\{0,1\}$ as $\omega_c = P^*(G_0,G_1;r;(c))$
> 	- From the verification equation, we get $\omega_c(G_c) = H$
> - $E$ outputs the witness $\sigma = \omega_1^{-1}\circ\omega_0$

Intuitively, the two verification equations give us maps $G_0 \overset{\omega_0}{\rightarrow} H \overset{\omega_1}{\leftarrow} G_1$, which we just plug together to get from $G_0$ to $G_1$ (via $H$).
Formally, the witness is correct beca use $\sigma(G_0) = \omega_1^{-1}(\omega_0(G_0)) = \omega_1^{-1}(H) = G_1$.

> [!info] Non-simplified scenario
> 
> > [!theorem] Theorem: the Graph Isomorphism property is a [[Proof of Knowledge (Classical)]] with knowledge error $\kappa = 1/2$.
> 
> For a prover that may not answer all challenges correctly *always* (but convinces the verifier with probability $> 1/2$), it may happen that when $E$ gets the two responses in the second step, one or more of them is actually invalid, i.e. fails the verification equation. In that case, the response is useless for the extractor.
> In that scenario, $E$ will have to restart and try different prover randomness $r$. Using a counting argument, one can show that for *some* $r$, the prover has to be able to answer both challenges (otherwise it cannot be convincing with probability larger than $1/2$). The extractor then tries different $r$ until it finds one for which both challenges can be answered.

# Notes
- Graph Isomorphism is not known to be $\mathbf{NP}$-complete, so the language is not of much importance. However, its complement ("prove two graphs are *not* isomorphic") is not known to be in $\mathbf{NP}$, so Graph Isomorphism and its complement often come up in educational contexts as good examples for the power of the class [[Interactive Protocol|IP]].