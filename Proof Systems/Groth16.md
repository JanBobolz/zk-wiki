---
aliases: []
tags: []
created: 2023-10-18
---
$\newcommand{\FF}{\mathbb{F}}$
$\newcommand{\ZZ}{\mathbb{Z}}$
$\newcommand{\idx}{\mathsf{i}}$
$\newcommand{\stm}{\mathsf{x}}$
$\newcommand{\wit}{\mathsf{w}}$

# Main Idea 
Groth16 can be viewed as an optimized variant of [[Pinocchio]] and is designed for [[Pinocchio#QAP]] relation. On a high-level, Groth16 manages to reduce the number of proof elements to 3 by stashing the following additional elements to $A,B,C$: 
  1. $D$ i.e. a commitment to the $h$ polynomial.  
  2. $A',B',C'$ commitments to $A^{\alpha_u}, B^{\alpha_v}, C^{\alpha_w}$.
  3. $E$ i.e. a commitment to $(ABC)^\beta$

# Dropping $D$ 
Dropping $D$, the prover now has to push $ht$ to the $C$ element i.e. $$C = [\sum_{i=0}^m a_i w_i {\color{red}+ ht}]_1$$

# Dropping $A',B'$
Dropping $A'$, we should add $\beta$ (roughly corresponding to $\alpha_u$ in Pinocchio) to $B$ and $\sum_i a_i \beta u_i$ to $C$, so that the pairing equation $e(A,B)=e(C,[1]_2)$ also guarantees that $A$ is in the linear span of $u_i$. 
Likewise, to force $B$ is in the span of $v_i$ we also add $\alpha$ to $A$ and $\sum_i a_i \alpha v_i$ to $C$.
Now the pairing equation should be slightly adjusted: $e(A,B)=e(C,[1]_2) \cdot e({\color{red}[\alpha]_1},{\color{blue}[\beta]_2})$, where honestly computed $A$, $B$, and $C$ are

$$ A = [\sum_{i=0}^m a_i u_i {\color{red}+\alpha}]_1$$
$$ B = [\sum_{i=0}^m a_i v_i {\color{blue}+\beta}]_2$$
$$ C = [\sum_{i=0}^m a_i w_i {\color{blue}+\sum_{i=0}^m a_i \beta u_i} {\color{red}+\sum_{i=0}^m a_i \alpha v_i} +ht]_1$$

# Dropping $C',E$ 
Recall that $E$ in Pinocchio essentially forces the prover to apply an identical linear transformation $T=(a_0,\ldots,a_m)$ to $(u_0,\ldots,u_m)$, $(v_0,\ldots,v_m)$, and $(w_0,\ldots,w_m)$.
In Groth16, this is achieved by having an honest prover compute the above $C$ divided by an additional element $\delta$, while the only CRS involving $\delta^{-1}$ are $[(w_i + \beta u_i +\alpha v_i)\delta^{-1}]_1$ and  $[(x^i t)\delta^{-1}]_1$.
This constraint also puts $C$ in the correct linear span, previously guaranteed by $C'$. 
The pairing equation should be slightly adjusted again: $e(A,B)=e(C,[{\color{yellow}\delta}]_2) \cdot e([\alpha]_1,[\beta]_2)$, where honestly computed $C$ is


$$ C = [(\sum_{i=0}^m a_i w_i +\sum_{i=0}^m a_i \beta u_i +\sum_{i=0}^m a_i \alpha v_i+ht){\color{yellow}\delta^{-1}}]_1$$

# The Protocol
> [!protocol] 
> - **Setup** $G(u_i,v_i,w_i,t)$: Output a CRS $\sigma$ containing
>   - $[x^i]_1$ for $i=1,\ldots,d-1$
>   - $[x^i]_2$ for $i=1,\ldots,d-1$
>   - $[(x^i t)\delta^{-1}]_1$ for $i=0,\ldots,d-2$
>   - $[\alpha]_1, [\beta]_2, [\delta]_2$
>   - $[ (\beta u_i + \alpha v_i+w_i)\delta^{-1}]_1$ for $i=0,\ldots,m$
> - **Prove**: $P(\sigma,(a_i)_{i\in [0,m]})$ outputs $\pi = (A,B,C)$ where
>   - $A = [\sum_{i=0}^m a_i u_i + \alpha]_1$
>   - $B = [\sum_{i=0}^m a_i v_i + \beta ]_2$
>   - $C = [\left(\sum_{i=0}^m a_i w_i + \alpha (\sum_{i=0}^m a_i v_i) + \beta (\sum_{i=0}^m a_i u_i \right)+ht)\delta^{-1}]_1$
> - **Verify**: $V(\sigma,\pi)$ checks
>   $$e(A,B) = e(C,[\delta]_2) \cdot e([\alpha]_1, [\beta]_2)$$ 

# Groth16 with public inputs
Analogously to [[Pinocchio#Pinocchio with public inputs]], we can adjust the above to full-fledged Groth16 for QAP relation. 
> [!protocol] 
> - **Setup** $G(u_i,v_i,w_i,t)$: Output a CRS $\sigma$ containing
>   - $[x^i]_1$ for $i=1,\ldots,d-1$
>   - $[x^i]_2$ for $i=1,\ldots,d-1$
>   - $[(x^i t)\delta^{-1}]_1$ for $i=0,\ldots,d-2$
>   - $[\alpha]_1, [\beta]_2, {\color{red}[\gamma]_2}, [\delta]_2$
>   - $\color{red} [ (\beta u_i + \alpha v_i+w_i)\gamma^{-1}]_1$ for $i=0,\ldots,\ell$
>   - $[ (\beta u_i + \alpha v_i+w_i)\delta^{-1}]_1$ for $i=\ell+1,\ldots,m$
> - **Prove**: $P(\sigma,(a_i)_{i\in [0,m]})$ outputs $\pi = (A,B,C)$ where
>   - $A = [\sum_{i=0}^m a_i u_i + \alpha]_1$
>   - $B = [\sum_{i=0}^m a_i v_i + \beta ]_2$
>   - $C = [\left(\sum_{\color{red}i=\ell+1}^m a_i w_i + \alpha (\sum_{\color{red} i=\ell+1}^m a_i v_i) + \beta (\sum_{\color{red} i=\ell+1}^m a_i u_i \right)+ht)\delta^{-1}]_1$
> - **Verify**: $V(\sigma,(a_i)_{i\in[0,\ell]},\pi)$ computes the public component for $C$ as
>   $$\color{red} C_{\text{pub}} = [\left(\sum_{i=0}^\ell a_i w_i + \alpha (\sum_{i=0}^\ell a_i v_i) + \beta (\sum_{i=0}^\ell a_i u_i \right)+ht)\gamma^{-1}]_1$$ 
> and checks
>   $$e(A,B) = {\color{red}e(C_{\text{pub}},[\gamma]_2)\cdot} e(C,[\delta]_2) \cdot  e([\alpha]_1, [\beta]_2)$$

# Groth16 with zero knowledge
Towards ZK, we randomize the two proof elements $A$ and $B$, and perturb $C$ to retain correctness.

> [!protocol] 
> - **Setup** $G(u_i,v_i,w_i,t)$: Output a CRS $\sigma$ containing
>   - $[x^i]_1$ for $i=1,\ldots,d-1$
>   - $[x^i]_2$ for $i=1,\ldots,d-1$
>   - $[(x^i t)\delta^{-1}]_2$ for $i=0,\ldots,d-2$
>   - $[\alpha]_1, {\color{red} [\beta]_1},[\beta]_2, [\gamma]_2, {\color{red} [\delta]_1},[\delta]_2$
>   - $[ (\beta u_i + \alpha v_i+w_i)\gamma^{-1}]$ for $i=0,\ldots,\ell$
>   - $[ (\beta u_i + \alpha v_i+w_i)\delta^{-1}]$ for $i=\ell+1,\ldots,m$
> - **Prove**: $P(\sigma,(a_i)_{i\in [0,m]})$ outputs $\pi = (A,B,C)$ where
>   - $A = [\sum_{i=0}^m a_i u_i + \alpha + {\color{red} r \delta}]_1$
>   - $B = [\sum_{i=0}^m a_i v_i + \beta  + {\color{red} s \delta}]_2$
>   - $C = [\left(\sum_{i=\ell+1}^m a_i w_i + \alpha (\sum_{i=\ell+1}^m a_i v_i) + \beta (\sum_{i=\ell+1}^m a_i u_i \right)+ht)\delta^{-1} {\color{red}+ r s\delta}]_1 {\color{red} \cdot A^s \cdot 
 B^r}$
> - **Verify**: $V(\sigma,(a_i)_{i\in[0,\ell]},\pi)$ computes the public component for $C$ as
>   $$C_{\text{pub}} = [\left(\sum_{i=0}^\ell a_i w_i + \alpha (\sum_{i=0}^\ell a_i v_i) + \beta (\sum_{i=0}^\ell a_i u_i \right)+ht)\gamma^{-1}]_1$$ 
> and checks
>   $$e(A,B) = e(C_{\text{pub}},[\gamma]_2)\cdot e(C,[\delta]_2) \cdot  e([\alpha]_1, [\beta]_2)$$

Since $A$ and $B$ are now masked by independently chosen $r,s$, we can easily perform ZK simulation by sampling uniform $A,B$ and set $C$ such that they meet the verification condition.

> [!algorithm] Simulator $S$
> - **SimSetup** $S_0(u_i,v_i,w_i,t)$
>   - Run $\sigma\gets G(u_i,v_i,w_i,t)$. Output $\sigma$ and a simulator trapdoor $\tau=(\alpha,\beta,\delta)$
> - **SimProof** $S_1((a_i)_{i\in[1,m]})$
>   - Sample uniform $a,b\in\ZZ_q$ and set $A=[a]_1,B=[b]_2$
>   - Set $C = [(ab-\alpha\beta - \sum_{i=0}^\ell a_i (w_i + \alpha v_i + \beta u_i))\delta^{-1}]_1$
>   - Output $\pi=(A,B,C)$


# Groth16 is re-randomizable
It is easy to see that Groth16 is re-randomizable i.e. given $\pi=(A,B,C)$ for a statement $\stm$ one can generate a valid proof $\pi'$ for the same $\stm$. 
There are essentially two ways to maul the proof:

1. Set $A'=A^\rho$, $B'=B^{-\rho}$ for some $\rho$, output $\pi'=(A',B',C)$.
2. Set $B'=B[\rho\delta]_2$ and $C'=A^\rho C$, output $\pi'=(A,B',C')$.

It is still possible to show that forging Groth16 proof is hard for $\stm'\neq\stm$ i.e. it is weakly non-malleable (see [BKSV21](https://eprint.iacr.org/2020/811.pdf)).
To obtain strong non-malleability, Groth16 can be modified to [Groth-Maller proof](https://eprint.iacr.org/2017/540.pdf).
