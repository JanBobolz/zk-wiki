---
aliases: []
tags: [session]
created: 2023-05-03
---

**🪑 Session chair:** Akira/Jan
<small>(Duties: Read material above-average carefully 🤓; prepare fallback discussions/questions (worst-case: just prepare some quiz questions about the material) 🙋. Prepare a few slides to guide the session through subtopics (this is <i>not</i> supposed to be a <i>detailed</i> summary of the material)</small>

**✍️ Notetaker:** Jan 
<small>(Duties: Take Obsidian notes during the session 📝. Moderate to get input for the wiki pages 🧠. Make people summarize / dumb down discussion results to keep things comprehensible for everyone 🧑‍⚖️.)</small>

## 🎯 Goals
First, this is to kick off the reading group. We want to discuss important notions, and get everyone up to speed with the influential "roots" of ZK proofs.

We want to understand
- Basic definitions of [[Interactive Protocol|the IP complexity class]], [[Soundness]]/[[Proof of Knowledge]], and [[Zero Knowledge]] (simulation paradigm).
- [[Cut-and-Choose]] protocols (e.g., 3-colorable, Quadratic Residue, Graph Isomorphism).
- [[Fiat-Shamir]].

## 📚 Material
- [Lecture](https://www.youtube.com/watch?v=uchjTIlPzFo) ([slides](https://zk-learning.org/assets/Lecture1-2023-slides.pdf))

Additional (optional) material can be found on https://zk-learning.org.

## 📝 Notes
### Session 1.1 (2023-06-01): Basic definitions
Mostly talked about [[Soundness]]/[[Proof of Knowledge|Proof of Knowledge]] and [[Zero Knowledge]].

#### Interactive Proofs
Let $L$ be a language. An *interactive proof system* for $L$ is a tuple of interactive algorithms Prover $P$ and Verifier $V$, that proceed as follows. 

1. $P$ and $V$ take a *statement* $x$ as common input. 
2. For each round $i=1,\ldots,r$: $P$ sends a message $a_i$ and $V$ responds with $q_i$.
3. At the end of interaction, $V$ outputs $1$ ("accept") or $0$ ("reject")

Typically, $V$ has limited computational power, whereas $P$ is unbounded. 

> [!question] Could the number of rounds $r$ be arbitrary? 
> We typically assume $r$ to be a constant. Some protocols have $r\in\log(|x|)$.

#### Completeness
We say a proof system $(P,V)$ is *complete* if $V$ accepts after both $P$ and $V$ follow the protocol honestly. More formally:
If $x\in L$, then $\Pr[(P,V)(x)=1]=1$.

#### Soundness
Consider a scenario where $P$ is potentially malicious, i.e., $P$ tries to convince $V$ that the statement $x$ belongs to $L$, even if $x\notin L$. Soundness guarantees that, as long as $x\notin L$ then $V$ always rejects no matter what a cheating prover does. Formally: 

If $x\notin L$, then $\forall P^*$, 
$$\Pr[(P^*,V)(x)=1]=\mathsf{negl}(|x|)$$

> [!question] What does $*$ mean? 
> We often indicate an algorithm $A^*$ is adversarial.    

#### Zero Knowledge
Now we consider a scenario where $V$ is malicious, i.e., $V$ may potentially deviate from the protocol to obtain some extra information than the fact that $x\in L$. Zero Knowledge guarantees that, no malicious $V^*$ can gain useful information while interacting with honest $P$.

Formally: 

If $x\in L$, then $\forall V^*$, $\exists S$ such that 
$$\textrm{view}_{V^*}((P,V^*)(x))\approx S(x)$$

where $\textrm{view}_{V^*}((P,V^*)(x))$ denotes the distribution of strings that $V^*$ observes during the execution of $(P,V^*)(x)$, i.e., all incoming messages to and outgoing messages from $V^*$. 

> [!question] What is the role of simulator $S$?
> Intuitively, the existence of simulator implies that the information $V^*$ gains from a protocol execution could be efficiently computed by $V^*$ itself from $x$. This means that $V^*$ has learned "nothing new" by interacting with $P$.

> [!question] If $V^*$ can simulate a protocol execution, why can't $V^*$ run a simulator to convince another verifier? Doesn't ZK condradict Soundness?
> Although it sounds somewhat paradoxical, the existence of simulator doesn't mean that one can play an honest prover. This is because a simulator may cheat by generating a transcript in the reverse order or by exploiting the knowledge of secret trapdoor. 

#### Proof of Knowledge
In some applications, plain soundness may not be satisfactory. Say you as a user want to authenticate yourself to the web service by proving that you *know* secret identity information. 

To this end, let us assume that $L$ is an NP language and consider the corresponding NP relation $R_L = \{(x,w) : |w| \leq \text{poly}(|x|) \land x\in L\}$, where the string $w$ is called *witness*. In a proof system for $R$, $P$ takes $w$ as private input, $P,V$ take $x$ as common input, and they interact, denoted by $(P(w),V)(x)$. 

We say a proof system for $R_L$ is *Proof of Knowledge (PoK)* if the following condition is satisfied: if $V$ accepts on statement $x$, then $P^*$ must have known the corresponding witness $w$.

Formally, $\exists E$ such that $E$ is PPT and $\forall P^*$, $\forall x\in L$, 
$$\Pr[b=1 \land (x,w)\notin R_L \;|\; b\gets(P^*,V)(x); w\gets E^{P^*}(x)] = \mathsf{negl}(|x|)$$

> [!question] What does $E^{P^*}$ mean? 
> It means that an extractor $E$ has black-box access to $P^*$. This implies that $E$ can [[Rewinding|rewind]] $P^*$ to its previous state. 


### Session 1.2 (2023-06-15): Example protocols
We will revisit the definitions and look at examples to understand the basic mechanisms and tricks better.

> [!info] I'll upload the material soon


### Session 1.3 (2023-06-22): More example protocols
For real this time: let's go through the example protocols and understand how they work. 