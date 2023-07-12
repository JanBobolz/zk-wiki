---
aliases: [KZG]
tags: []
created: 2023-07-12
---

A simple [[Polynomial Commitment]] scheme.

# Construction
The public parameters are $(g_1^{\tau^i}, g_2^{\tau^i})_{i=0}^n$ for a random $\tau\leftarrow \mathbb{Z}_p$ ($\tau$ enables you to break the binding property, so it *must* be safely discarded) and $g_1\leftarrow \mathbb{G}_1,g_2\leftarrow \mathbb{G}_2$.
A commitment to the polynomial $f\in \mathbb{Z}_p[x]$ of degree $\leq n$ is $$\mathsf{Com}(f) = g^{f(\tau)}$$
Which can be efficiently computed as $g^{f(\tau)} = \prod_{i=0}^n (g^{\tau^i})^{a_i}$ using the coefficients $a_i$ of $f$.


> [!info] Hiding property
> To make KZG hiding, one can use $\mathsf{Com}(f) = g^{f(\tau)}\cdot h^r$ for some other random group element $h$, though protocols may have other (more efficient) means of hiding $f$. 
> In the remainder of this article, we do not consider the hiding property, but note that what we write here should be applicable to the hiding version, too.

# Lagrange basis
Instead of $\mathit{pp} = (g^{\tau^i})_{i=0}^n$, people often use the Lagrange basis $\mathit{pp}_\mathrm{Lagrange} = (g^{\ell_i(\tau)})_{i=0}^n$ for better performance, where $\ell_i(i) = 1$ and $\ell_i(j) = 0$ for $j\in\{0,\dots,n\}\setminus\{i\}$.
Similarly to above, one can write any polynomial $f$ as a linear combination of the $\ell_i$ (via [[Lagrange interpolation]]), so computing $g^{f(\tau)}$ using $\mathit{pp}_\mathrm{Lagrange}$ can be done as $g^{f(\tau)} = \prod_{i=0}^n (g^{\ell_i(\tau)})^{f(i)}$.

> [!question] Someone please sanity-check this.
> For example: is $\{0,\dots,n\}$ the right basis to use for Lagrange or should it be some roots-of-unity thing?

# Operations and checks
## Factor opening
To prove that public $t$ is a factor of committed $f$ (i.e. $\exists q: q\cdot t = f$), the prover sends $\mathsf{Com}(q)$ and the verifier checks $$e(\mathsf{Com}(q), g_2^{t(\tau)}) = e(\mathsf{Com}(f), g_2)$$
(which is equivalent to checking that $\mathsf{Com}(q)^{t(\tau)} = \mathsf{Com}(f)$, but the pairing is used because the verifier cannot compute $\mathsf{Com}(q)^{t(\tau)}$, not knowing $\tau$).

## Point opening
To prove that $f(u) = v$ for a committed polynomial $f$, one shows that $(x-u)$ is a factor of $f-v$ (which then implies that $(f-v)(u) = 0$, i.e. $f(u) = v$). See [[Univariate Poly Tricks#Evaluation test via factor test]].