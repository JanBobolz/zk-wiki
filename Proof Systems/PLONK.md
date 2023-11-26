---
aliases: [plonkish]
tags: []
created: 2023-07-11
---

# Resources
- https://risencrypto.github.io/PLONKWHY/ discussion of some details

# Goal
The prover $P$ covinces the verifier $V$ that they know a correct witness $w$ such that $C(x,w)=0$ for public input $x$. 

# High-Level Structure
1. Preprocessing phase: Both $P$ and $V$ obtains preprocessed polynomials $S$ and $W$ representing the circuit constraints $C$. This step can be done offline before the input $x,w$ is known.
2. Encoding the input: Once the statement $x$ is known, both $P$ and $V$ construct a low-degree polynomial $v$ encoding $x$.
3. Committing to the witness: $P$ sends a witness-carrying polynomial $T$ encoding $x$, $w$,  and all the intermediate wire values while evaluating $C(x,w)$.
	- This commitment works in a gate-by-gate fashion, i.e. $T(y)$ is the left input, $T(\omega y)$ is the right input, $T(\omega^2 y)$ is the output of the gate $y$. This commitment is oblivious to the wiring.
4. $P$ and $V$ perform the following subprotocols:
    - Input Check: $T(y)-v(y)=0,\forall a\in\Omega_{inp}$ (realized by Zero Check on $\Omega$)
	    - Ensures that the input wires of $T$ are consistent with the input $x$.
    - Gate-by-Gate Check: $F(y):=S(y)(T(y)+T(\omega y)) + (1-S(y))T(y)T(\omega y)=T(\omega^2 y)$ (realized by Zero Check on $\Omega$)
	    - Ensures that each gate is correctly computed (left input wire _gate_ right input wire = output wire).
    - Wiring Check: $T(y)=T(W(y)),\forall y\in\Omega$ (realized by Prescribed Permutation Check on $T$, with prescribed permutation $W$ that encodes the circuit wiring)
	    - Ensures that $T$, which is quite redundant (contains each wire value multiple times, once for every time it plays an input/output role in some gate), is correct, i.e. the same wire contains the same value at each of its occurences.
	    - for example, if the wiring requires $T(y_1) = T(y_2) = T(y_3)$, say, $y_1$ is an output wire and $y_2$ and $y_3$ are input wires to some other gate, which are wired to $y_1$, then $W$ is a permutation that includes the cycle $(y_1, y_2, y_3)$, i.e. $W(y_1) = y_2, W(y_2) = y_3, W(y_3) = y_1$.

# Where is the magic? 
- The polynomial $T$ carries the entire "computatatinal traces" for $C(x,w)=0$ via Lagrange interpolation. It is strucuted elegantly in such as way the subsequent subprotocols can obtain any intermediate wire value by evaluating $T$ at a right position. 
- Input Check and Gate-by-Gate checks are rather straightforward.
- Wiring Check is the most involved part. One way to approach it: Understand relation between the circuit topology and permutation: Essentially, the subprotocol can make sure that the traces committed to in the polynomial $T$ are correctly positioned according to the circuit topology. It is easy to check that the circuit topology can be naturally described by some permutation e.g. the output of gate $1$ matches the left input of gate $2$, the output of gate $2$ matches the right output of gate $3$, etc. 


 
