---
aliases: []
tags: [session]
created: 2023-07-18
---

**🪑 Session chair:** Jan
<small>(Duties: Read material above-average carefully 🤓; prepare fallback discussions/questions (worst-case: just prepare some quiz questions about the material) 🙋. Prepare a few slides to guide the session through subtopics (this is <i>not</i> supposed to be a <i>detailed</i> summary of the material)</small>

**✍️ Notetaker:** ?
<small>(Duties: Take notes during the session, push them to the wiki afterwards 📝. Moderate to get input for the wiki pages 🧠. Make people summarize / dumb down discussion results to keep things comprehensible for everyone 🧑‍⚖️.)</small>

## 🎯 Goals
This session covers FRI-based polynomial commitments. We will particularly focus on the following contents from Lecture 7 (see [[#📚 Material]]). 

The main goal is to understand what FRI-based polynomial commitment schemes are, what they offer, and how they work.

## ❓ Quiz Questions
- How does the code rate $\rho$ affect (effect, even) the tradeoff between prover work and verifier work?
- Why do we use the [[Reed-Solomon]] code at all? Why not just set $\rho = 1$? 
- What's the requirement on the field $\mathbb{F}$? Why do we like Goldilocks fields like $\mathbb{F}_p$ with $p = t^2-t+1$?
- Because we only check *proximity* of the committed code to some low-degree polynomial, the prover may be able to equivocate the committed polynomial, choosing to evaluate $f(u)$ for any polynomial $f$ close enough to the committed code word (there may be multiple if $\rho$ is small). Why, intuitively, is that not an issue in, say, [[PLONK]]?
- What's the main idea behind the folding? 


## 📚 Material
- [Lecture](https://youtu.be/A3edAQDPnDY) ([Slides](https://zk-learning.org/assets/lecture8.pdf))
	- Watch 26:18 until 1:37:00
		- The Fiat-Shamir part and the overview part is not relevant to the discussion of this session, you can skip it if you're short on time.
- https://eprint.iacr.org/2019/1020 or https://drops.dagstuhl.de/opus/volltexte/2018/9018/pdf/LIPIcs-ICALP-2018-14.pdf as additional material, should the lecture video not provide all context.

## 📝 Notes
### Session (Oct 24)
