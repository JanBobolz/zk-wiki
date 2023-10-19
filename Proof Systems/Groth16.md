---
aliases: []
tags: []
created: 2023-10-18
---
$\newcommand{\FF}{\mathbb{F}}$
$\newcommand{\idx}{\mathsf{i}}$
$\newcommand{\stm}{\mathsf{x}}$
$\newcommand{\wit}{\mathsf{w}}$

# Main Idea 
Groth16 can be viewed as an optimized variant of [[Pinocchio]] and is designed for [[Pinocchio#QAP]] relation. On a high-level, Groth16 manages to reduce the number of proof elements to 3 by stashing the following additional elements to $A,B,C$: 
  1. $D$ i.e. a commitment to the $h$ polynomial separately.  
  2. $A',B',C'$ commitments to $A^{\alpha_u}, B^{\alpha_v}, C^{\alpha_w}$.
  3. $E$ i.e. a commitment to $(ABC)^\beta$

# Dropping $D$ 
Dropping $D$, the prover now has to push $ht$ to the $C$ element i.e. $$C = [\sum_{i=0}^m a_i w_i + ht]_1$$

# Dropping $A',B',C'$ 

# Dropping $E$ 

# The Protocol
> [!protocol] 
> - **Setup** $G(u_i,v_i,w_i,t)$: Output a CRS $\sigma$ containing
>   - $[x^i]_1$ for $i=1,\ldots,d-1$
>   - $[x^i]_2$ for $i=1,\ldots,d-1$
>   - $[(x^i t)\delta^{-1}]_2$ for $i=0,\ldots,d-2$
>   - $[\alpha]_1, [\beta]_2, [\delta]_2$
>   - $[ (\beta u_i + \alpha v_i+w_i)\delta^{-1}]$ for $i=0,\ldots,m$
> - **Prove**: $P(\sigma,(a_i)_{i\in [0,m]})$ outputs $\pi = (A,B,C)$ where
>   - $A = [\sum_{i=0}^m a_i u_i + \alpha]_1$
>   - $B = [\sum_{i=0}^m a_i v_i + \beta ]_2$
>   - $C = [\left(\sum_{i=0}^m a_i w_i + \alpha (\sum_{i=0}^m a_i v_i) + \beta (\sum_{i=0}^m a_i u_i \right)+ht)\delta^{-1}]_1$
> - **Verify**: $V(\sigma,\pi)$ checks
>   $$e(A,B) = e(C,[\delta]_2) \cdot e([\alpha]_1, [\beta]_2) \cdot $$ 

# Groth16 with public inputs
Analogously to [[Pinocchi#Pinocchio with public inputs]], we can adjust the above to full-fledged Groth16 for QAP relation. 
> [!protocol] 
> - **Setup** $G(u_i,v_i,w_i,t)$: Output a CRS $\sigma$ containing
>   - $[x^i]_1$ for $i=1,\ldots,d-1$
>   - $[x^i]_2$ for $i=1,\ldots,d-1$
>   - $[(x^i t)\delta^{-1}]_2$ for $i=0,\ldots,d-2$
>   - $[\alpha]_1, [\beta]_2, [\gamma]_2, [\delta]_2$
>   - $[ (\beta u_i + \alpha v_i+w_i)\gamma^{-1}]$ for $i=0,\ldots,\ell$
>   - $[ (\beta u_i + \alpha v_i+w_i)\delta^{-1}]$ for $i=\ell+1,\ldots,m$
> - **Prove**: $P(\sigma,(a_i)_{i\in [0,m]})$ outputs $\pi = (A,B,C)$ where
>   - $A = [\sum_{i=0}^m a_i u_i + \alpha]_1$
>   - $B = [\sum_{i=0}^m a_i v_i + \beta ]_2$
>   - $C = [\left(\sum_{i=\ell+1}^m a_i w_i + \alpha (\sum_{i=\ell+1}^m a_i v_i) + \beta (\sum_{i=\ell+1}^m a_i u_i \right)+ht)\delta^{-1}]_1$
> - **Verify**: $V(\sigma,(a_i)_{i\in[0,\ell]},\pi)$ computes the public component for $C$ as
>   $$C_{\text{pub}} = [\left(\sum_{i=0}^\ell a_i w_i + \alpha (\sum_{i=0}^\ell a_i v_i) + \beta (\sum_{i=0}^\ell a_i u_i \right)+ht)\gamma^{-1}]_1$$ 
> and checks
>   $$e(A,B) = e(C_{\text{pub}},[\gamma]_2)\cdot e(C,[\delta]_2) \cdot  e([\alpha]_1, [\beta]_2) \cdot $$ 