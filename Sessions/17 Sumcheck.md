---
aliases: 
tags:
  - session
created: 2024-06-13
---
## 🎯 Goals
- Understand the Sumcheck protocol and underlying ideas
- Understand applications
- Relation to Bulletproofs

## 📚 Material
- [Five minute intuition (David Wong)](https://www.youtube.com/watch?v=XV62OB022tU)
- [Justin Thaler ZK MOOC lecture](https://youtu.be/4018OYyoAf8) ([Slides](https://zk-learning.org/assets/lecture4.pdf))
- [Jonathan Bootle lecture](https://youtu.be/TmSOC8FN2GE) (with lots of embedding into literature)
- [Paper: Sumcheck Arguments and their Applications](https://eprint.iacr.org/2021/333)
- [Thaler lecture notes](https://people.cs.georgetown.edu/jthaler/ProofsArgsAndZK.pdf) Chapters 3 & 4.
- [Chiesa Lecture with proofs](https://www.youtube.com/watch?v=N1-67VPrsbA)
- [GKR overview talk](https://youtu.be/x8pUxFptfb0?t=416)


Advanced:
- [Sublinear sumchecking paper](https://eprint.iacr.org/2024/816)
- HyperNova
- [A Note on the GKR Protocol](https://people.cs.georgetown.edu/jthaler/GKRNote.pdf) 
- [A Time-Space Tradeoff for the Sumcheck Prover](https://eprint.iacr.org/2024/524)
- Blendy ([talk](https://www.youtube.com/live/pS3REeCWMQ8?si=qPSqKqV4dI-pcjq0), [slides](https://drive.google.com/file/d/1SUguLkKis_xpiY3mgmKEsp0dg5BXM2Y7/view?pli=1))
- [The Sum-Check Protocol over Fields of Small Characteristic](https://eprint.iacr.org/2024/1046)

## 📝 Notes
### June 19
- Intro session: watching one of the intro talks on the topic together
	- https://youtu.be/4018OYyoAf8 from 45:00

### June 26
- Continue https://youtu.be/4018OYyoAf8?t=4402 (starting with linked timestamp)

### July 3
- Finish https://youtu.be/4018OYyoAf8?t=6425 (Circuit → IOP → Sumcheck)
- Discuss ideas
- Discuss next steps for reading group

### July 10
The lecture last week left did not explain how to check (using sumchecks) that a multivariate polynomial vanishes on the boolean hypercube, i.e. $f(x_1,\dots,x_n) = 0\ \forall \{0,1\}^n$ (this statement is interesting because $f$ is set up such that this statement is equivalent to all circuit constraints being fulfilled).
This session's goal is to find out how that actually works.

Material: 
- [Thaler lecture notes](https://people.cs.georgetown.edu/jthaler/ProofsArgsAndZK.pdf)  probably around Lemma 4.7 / Remark 4.4. We should probably just look at GKR at this point. 

For comedic effect, one might also ask ChatGPT about it, which has a very simple solution:
![[chatgpt-hypercube-vanish.png]]
To be fair, it's quite impressive that it understood the question (*"I was watching a lecture about sumchecks (as in SNARKs). The lecturer reduced circuit satisfiability to a certain polynomial vanishing on the boolean hypercube, and said that this can be checked with a sumcheck. How?"*), and it supplied me with lots of explanations of how sumchecks work (which seemed decent), but it really messed up actual answer.