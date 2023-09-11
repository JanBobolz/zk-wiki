---
aliases: []
tags: [session]
created: 2023-07-18
---

**🪑 Session chair:** Akira
<small>(Duties: Read material above-average carefully 🤓; prepare fallback discussions/questions (worst-case: just prepare some quiz questions about the material) 🙋. Prepare a few slides to guide the session through subtopics (this is <i>not</i> supposed to be a <i>detailed</i> summary of the material)</small>

**✍️ Notetaker:** ?
<small>(Duties: Take notes during the session, push them to the wiki afterwards 📝. Moderate to get input for the wiki pages 🧠. Make people summarize / dumb down discussion results to keep things comprehensible for everyone 🧑‍⚖️.)</small>

## 🎯 Goals
This session covers detailed syntax and security properties of discrete logarithm-based polynomial commitments. We will particularly focus on the following contents from Lecture 6 (see [[#📚 Material]]). 

1. Security of KZG
    - Binding, evalution binding, knowledge soundness, hiding
    - Why KZG is evaluation binding under the $d$-SDH assumption
    - Why KZG is knowledge sound under the $d$-KoE assumption
    - How to make KZG hiding
    - Ceremony for distributed generation of SRS
    - Batch proof: (1) single polynomial, many evaluations, (2) many polynomials, many evaluations

2. Bulletproofs-style transparent polynomial commitments
    - The split-and-fold paradigm
    - Tree-based special soundness
    - Hyrax, Dory, Dark

## ❓ Quiz Questions
- Why is the $q$-power knowledge of exponent assumption true in GGM?
- Why $\hat{c}=g^{\alpha f(\tau)}$ can be dropped if we are to prove knowledge soundness of KZG directly in the GGM?
- If the KZG commitment is randomized i.e. $c=g^{f(\tau)+r\eta}$, what does the verification operatoin look like? How many evaluations can be revealed to prove ZK? 
- What does the complete protocol for batch opening look like?
- Does Bulletproofs-PCom satisfy evaluation binding?
- What's the intuition of knowledge soundness of Bulletproofs-PCom?

## 📚 Material
- [Lecture](https://youtu.be/WyT5KkKBJUw) ([Slides](https://zk-learning.org/assets/lecture6.pdf))

## 📝 Notes
### Subsession 1 (September 12)
