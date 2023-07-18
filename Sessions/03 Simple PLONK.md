---
aliases: []
tags: [session]
created: 2023-06-28
---

**🪑 Session chair:** Jan
<small>(Duties: Read material above-average carefully 🤓; prepare fallback discussions/questions (worst-case: just prepare some quiz questions about the material) 🙋. Prepare a few slides to guide the session through subtopics (this is <i>not</i> supposed to be a <i>detailed</i> summary of the material)</small>

**✍️ Notetaker:** Akira
<small>(Duties: Take notes during the session, push them to the wiki afterwards 📝. Moderate to get input for the wiki pages 🧠. Make people summarize / dumb down discussion results to keep things comprehensible for everyone 🧑‍⚖️.)</small>

## 🎯 Goals
This session covers the KZG commitment and PLONKish PolyIOP. We will particularly focus on the following contents from Lecture 5 (see [[#📚 Material]]). 

1. The KZG polynomial commitment scheme
    - Efficiency: commitment size, proof size, proving time, verification time
    - Compiling the equality check IOP with KZG
    - Security will be covered in the next session

2. Simple-PLONK PolyIOP
    - Gadgets for univariate polynomials: ZeroTest, SumCheck, ProdCheck, (Prescribed) Permutation Check (cf. [[Univariate Poly Tricks]])
    - PLONK's arithmetization

## ❓ Quiz Questions
### KZG
- Why does $\tau$ have to be discarded? How would you break the binding property using $\tau$?
- What does the Lagrange ”selector” polynomial $\lambda_i$ do?
- Can you safely publish both the “normal” KZG pp and the “Lagrange” KZG pp at the same time?
- If proofs are so much shorter, why don’t we just use KZG as a vector commitment everywhere instead of Merkle trees?

### Polynomial gadgets
- What’s the advantage of using the subgroup $\Omega = \langle \omega \rangle \subset\mathbb{Z}_p^*$ as a set for the ZeroTest/ProdCheck?
- Can we do an efficient ZeroTest/ProdCheck on arbitrary subsets? Or do we crucially need the subgroup properties? 
- What is the role of the second polynomial variable ($Y$) in the prescribed permutation check?

### Arithmetization
- does the prover actually have to run FFT to compute the coefficients of the computation trace polynomial?
- how many polynomials does the IOP prover commit to? How many are computed by the verifier? How many can be computed during preprocessing?

## 📚 Material
- [Lecture](https://youtu.be/A0oZVEXav24) ([Slides](https://zk-learning.org/assets/lecture5-2023.pdf))
- [PLONK arithmetization](https://hackmd.io/@jake/plonk-arithmetization)

## 📝 Notes
### Subsession 1 (July 13)
#### Overview of KZG Polynomial Commietment
- To support polynomials of degree at most $d$ as input, $\mathsf{KeyGen}$ of KZG must be run by a trusted party who makes sure to ``forget'' the secret exponent $\tau$. Revealing $\tau$ is devastating in many ways. For example, it is easy to break evaluation binding with the knowledge of $\tau$: 
    1. Given $f$, generate a commitment $\mathsf{com}=g^{f(\tau)}$
    2. Upon receiving an evaluation point $u$, pick an arbitrary fake statement $v\neq f(u)$. Generate a fake opening proof $\pi=g^{(f(\tau)-v)/(\tau-u)}$. Such $\pi$ always gets accepted by a verifier.
- With known $\tau$, one can generate arbitrary power of tau $g^{\tau^i}$, which also allows to break the maximum degree bound of supported polynomials. 
- To verify KZG opening proof, the verifer does not need to know the entire SRS---only $h^\tau$ is sufficient. This is because the verification equation is simply $e(\mathsf{com}/g^v,h)=e(\pi,h^{\tau}/h^u)$.
- Some times, the input polynomial is represented in point-value form rather than a vector of coefficients. In that case, it is more convenient to turn the SRS into Lagrange form 
$(g^{\lambda_i(\tau)})_{i=0,\ldots d}$.
Then the committing operation only takes linear time in $d$, instead of $O(d\log d)$ spent on NTT to move to the coefficient representation.
- Feist-Khovratovich algorithm for fast multi-point proof generation
    - To generate KZG proofs for opening proofs in  $\Omega\subset\mathbb{F}$ such that $|\Omega|=d$, a naive method requires $O(d^2)$ time if $\deg(f) = d$.
    - The FK algorithm can speed it up to achieve $O(d\log d)$, taking advantage of NTT!

#### Commit-and-Prove IOP gadgets
- The vanishing polynomial of $\Omega\subset\mathbb{F}$ of size $k$ is $Z_\Omega(X):=\prod_{a\in\Omega}(X-a)$. By choosing $\Omega=\{\omega^i\}_{i=0,\ldots,k-1}$ where $\omega\in\mathbb{F}$ is a primitive $k$-th root of unity, the vanishing polynomial $Z_\Omega(X)=X^k-1$ splits completely in $\mathbb{F}[X]$. This form is easy to deal with, because evaluation of of $Z_\Omega$ is simple double-and-add and only takes $\log_2 k$ field multiplications.
- Compiled ZeroTest on $\Omega$ can be slightly optimized using KZG. That is, the prover directly computes $\pi=g^{q(\tau)}=g^{f(\tau)/{Z_\Omega(\tau)}}$ as amortized opening proofs for $\mathsf{com}=g^{f(\tau)}$. The verifier can check $e(\mathsf{com},h)=e(\pi,h^{Z_\Omega(\tau)})$
- 