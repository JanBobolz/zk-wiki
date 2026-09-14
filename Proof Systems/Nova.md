---
aliases:
tags:
created: 2024-03-11
---
Introduces an [[Incrementally Verifiable Computation|IVC]] using [[Folding Scheme|folding]] of [[R1CS and QAPs (Rank 1 Constraint System and Quadratic Arithmetic Programs)|R1CS]] (made possible by [[Relaxed R1CS]]).

# Overview

At each step $i$, the prover needs to produce a SNARK proving it has correctly computed the SNARK for the output of step $i-1$, and a SNARK verifier circuit to verify the SNARK from step $i-1$. The folding scheme discussed in the paper introduces a way to reduce the problem of checking two NP instances into checking a single one. The approach does not require FFTs or a trusted setup, so it can be deployed.

The time for the prover ($O(|F|)$) and the verifier is $O(1)$, which is the fastest in the literature, and the size of the verifier circuit is $O(|F|)$ group elements, but with a variant of an existing zkSNARK (Spartan), we can prove it succinctly in $O(\log |F|)$.

# Runtime and Memory Benefits

Nova's approach to IVC achieves the smallest verifier circuit in the literature. Since the verifier's cost in the non-interactive version of the folding scheme for relaxed R1CS is $O_\lambda(1)$, the size of the computation that Nova's prover proves at each incremental step is $\approx |F|$, assuming $N$-sized vectors are committed with $O_\lambda(1)$-sized commitments (e.g., Pedersen's commitments). In particular, the verifier circuit in Nova is constant-sized, and its size is dominated by two group scalar multiplications.

Nova's prover can prove knowledge of a satisfying witness to the running relaxed R1CS instance in zero-knowledge, with an $O_\lambda(\log |F|)$-sized succinct proof, using a zkSNARK that we design.

# Construction of the NIFS (Non-Interactive Folding Scheme)



# The Difference

- Using a SNARK-based IVC approach is impractical, as stated by many works (). Since verifying a SNARK at each step, with or without trusted setup, is expensive asymptotically, as it needs to produce and verify SNARKs at each step.
- Verifier circuit is constant-sized.
- Although the folding scheme is weaker than any argument of knowledge.

# Steps

- At each incremental step, Nova proves that the current step was computed correctly, and instead of proving step $i-1$ like in general approaches to IVC, the prover takes the R1CS instance and folds it into a relaxed running R1CS instance.
- Nova also incorporates a variant of a zkSNARK because the IVC-based folding scheme folds the witnesses but does not hide the witnesses. So, to prove the validity of the IVC proofs in zero-knowledge, we use a zkSNARK. Zero-knowledge IVC has not yet been built directly, so we instead use, at the end of the process, an efficient zkSNARK to prove the result of what we folded through IVC.
- Since the IVC-based folding scheme is public-coin, we can make it non-interactive via Fiat-Shamir.
- We use committed relaxed R1CS to avoid losing zero-knowledge in the first step, since the prover was sending $W_1$ and $W_2$ in the relaxed R1CS version. So we treat $W$ and $E$ as witnesses to make this a different variant: committed relaxed R1CS.
- At each step, a valid IVC proof is a satisfying witness of the running relaxed R1CS instance, along with the instance. Nova can also prove, at each step, in zero-knowledge and succinctly, that it knows a valid IVC proof (i.e., a satisfying witness) to the running committed relaxed R1CS instance.

# Constructing IVC from a Folding Scheme

- An IVC scheme allows the prover to show that $z_n = F^{(n)}(z_0)$ for initial input $z_0$ and output $z_n$. $F$ is a non-deterministic, polynomial-time computable function.
- $F'$ is an augmented circuit which does two things: computes the next step, and verifies the proof of all previous step computations.
- Intuition: $F'$ takes as non-deterministic advice two committed relaxed R1CS instances, $u_i$ and $U_i$. Here, $U_i$ represents the correct execution of the first $i-1$ invocations of $F'$, while $u_i$ represents the correct execution of the $i$-th invocation of $F'$.
- First step — $F'$ has $u_i$, which contains $z_i$, to compute $z_{i+1} = F'(z_i)$.
- Second step — $F'$ invokes the verifier of NIFS to fold the task of checking $u_i$ and $U_i$ into $U_{i+1}$.
- Then the IVC prover computes a new instance $u_{i+1}$, which attests to the correctness of $z_{i+1} = F'(z_i)$ and that $U_{i+1}$ is the correct fold of $u_i$ and $U_i$. The folding of $u_i$ and $U_i$ is the running relaxed R1CS instance discussed earlier, so at each step, instead of checking the previous $i-1$ correct invocations and the $i$-th step's computation separately, we fold them together and compute the next step's computation, and so on until the end.
- At the end, we use a zkSNARK to prove that $U_{i+1}$ (which becomes $U_i$ for the next part) correctly contains the executions up to $i$, which will be checked together.
- The IVC proof $\Pi_{i+1}$ is $((U_{i+1}, W_{i+1}), (u_{i+1}, w_{i+1}))$. Succinctness is maintained by the properties of the folding scheme.

# About Compression of the IVC Proofs

We leverage the fact that $\Pi$ contains two committed relaxed R1CS instance-witness pairs. So, $P$ first folds the instance-witness pairs $(u, w)$ and $(U, W)$ in $\Pi$ to produce a folded instance-witness pair $(U', W')$, using NIFS.P. Next, $P$ runs zkSNARK.P to prove that it knows a valid witness for $U'$. For zero-knowledge to hold, we need $\Pi$ to be honestly randomized.