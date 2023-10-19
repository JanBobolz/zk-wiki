---
aliases: []
tags: []
created: 2023-10-18
---
$\newcommand{\FF}{\mathbb{F}}$
$\newcommand{\idx}{\mathsf{i}}$
$\newcommand{\stm}{\mathsf{x}}$
$\newcommand{\wit}{\mathsf{w}}$

# QAP
Consider an arithmetic circuit consisting of $d$ multiplication gates and $m$ wires. 
For the $j$ th multiplication gate, we assign a constant $\omega_j\in\FF$. Let $t(X)=\prod_{j=1}^d(X-\omega_j)$ be a vanishing polynomial over $\omega_1,\ldots,\omega_d$.

Define selector polynomials $u_i,v_i,w_i\in\FF[X]$ for $i=0,\ldots,m$ of degree $d-1$ such that
  - $u_i(r_j)=1$ if the $i$-th wire is left input of $j$-th gate, and $0$ otherwise.
  - $v_i(r_j)=1$ if the $i$-th wire is right input of $j$-th gate, and $0$ otherwise.
  - $w_i(r_j)=1$ if the $i$-th wire is output of $j$-th gate, and $0$ otherwise.
  - $u_0,v_0,w_0$ specify addition/mulitplication by constants. 

A quadratic arithmetic program (QAP) over $\FF$ consists of $((u_i,v_i,w_i)_{i\in[0,m]},t)$.
The wire values $a_1,\ldots,a_m$ satisfies QAP iff there exists some $h\in\FF_{\leq d-2}[X]$ such that
$$\left(\sum_{i=0}^m a_i u_i(X)\right)\cdot\left(\sum_{i=0}^m a_i v_i(X)\right) - \sum_{i=0}^m a_i w_i(X) \equiv h(X) t(X)$$
where $a_0=1$. 

# Relation
Given an index $\idx = ((u_i,v_i,w_i)_{i\in[0,m]},t)$, we obtain the following index relation:

$$R_{\text{QAP}}= \left\{(\idx,(a_i)_{i\in[0,\ell]},(a_i)_{i\in[\ell+1,m]}) \,:\, \exists h\in\FF_{\leq d-2}[X],\left(\sum_{i=0}^m a_i u_i(X)\right)\cdot\left(\sum_{i=0}^m a_i v_i(X)\right) - \sum_{i=0}^m a_i w_i(X) \equiv h(X) t(X), a_0=1  \right\}$$

where $\ell$ is the size of public statement. 

# The naive protocol
For a moment, let's assume that a public statement is empty i.e. $\ell=0$.
To prove knowledge of a valid witness vector $a_1,\ldots,a_m$, we can consider the following protocol that checks divisibility of QAP relation taking advantage of bilinear pairings. 

> [!protocol] 
> - **Setup** $G(u_i,v_i,w_i,t)$: Output a CRS $\sigma$ containing
>   - $[x^i]_1$ for $i=1,\ldots,d-1$
>   - $[x^i]_2$ for $i=1,\ldots,d-1$
>   - $[t]_2$
> - **Prove**: $P(\sigma,(a_i)_{i\in [0,m]})$ outputs $\pi = (A,B,C,D)$ where
>   - $A = [\sum_{i=0}^m a_i u_i ]_1$
>   - $B = [\sum_{i=0}^m a_i v_i ]_2$
>   - $C = [\sum_{i=0}^m a_i w_i ]_1$
>   - $D = [h]_1$
> - **Verify**: $V(\sigma,\pi)$ checks
>   - $e(A,B) = e(C,[1]_2) \cdot e(D, [t]_2)$

> [!info] Attacks
> Assuming that an adversary only performs generic group operations on CRS $\sigma$, we can at least make sure that for *some* polynomials $a,b,c$ such that $A=[a]_1,B=[b]_2,C=[c]_1$, the polynomial $a\cdot b- c$ is divisible by $t$. It is easy to see however that a cheating prover can convince the verifier in essentially two ways: (1) use arbitrarily shifted polynomials to construct $a,b,c$ e.g. $a = \sum_i a_i u_i + f$ instead of the linear span of $u_i$, and  (2) use inconsistent wire values to construct $a,b,c$ i.e. $a = \sum_i a_i u_i, b = \sum_i a'_i v_i, c = \sum_i a''_i u_i$. 
> To counter the first attack, we can introduce additional pairing checks to make sure that $a,b,c$ are computed from the correct linear spans. That is, we ask a prover to additionally output $A'=A^{\alpha_u}$, while including $[\alpha_u u_i]_1$ (but e.g. not $[\alpha_u x^i]_1$) in the CRS. In this way the prover is forced to construct $A$ as linear combinations of $[u_i]_1$ only. 
> The second attack can be thwared by adding another pairing check to make sure that the same coefficients are used for $a,b,c$. This can be realized by having a prover to compute $E=(ABC)^\beta$ while including $[\beta(u_i+v_i+w_i)]_1$ in the CRS. 

