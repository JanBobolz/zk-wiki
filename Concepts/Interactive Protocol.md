---
title: "Interactive Protocols"
aliases: [IP, Interactive Proof]
tags: []
created: 2023-05-03
---

# Examples from Lecture 1
## Toy Example: Page with two colors
- The prover convinces a blind verifer that a page contains two distinct colors

## ZKP for Quadratic Residue
- Relation: $R_{\text{QR}}= \{((Y,N),X): Y = X^2 \bmod N \land \text{gcd}(N,X)=1\}$

## ZKP for Graph Isomorphism
- Relation $R_\text{GI} = \{((G_0,G_1), \pi) : \pi(G_0)=G_1\}$
- The protocol is also PoK of $\pi$

## ZKP for 3 Colorable Graph
- Relation $R_\text{3CG}=\{((E,V), c) : \forall e_{i,j}\in E, c(v_i)\neq c(v_j) \land |\text{Im}(f)|=3\}$

## ZKP for Graph Non-Isomorphism
- Language $L_\text{GNI} = \{(G_0,G_1) : \forall G \in \text{permutation of } G_0, G_1 \neq G\}$
	- We don't know if GNI is in NP. The protocol illustrates an example of ZKP for languages outside NP. 


# The complexity class IP
