---
aliases: [ZK]
tags: []
created: 2023-05-03
---

The *Zero Knowledge* (*ZK*) property states that a verifier cannot learn anything about the prover's witness.

# High level intuition
How do we model that a (verifier) machine does not learn anything? We say "anything the machine could compute after participating in the protocol, it could also have computed without interacting with the prover at all".

We formulate this as "whatever the verifier observes can also be computed (with essentially the same probability distribution) by a simulator $S$, which only gets the public $x$ as input (not $w$)".

> [!warning] Intuition breakdown
> This intuition works very well in the interactive, classical setting, where we can actually say "the verifier could just as well have executed the simulator instead of interacting with the prover" (cf. [[Deniable Zero Knowledge]]). However, in many other settings, the practical verifier cannot actually execute the simulator (e.g., because simulation requires programming a [[Random Oracle]] or embedding a trapdoor into the [[Reference String|CRS]]).

# Concrete definitions
## Interactive, classical setting
- [[Zero Knowledge (Classical)|Micali-style ZK]] in standalone setting.
- [[Special Honest-Verifier Zero Knowledge]] best known as a special feature of [[Sigma Protocol]]s, simple but powerful enough to be generically upgraded to stronger (dishonest verifier) ZK notions.

> [!hint] Simulator advantage over the prover
> The simulator only has to output the post-hoc verifier view and can internally compute messages of the transcript in any order it likes (in contrast, the actual prover has go through the proof in the normal order). 

## Interactive, [[Reference String|CRS]] setting
- ...

## Non-interactive, [[Reference String|CRS]] setting
- ...

## Non-interactive, [[Random Oracle|ROM]] setting
- ...