# Simple Pinocchio
In what follows, we assume the type-I pairing for simplicity. 
> [!protocol] 
> - **Setup** $G(u_i,v_i,w_i,t)$: Output a CRS $\sigma$ containing
>   - $[x^i]_1$ for $i=1,\ldots,d-1$
>   - $[x^i]_2$ for $i=1,\ldots,d-1$
>   - $[t]_2$
>   - $[\alpha_u]_2, [\alpha_v]_1, [\alpha_w]_2, [\beta]_2$
>   - $[\alpha_u u_i]_1,[\alpha_v v_i]_2,[\alpha_w w_i]_1$ for $i=0,\ldots,m$
>   - $[\beta (u_i+v_i+w_i)]$ for $i=0,\ldots,m$
> - **Prove**: $P(\sigma,(a_i)_{i\in [0,m]})$ outputs $\pi = (A,A',B,B',C,C',D,E)$ where
>   - $A = [\sum_{i=0}^m a_i u_i ]_1$, $A' = [\sum_{i=0}^m a_i \alpha_u u_i ]_1$
>   - $B = [\sum_{i=0}^m a_i v_i ]_2$, $B' = [\sum_{i=0}^m a_i \alpha_v v_i ]_2$
>   - $C = [\sum_{i=0}^m a_i w_i ]_1$, $C' = [\sum_{i=0}^m a_i \alpha_w w_i ]_1$
>   - $D = [h]_1$
>   - $E = [\sum_{i=0}^m a_i \beta (u_i + v_i + u_i)]_1$
> - **Verify**: $V(\sigma,\pi)$ performs the following checks.
>   - *Divisibility check*: $e(A,B) = e(C,[1]_2) \cdot e(D, [t]_2)$ 
>   - *Linear span check*: $e(A,[\alpha_u]) = e(A',[1]_2)$, $e([\alpha_v]_1,B) = e([1]_1,B')$, $e(C,[\alpha_w]) = e(C',[1]_2)$
>   - *Wire consistency check*: $e(ABC, [\beta]_2) = e(E, [1]_2) $ 

# Pinocchio with public inputs
Now it is easy to account for public inputs $a_1,\ldots,a_\ell$. All we need to do is to ask a prover to compute the above using private inputs and a verifer to adjust the proof strings using the knowledge of a public statement. 

> [!protocol] 
> - **Setup** $G(u_i,v_i,w_i,t)$: Output a CRS $\sigma$ containing
>   - $[x^i]_1$ for $i=1,\ldots,d-1$
>   - $[x^i]_2$ for $i=1,\ldots,d-1$
>   - $[t]_2$
>   - $[\alpha_u]_2, [\alpha_v]_1, [\alpha_w]_2, [\beta]_2$
>   - $[\alpha_u u_i]_1,[\alpha_v v_i]_2,[\alpha_w w_i]_1$ for $i=0,\ldots,m$
>   - $[\beta (u_i+v_i+w_i)]$ for $i=0,\ldots,m$
> - **Prove**: $P(\sigma,(a_i)_{i\in [0,m]})$ outputs $\pi = (A,A',B,B',C,C',D,E)$ where
>   - $A = [\sum_{i=\ell+1}^m a_i u_i ]_1$, $A' = [\sum_{i=\ell+1}^m a_i \alpha_u u_i ]_1$
>   - $B = [\sum_{i=\ell+1}^m a_i v_i ]_2$, $B' = [\sum_{i=\ell+1}^m a_i \alpha_v v_i ]_2$
>   - $C = [\sum_{i=\ell+1}^m a_i w_i ]_1$, $C' = [\sum_{i=\ell+1}^m a_i \alpha_w w_i ]_1$
>   - $D = [h]_1$
>   - $E = [\sum_{i=\ell+1}^m a_i \beta (u_i + v_i + u_i)]_1$
> - **Verify**: $V(\sigma,(a_i)_{i\in [0,\ell]},\pi)$ first adjusts proof strings as follows:
>   - $A^* = [\sum_{i=0}^\ell a_i u_i ]_1 \cdot A$
>   - $B^* = [\sum_{i=0}^\ell a_i v_i ]_2 \cdot B$
>   - $C^* = [\sum_{i=0}^\ell a_i w_i ]_1 \cdot C$
> and then performs the following checks.
>   - *Divisibility check*: $e(A^*,B^*) = e(C^*,[1]_2) \cdot e(D, [t]_2)$ 
>   - *Linear span check*: $e(A,[\alpha_u]) = e(A',[1]_2)$, $e([\alpha_v]_1,B) = e([1]_1,B')$, $e(C,[\alpha_w]) = e(C',[1]_2)$
>   - *Wire consistency check*: $e(ABC, [\beta]_2) = e(E, [1]_2) $ 