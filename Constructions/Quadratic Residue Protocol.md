---
aliases: [QR protocol, QR]
tags: []
created: 2023-06-18
---

A [[Cut-and-Choose]] protocol for proving that a number is a square mod $N$. $$\mathcal{R} = \{((N, y), w) \mid y\in \mathbb{Z}_N^*\text{ and } w^2 = y \mod N\}$$
The $y\in\mathbb{Z}_N^*$ part is trivial to check efficiently by any verifier itself. The interesting part is $w^2 = y$, i.e. that $y$ has a square root. This is an interesting statement because if $N$ is the product of two large random primes, then it's difficult to decide whether any given $y$ has a square root or not.

> [!info] Notation
> We drop the "$\bmod N$" from expressions below, but any computation involving elements from $\mathbb{Z}_N$ is understood to be $\bmod N$.

> [!warning] This protocol is very similar to [[Graph Isomorphism Protocol]].

# The main idea
Following the [[Cut-and-Choose]] paradigm, the protocol splits $x = (N,y)$ and square root $w$ into 
- $x_0 = (N, r^2), w_0 = r$ 
- $x_1 = (N,r^2\cdot y), w_1 = r\cdot w$
for a random value $r\gets\mathbb{Z}_N^*$. 

> [!info] Intuition
> The two parts read as
> - $r^2$ is a square (with random square root $r$) 
> - $r^2\cdot y$ is a square (with random square root $r\cdot w$)

Then we get the two required properties (see [[Cut-and-Choose#Splitting the statement into parts]]):
- Neither $w_0 = r$ nor $w_1 = r\cdot w$ on their own reveal anything about $\sigma$ (given that $r$ is a random group element from $\mathbb{Z}_N^*$)
- Together, $w_0 = r$ and $w_1 = r\cdot w$ reveal $w$.

# The protocol
Following the [[Cut-and-Choose#The protocol]] template:

> [!protocol] Protocol between $P(N,y,w)$ and $V(N,y)$
> - **Announcement**: Prover chooses a random $r\gets\mathbb{Z}_N^*$ and sends announcement $a = r^2$.
> - **Challenge**: The verifier chooses a random challenge $c\gets\{0,1\}$ (challenging the prover to reveal the square root of $a\cdot y^c$).
> - **Response**: The prover reveals $z = \begin{cases} r & c=0 \\ r\cdot w  & c = 1\end{cases}$
> - The verifier checks that $y\in\mathbb{Z}_N^*$ and $z^2 = a\cdot y^c$.

> [!info] Optimization of the [[Cut-and-Choose]] template
> Instead of sending both "split" instances $x_0 = (N,r^2)$ and $x_1 = (N,r^2y)$, we simply send $r^2$, given that the verifier already knows $N,y$. 
> In the verification equation, the verifier adds the "$\cdot y$" itself, if needed.

## The [[Zero Knowledge (Classical)|ZK]] property
This follows [[Zero Knowledge (Classical)#Typical simulator outline]]. 

The [[Zero Knowledge (Classical)]] simulator $S(x)$ for (malicious) verifier $V^*$ works as follows:
> [!algorithm] Simulator $S(G_1,G_2)$
> 1. $S$ computes a random transcript of the protocol:
> 	- $S$ chooses a random challenge $c \gets \{0,1\}$
> 	- $S$ chooses a random response $z \gets \mathbb{Z}_N^*$
> 	- $S$ computes the unique fitting announcement $a = z^2\cdot y^{-c}$
> 2. $S$ rejects challenges $c$ not consistent with what $V^*$ would output:
> 	- $S$ chooses randomness $R$ and computes $c' = V^*(G_1,G_2;R;a)$
> 	- If $c' = c$, then output the view $$(x,R,a,z)$$ Otherwise, start from the beginning and try again. 

## The [[Proof of Knowledge (Classical)|PoK]] property
For simplicity, we assume that the prover has probability 1 to convince an honest verifier, which puts us into the [[Proof of Knowledge (Classical)#A simplified toy definition]] scenario. 
The extractor for prover $P^*$ works as follows:
> [!algorithm] Extractor $E^{P^*}(N,y)$
> - $E$ retrieves the prover’s announcement $a = P^*(G_0,G_1;R)$ (for some random $R$)
> - $E$ retrieves the prover’s response for both challenges $c\in\{0,1\}$ as $z_c = P^*(N,y;R;c)$
> 	- From the verification equations, we get $z_0^2 = a$ and $z_1^2 = a\cdot y$
> - $E$ outputs the witness $w = z_1/z_0$

Formally, the witness is correct because $w^2 = z_1^2 / z_0^2 = ay / a = y$.

> [!info] Non-simplified scenario
> 
> > [!theorem] Theorem: the quadratic residue protocol is a [[Proof of Knowledge (Classical)]] with knowledge error $\kappa = 1/2$.
> 
> For a prover that may not answer all challenges correctly *always* (but convinces the verifier with probability $> 1/2$), it may happen that when $E$ gets the two responses in the second step, one or more of them is actually invalid, i.e. fails the verification equation. In that case, the response is useless for the extractor.
> In that scenario, $E$ will have to restart and try different prover randomness $R$. Using a counting argument, one can show that for *some* $R$, the prover has to be able to answer both challenges (otherwise it cannot be convincing with probability larger than $1/2$). The extractor then tries different $R$ until it finds one for which both challenges can be answered.
