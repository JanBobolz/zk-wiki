---
aliases: []
tags: [session]
created: 2023-07-18
---

**🪑 Session chair:** Jan
<small>(Duties: Read material above-average carefully 🤓; prepare fallback discussions/questions (worst-case: just prepare some quiz questions about the material) 🙋. Prepare a few slides to guide the session through subtopics (this is <i>not</i> supposed to be a <i>detailed</i> summary of the material)</small>

**✍️ Notetaker:** Akira
<small>(Duties: Take notes during the session, push them to the wiki afterwards 📝. Moderate to get input for the wiki pages 🧠. Make people summarize / dumb down discussion results to keep things comprehensible for everyone 🧑‍⚖️.)</small>

## 🎯 Goals
This session covers code-based polynomial commitments. We will particularly focus on the following contents from Lecture 6 (see [[#📚 Material]]). 

1. (Linear) Error Correcting Codes
    - Definition
    - Reed-Solomon code

2. Polynomial commitment from error-correcting codes
    - Vec-Mat product commitment with $\sqrt{d}$ size proof
    - Proximity test and consistency check
    - Sketch of improvements for the proof size and prover time

## ❓ Quiz Questions
### General [[Reed-Solomon]] facts
1. What's the Reed-Solomon encoding of (5,0,0,0,...) ?
2. Why is Reed-Solomon a linear code?
3. What's the one-line proof that Reed-Solomon Codes $\mathbb{F}^k \rightarrow \mathbb{F}^n$ have minimum distance $\Delta = n-(k-1)$ ?
4. What does a "worst-case" pair of messages with minimum distance between them look like? (depending on the evaluation points $(\omega^i)_{i=1}^{n}$)
5. How many errors can I detect/correct? (Though we won't actually use any of this)
6. What kind of field $\mathbb{F}$ can we use? What's the advantage compared to the group/pairing commitment world?

### Polynomial Commitment from Reed-Solomon
1. What's the overall strategy to get square-root evaluation proof sizes? 
	- Prover commits to polynomial $f$. How?
	- Verifier wants to check that $f(u) = y$.
		- How is that statement rewritten/decomposed?
		- Which part does the prover compute, which part does the verifier evaluate?
2. Why not just [[Merkle-Tree]]-commit to the coefficients of the polynomial $f$? What's the benefit of Reed-Solomon-encoding $f$ before committing to it?
3. Why is it important for the verifier to check that the committed matrix is (close to) a commitment to a valid code word? (i.e. why do we need the proximity test?)
4. What are the chances that I can Merkle-Tree commit to a non-codeword in a way that's not detected during proximity testing? Is that an issue?
	- Replace some column with zeros (and be clever when asked for $\vec{r}\cdot \mathbf{M}$).
	- Swap two columns (and be clever when asked for $\vec{r}\cdot \mathbf{M}$).
	- Multiply a row with a constant (this is a stupid "attack" to include here. Why?)
5. Where's the cryptography in all this? What kind of assumptions do we have to make for the polynomial commitment scheme to work?
6. For the proximity test optimization of having the prover send the word ($r\cdot \mathrm{coefficientMatrix}$) instead of the code $r\cdot \mathsf{ReedSolomon}(\mathrm{coefficientMatrix})$, how does the verifier compute the code in time $\mathcal{O}(\sqrt{d})$ (or at least the values from the code he needs to check consistency with the committed columns)? (can he, actually? Slides do claim that runtime)

### Towards linear-time computable ECC-based polynomial commitments
- What's the issue with the Reed-Solomon-based $\sqrt(d)$ construction above? Regarding space and time?
- How do expander graphs correspond to a coding scheme?
		- How can this be generalized beyond $\mathbb{F}_2$? (I don't have an answer, I don't think it was explained in the material) 
- Can anyone summarize the strategy for getting the nice recursive codes?

## 📚 Material
- [Lecture](https://youtu.be/1S7ZjqG9uyI) ([Slides](https://zk-learning.org/assets/lecture7.pdf))

## 📝 Notes
### Subsession 1 (Oct 10)
