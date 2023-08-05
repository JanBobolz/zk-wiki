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
### Subsession 1-3 (July 13, 20, 27)
#### Overview of KZG Polynomial Commietment
- To support polynomials of degree at most $d$ as input, $\mathsf{KeyGen}$ of KZG must be run by a trusted party who makes sure to „forget“ the secret exponent $\tau$. Revealing $\tau$ is devastating in many ways. For example, it is easy to break evaluation binding with the knowledge of $\tau$: 
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
##### Zero Test on $\Omega$: $f(a)=0, \forall a\in\Omega$
- The vanishing polynomial of $\Omega\subset\mathbb{F}$ of size $k$ is $Z_\Omega(X):=\prod_{a\in\Omega}(X-a)$. By choosing $\Omega=\{\omega^i\}_{i=0,\ldots,k-1}$ where $\omega\in\mathbb{F}$ is a primitive $k$-th root of unity, the vanishing polynomial $Z_\Omega(X)=X^k-1$ splits completely in $\mathbb{F}[X]$. This form is easy to deal with, because evaluation of of $Z_\Omega$ is simple double-and-add and only takes $\log_2 k$ field multiplications.
- Compiled ZeroTest on $\Omega$ can be slightly optimized using KZG. That is, the prover directly computes $\pi=g^{q(\tau)}=g^{f(\tau)/{Z_\Omega(\tau)}}$ as amortized opening proofs for $\mathsf{com}=g^{f(\tau)}$. The verifier can check $e(\mathsf{com},h)=e(\pi,h^{Z_\Omega(\tau)})$

##### Product Check on $\Omega$: $\prod_{a\in\Omega}f(a)=1$
- Core idea: (1) Construct a degree-$k$ polynomial $t(X)$ which encodes "the $s$-th parttial product of $f$" (i.e. $\prod_{i=0}^s f(\omega^i)$) at $\omega^s$ for $s=0,\ldots,k-1$, and (2) invoke Zero Test on $\Omega$ for the polynomial $t_1(X):= t(\omega X) - t(X)f(\omega X)$.
- Q. How many queries does the verifier make? - 5
    - $q=t_1/Z_\Omega$ at $r$
    - $f$ at $\omega r$
    - $t$ at $\omega^{k-1}$, $r$, and $\omega r$

##### Permutation Check: $f(\omega^i)_{i=0}^{k-1}=W(g(\omega^i)_{i=0}^{k-1})$ for some permutation $\Omega$
- Q. To prove $f$ is a permutation of $g$, why can't we just rely on the equality check for $\hat{f}(a)=\prod_{a\in\Omega}(X-f(a))$ and $\hat{g}(a)=\prod_{a\in\Omega}(X-g(a))$? 
    - A. Without Product Check, the verifier cannot (efficiently) check that $\hat{f},\hat{g}$ are correctly constructed from $f,g$
- Q. Why 6 queries? 
    - $q=t_1/Z_\Omega$ at $r$
    - $f$ at $\omega r$
    - $g$ at $\omega r$
    - $t$ at $\omega^{k-1}$, $r$, and $\omega r$

##### Prescribed Permutation Check: $f(y)-g(W(y))=0, \forall y\in\Omega$ 
- Naive approach (i.e. invoking Zero Test on $f(X)-g(W(X))$) is still okay for constructing SNARK *in theory*.
    - Linear time prover is usually not a requirement for SNARK. We only care about the verification time and proof size. 

#### Simple-PLONK
- Remark: The original PLONK construction encodes left, right, output wires separately into polynomials $f_L,f_R, f_O$, respectively. The present  construction simplifies it by combining them in a single $T$ of degree $3|C|+|I|$. 

- The selector polynomial $S$ only encodes binary info, but if we need more gate types than just addition and multiplication, we can generalize $S$ in a straightforward manner. 

- We can construct PolyIOP for PLONK constraint systems by taking advantage of the above commit-and-prove gadgets: 
1. Input Check: $T(y)-v(y)=0,\forall a\in\Omega_{inp}$ 
2. Gate-by-Gate Check: $F(y):=S(y)(T(y)+T(\omega y)) + (1-S(y))T(y)T(\omega y)=T(\omega^2 y)$
3. Wiring Check: $T(y)=T(W(y)),\forall y\in\Omega$

- Q. Why the soundness error $7|C|/p$? 
    - A. In Gate-by-Gate Check (realized by Zero Check on the polynomial F(X)), the prover sends $q=F/Z_{\Omega}$ which has degree at most $7|C|$ because:
        - $\deg(S) = 3|C|$;
        - $\deg(T(X)\cdot T(\omega X)) = 2\cdot(3|C|+|I|)$;
        - $\deg(Z_\Omega) = 3|C|$;
        - assuming $|I|\ll|C|$;
        - and therefore, $\deg(f)=\deg(S)+\deg(T(X)\cdot T(\omega X))-\deg(Z_\Omega)<7|C|$

- Q. On page 53, why $S$ and $W$ are "untrusted"? 
    - A. Anyone can publicly check that $S$ and $W$ are correctly constructed from the circuit constraints (in the offline phase). No need to rely on any trusted third party generating them correctly. 