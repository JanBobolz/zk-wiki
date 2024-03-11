---
aliases:
  - folding
tags: 
created: 2024-03-11
---
An interactive protocol between a prover and verifier that takes two instances $x_1,x_2$ as common input. The prover also gets witnesses $(w_1,w_2)$ as input. It outputs a *folded* instance $x$ (idea: $x = x_1 + c x_2$ for the verifier's random challenge $c$). The prover also outputs a folded witness $w$ (idea: $w = w_1 + cw_2$).

Allows compressing many statements into one statement of essentially the same size. Enables [[Incrementally Verifiable Computation|IVC]].

## Properties
- Completeness: If the original $(x_i,w_i)\in\mathcal{R}$, then also the folded $(x,w)\in\mathcal{R}$
- Soundness: Given $x_1,x_2$ and the folded $x,w$, can (with [[Rewinding]] magic) compute valid witnesses $w_1,w_2$ for the unfolded statements $x_1,x_2$.
	- More formally: Given the folded witness $(x,w) \in\mathcal{R}$ and the original instances $x_1,x_2$ that folded into $x$ (and three transcripts of the folding protocol with different challenges), one can efficiently compute witnesses $w_1,w_2$ (such that $(x_i,w_i)\in\mathcal{R}$)
- Zero Knowledge: the transcript doesn't leak anything about the witness.

# Instantiations
- [[Nova]] original use of this notion, using [[Relaxed R1CS]]
- [[Sangria]] folding for [[PLONK|plonkish]] relations

# Resources
- [Nova paper](https://eprint.iacr.org/2021/370) introduces the notion