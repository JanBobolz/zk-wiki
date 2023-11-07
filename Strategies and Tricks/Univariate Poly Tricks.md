---
aliases: 
tags: 
created: 2023-07-12
---

This article lists a bunch of reductions of the form "in order to check property x on polynomial $f$, one can check property $y$ on $f'$, instead".

> [!note] Notation
> - $f,h \in \mathbb{Z}_p[x]$ are polynomials of degree at most $n$.
> - $\Omega\subset \mathbb{Z}_p$ is a subset of $\mathbb{Z}_p$, typically (for performance) a multiplicative subgroup of $\mathbb{Z}_p^*$ of the form $\Omega = \langle \omega \rangle = \{\omega^i\}_{i=0}^{k-1}$ for a primitive $k$th root of unity $\omega\in\mathbb{Z}_p^*$.
> - We assume that $p$ is exponentially large (prime order of elliptic curve group, roughly $p\approx 2^{256}$), $n$ is very large (*roughly* the size of the circuit we want to prove stuff about), $k$ is also pretty big. $p \gg n > k$.

> [!todo] Sanity check
> What's the typical size of $n,k$ in the callout above?

# Polynomial equality test
To test polynomial equality $f = h$, one can instead test that $f(c) = t(c)$ for some random challenge $c\leftarrow\mathbb{Z}_p$.
See [[Schwartz-Zippel]].

# Zero test
To test that $f=0$, one can test that $f(c) = 0$ for a random $c\leftarrow \mathbb{Z}_p$. 
See [[Schwartz-Zippel]].

# Batch zero test via factor test
To test that $f(\Omega) = \{0\}$ (i.e. $\forall \omega\in \Omega:\ f(\omega) = 0$) for any subset $\Omega \subset \mathbb{Z}_p^*$, one can check that $$\left(\prod_{\omega\in\Omega}(x-\omega)\right) \mid f$$
> [!info] Performance
> Computing/evaluating the polynomial $Z_\Omega = \prod_{\omega\in\Omega}(x-\omega)$ potentially takes a lot of time, which we often want to avoid.
> In those cases, it's useful to choose $\Omega = \langle \omega \rangle = \{\omega^i\}_{i=0}^{k-1}$ for a primitive $k$th root of unity $\omega\in\mathbb{Z}_p^*$, in which case $Z_\Omega = x^k-1$, which is very efficient to compute and evaluate. 

# Evaluation test via factor test
To check that $f(u) = v$, one can instead test that $$(x-u)|(f-v)$$
(i.e. $\exists q\in\mathbb{Z}_p[x]:\ q\cdot (x-u) = f-v$)

> [!info] Idea
> The idea here is $f(u) = v \Leftrightarrow (f-v)(u) = 0 \Leftrightarrow (x-u) | (f-v)$.

# Product check
To check that $\prod_{i=0}^{k-1}f(\omega^i) = 1$, for a primitive $k$th root of unity $\omega$, have the prover interpolate the partial product polynomial $$t(\omega^s) = \prod_{i=0}^s f(\omega^i)$$ for $s\in\{0,\dots,k-1\}$. Note that $t(\omega^{k-1}) = \prod_{i=0}^{k-1}f(\omega^i)$, which is what we want to check is $1$.
The product check boils down to 
1. $t(\omega^{k-1}) = 1$ (which is what we want to check)
2. $t(\omega\cdot z)-t(z)\cdot f(\omega\cdot z) = 0$ for all $z\in\Omega$ (well-formed $t$)

The second test can be done via [[#Batch zero test via factor test]] on $f'(x) = t(\omega\cdot x)-t(x)\cdot f(\omega\cdot x)$.

> [!note] Batch zero test on $f'$
> Note that evaluating $f'$ at a random position $c$ can be done by simply evaluating $t$ at $\omega\cdot c$ and $c$, and $f$ at $\omega\cdot c$, so $f'$ can be implicitly represented, no need for additional commitments on the prover side.

# Sum check

# Permutation check

# Prescribed permutation check


# Resources
- https://zk-learning.org/assets/lecture5-2023.pdf