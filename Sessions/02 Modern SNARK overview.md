---
aliases: []
tags: [session]
created: 2023-05-03
---

**🪑 Session chair:** Akira
<small>(Duties: Read material above-average carefully 🤓; prepare fallback discussions/questions (worst-case: just prepare some quiz questions about the material) 🙋. Prepare a few slides to guide the session through subtopics (this is <i>not</i> supposed to be a <i>detailed</i> summary of the material)</small>
**✍️ Notetaker:** Jan/Akira 
<small>(Duties: Take Obsidian notes during the session 📝. Moderate to get input for the wiki pages 🧠. Make people summarize / dumb down discussion results to keep things comprehensible for everyone 🧑‍⚖️.)</small>

## 🎯 Goals
This session serves as a bird's-eye overview of recent (ZK)SNARKs and real-world applications. We will particularly focus on the following contents from Lecture 2 (see [[#📚 Material]]). 

1. SNARK overview
    - Parse "Succinct Non-interactive ARgument of Knowledge"
    - Blockchain applications
    - Example: SNARK for correctly processed photos

2. Paradigm of building SNARKs
    - IOP + FCOM = SNARK
    - [[Schwartz-Zippel]] lemma
    - Example: Poly-IOP for equality test
    - Example: Poly-IOP for set inclusion test

## ❓ Quiz Questions
You are encouraged to think about the following questions before the session starts. 

1. In which blockchain applications, SNARK without ZK is sufficient? 
2. What are the statement and witness in the photo-processing example?
    - What kind of attack would be possible if the signature is missing?
3. Why is preprocessing useful? 
4. In the equality test protocol, what happens if the prover doesn't respect the degree bound?
5. In the set inclusion test protocol, what happens if the oracle $q$ sent by the prover is not a polynomial?


## 📚 Material
- [Lecture](https://youtu.be/bGEXYpt3sj0) ([slides](https://zk-learning.org/assets/Lecture2-2023.pdf))
Additional (optional) material can be found on https://zk-learning.org.

## 📝 Notes
### Subsession 1 (June 29)
#### How big is a witness usually? 
- For $\mathbf{NP}$ languages, per definition, the witness is at most polynomial length in the statement.
- If witnesses are $\mathcal{O}(\log(|x|))$ length, then the language is in $\mathbf{P}$ (since then you can just try out all $\mathcal{O}(|x|)$ potential witnesses in polynomial time and decide the language with that). 
	- We can do [[Zero Knowledge|ZK]], [[Proof of Knowledge|PoK]] proofs for languages in $\mathbf{P}$, but they're *trivial*: The extractor doesn't need the help of the prover to compute a witness, and the ZK simulator can just compute a witness and run the protocol honestly.
	- (Concretely, *maybe* you still want to compress the witness with a [[SNARG]] if the constants hidden in the $\mathcal{O}$ notation are large, but complexity-theoretically, nothing interesting is happening here)
- Witnesses should *usually* be thought of as poly size. 
- A [[SNARK]] effectively compresses a witness from poly length to polylog length (or even to constant length) using computational assumptions.
	- Note that "polylog or constant length" here means $\mathcal{O}(\mathit{polylog}(|x|) \cdot \mathit{poly}(\lambda))$ or $\mathcal{O}(\mathit{poly}(\lambda))$, i.e. a security parameter is involved and ensures that in practice, I cannot just try out all possible SNARK proofs in order to gain insight about a statement's validity (in contrast to what we argued above regarding log-length witnesses).

#### Blockchain rollups
Statement $x = (\mathit{previousState}, \mathit{newState})$, witness $w = \mathit{txData}$, relation $$\mathcal{R} = \{((\mathit{previousState}, \mathit{newState}), \mathit{txData}):\ \text{applying transactions in }\mathit{txData}\text{ to }\mathit{previousState}\text{ yields } \mathit{newState}\}$$
#### Photo misinformation
Statement $x = (\mathit{editedPhoto},\mathit{appliedOps}, \mathit{vk})$, witness $w = (\mathit{originalPhoto}, \sigma)$, relation $$\mathcal{R} = \{((\mathit{editedPhoto},\mathit{appliedOps}, \mathit{vk}), (\mathit{originalPhoto}, \sigma)):\ \text{photo edited correctly and }\sigma \text{ is valid signature on }\mathit{originalPhoto} \text{ under }\mathit{vk}\}$$
- Enables a short proof of photo validity (e.g., only cropped, scaled) instead of having to download/check the full photo to check the signature $\sigma$.
- May also be nice to prove things in [[Zero Knowledge|ZK]], e.g., "I've only applied some face blur, otherwise the photo is the original", where we wouldn't want to reveal the unedited photo at all (and hence need [[Zero Knowledge|ZK]]). 

#### Why is preprocessing useful?
- First: need some sort of setup for *security*. 
	- Simulator/extractor need to be able to "cheat" somehow since normal users shouldn't be able to simulate or extract. 
	- For interactive proofs ([[01 History and Basics]]), we gave the simulator the ability to "compute the protocol messages out of order" and the extractor was allowed to "ask the prover multiple questions by [[Rewinding]]". 
	- Intuitively, this doesn't fly anymore for non-interactive proofs like [[SNARK]]s because there is no interaction; the prover just sends the proof to the verifier ^[in a way, we can cheat back interaction even for non-interactive proofs by looking at the prover's interaction with a [[Random Oracle]], see [[Fiat-Shamir]]].
- Second: *Someone* trusted by the verifier needs to read the full statement at some point. 
	- If the verifier does it themselves, then the verifier's runtime is at least linear in the statement size, which we want to avoid (though this is the case in [[Bulletproof]]s, for example). 
	- The preprocessing step is the step where a trusted party reads and "summarizes" the statement for the verifier, which makes the verifier very efficient, not even having to read the statement.

> [!question] Preprocessing vs transparent setup
> Who reads the full statement/circuit in a [[Transparent Setup]] or [[Random Oracle|ROM]]-setup scenario? 


#### IOPs
We talked about [[Interactive oracle proof|IOP]]s, trying to understand what they're about. Quick summary:
It's like an interactive protocol, but instead of sending a message, the prover sends a "box" that contains some function $f$. The verifier can then query the box for $f(x)$ for some $x$ of its choice.

> [!caution] Why not just have the verifier send $x$ and the prover respond with $f(x)$?
> A crucial component for [[Interactive oracle proof|IOP]]s is that the prover first *commits* to the function $f$ before seeing what question $x$ the verifier is going to ask. 
> Indeed, when instantiating the [[Interactive oracle proof|IOP]], we will replace the "box" with a [[Functional Commitment]], ensuring that the prover cannot change its mind about $f$ after seeing $x$.

### Subsession 2 (July 6)
Plan: A closer look at the toy example for some of the IOP techniques.