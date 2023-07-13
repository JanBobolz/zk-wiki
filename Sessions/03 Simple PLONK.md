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
- Why does $\tau$ have to bee discarded? How would you break the binding property using $\tau$?
- What does the Lagrange ”selector” polynomial $\lambda_i$ do?
- Can you safely publish both the “normal” KZG pp and the “Lagrange” KZG pp at the same time?
- If proofs are so much shorter, why don’t we just use KZG as a vector commitment everywhere instead of Merkle trees?

### Polynomial gadgets
- What’s the advantage of using the subgroup $\Omega = \langle \omega \rangle \subset\mathbb{Z}_p^*$ as a set for the ZeroTest/ProdCheck?
- Can we do an efficient ZeroTest/ProdCheck on arbitrary subsets? Or do we crucially need the subgroup properties? 
- What is the role of the second polynomial variable ($Y$) in the prescribed permutation check?

## 📚 Material
- [Lecture](https://youtu.be/A0oZVEXav24) ([Slides](https://zk-learning.org/assets/lecture5-2023.pdf))
- [PLONK arithmetization](https://hackmd.io/@jake/plonk-arithmetization)

## 📝 Notes
(to be filled **after** each subsession)