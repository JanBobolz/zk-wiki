---
aliases: 
tags:
  - session
created: 2024-09-12
---

**🪑 Session chair:** Akira
<small>(Duties: Read material above-average carefully 🤓; prepare fallback discussions/questions (worst-case: just prepare some quiz questions about the material) 🙋. Prepare a few slides to guide the session through subtopics (this is <i>not</i> supposed to be a <i>detailed</i> summary of the material)</small>

## 🎯 Goals
- Good overview of the situation
	- what is the issue? 
	- how much of it can be generically remedied?
	- what are other strategies to deal with multi-round loss?

## 📚 Material
- [AFK23](https://link.springer.com/article/10.1007/s00145-023-09478-y): main reference
### Extensions
(if we have time)
- [Aggregation with LaBRADOR](https://eprint.iacr.org/2024/311): Sections 4, 5
- https://eprint.iacr.org/2024/904
- [CDS for multi-round](https://link.springer.com/chapter/10.1007/978-3-031-68400-5_12)

## 📝 Notes
### Sept 18
- Discussion about sanity check: sequential repetition of small-challenge-space protocols (e.g., some traditional [[Cut-and-Choose]] protocols)
	- Interactively, sequential repetition works for extraction. 
	- With Fiat-Shamir, sequential repetition does not amplify security at all (can grind challenges). 
	- Question: How is that reflected in [AFK23](https://link.springer.com/article/10.1007/s00145-023-09478-y)'s Theorem 2?
	- Answer: The soundness/knowledge error gets very bad very quickly when increasing the number of rounds

### Oct 16
