---
aliases: 
tags:
  - session
created: 2024-03-19
---
## 🎯 Goals
Goal is to understand [[ProtoStar]].

## 📚 Material
- We're still following, roughly, [An incomplete guide to Folding](https://taiko.mirror.xyz/tk8LoE-rC2w0MJ4wCWwaJwbq8-Ih8DXnLUf7aJX1FbU), ProtoStar is Section 6
- https://eprint.iacr.org/2023/620 original paper
- [Presentation](https://youtu.be/wtxVYiZh7zc) ([slides](https://www.slideshare.net/AlexPruden/zkstudyclub-protostar-binyi-chen-benedikt-bnz-espresso-systems))
	- [Alternative](https://youtu.be/tt00TLFJPpc) (same talk, essentially, probably different discussion)

## 📝 Notes
### April 23, 30 sessions
First, going to watch [a presentation](https://youtu.be/wtxVYiZh7zc) about it together.

### May 7
Go through the paper together, answer questions, improve our understanding.

*Some example questions*
- What special case protocol makes ProtoStar equivalent to Nova?
- Let’s go through the compilation steps for a simple example protocol
- How does ProtoStar achieve *nonuniform* IVC?
- What do we optimize for when designing special sound protocols for the ProtoStar compiler?
	- Number of rounds? Messages? Length of messages? 
	- Sparsity?
	- Degree/number of verification equations?
- How/why does the lookup protocol work?