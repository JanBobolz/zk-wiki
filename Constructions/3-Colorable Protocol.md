---
aliases: []
tags: []
created: 2023-06-18
---

# Relation
$$\mathcal{R} = \{((V,E),f:V\to \{1,2,3\}) \mid \forall (v_i,v_j)\in E, f(v_i)\neq f(v_j)\}$$ 
That is, two adjacent nodes are assigned distinct colors. 

# Preliminaries: Commitment Scheme
Assume a commitment scheme $(\mathsf{Com},\mathsf{Ver})$ with computational hiding and perfect binding. 


# The main idea
The prover sends commitments to randomly permuted color assignments. Then the verifier asks for openings of two commitments corresponding to randomly chosen adjacent nodes and checks that two colors are distinct. 
Intuitively, ZK holds thanks to the secret random permutation and hiding of the commitment scheme. 
Soundness is guaranteed by binding: since at the time of committing the prover does not know which edge will be opened, i.e., if there exists some edge where the colors are the same, then with probability $1-1/|E|$ the prover gets caught. 

# The protocol
> [!protocol] Protocol between $P(V,E,f)$ and $V(V,E)$. Let $|V|=n$
> - **Commit**: Prover chooses a random permutation $\pi$ over $\{1,2,3\}$ and let $g = \pi \circ f$. Run $(c_i,d_i) = \mathsf{Com}(g(v_i))$ and send $c_i$ for all $v_i\in V$. 
> - **Challenge**: The verifier chooses a random challenge $e=(v_i,v_j)\gets E$ (challenging the prover to reveal the colors assigned to adjacent nodes $v_i$ $v_j$).
> - **Response**: The prover reveals $a_i=g(v_i)$ and $a_j=g(v_j)$ with decommitment strings $d_i$ and $d_j$.
> - The verifier checks that, $\mathsf{Ver}(c_i,a_i,d_i)=1$, $\mathsf{Ver}(c_j,a_j,d_j)=1$, $a_i,a_j\in\{1,2,3\}$ and $a_i\neq a_j$.

## The [[Zero Knowledge (Classical)|ZK]] property
This follows [[Zero Knowledge (Classical)#Typical simulator outline]]. 

The [[Zero Knowledge (Classical)]] simulator $S(x)$ for (malicious) verifier $V^*$ works as follows:
> [!algorithm] Simulator $S(V,E)$
> 1. $S$ computes a random transcript of the protocol:
> 	- $S$ chooses a random challenge $e=(v_i,v_j) \gets E$
> 	- $S$ chooses random colors $a_i,a_j\gets\{1,2,3\}$ such that $a_i\neq a_j$. Let $(c_i,d_i) = \mathsf{Com}(a_i)$ and $(c_j,d_j) = \mathsf{Com}(a_j)$. 
> 	- $S$ generates commitments $c_k$ to $1$ for $v_k\in V\setminus\{v_i,v_j\}$. 
> 2. $S$ rejects challenges $(v_i,v_j)$ not consistent with what $V^*$ would output:
> 	- $S$ chooses randomness $r$ and computes $e' = V^*(V,E;r;c_1,\ldots,c_n)$
> 	- If $e' = e$, then output the view $$(x,r,c_1,\ldots,c_n,a_i,a_j,d_i,d_j)$$ Otherwise, start from the beginning and try again. 

> [!info] 
> The expected running time of the above simulator is $|E|$.
> To show this formally, we need a hybrid argument. We only provide a sketch below. 
> In hybrid 1, we consider a modified simulator which behaves like the above simulator, except that all commitments are computed honestly using the knowledge of witness $f$. Since randomly picked challenge $e\in E$ is independent of $c_1,\ldots,c_n$, the probability that $e'=e$ is $1/|E|$. Hence, in hybrid 1, a modified simulator's expected running time is $|E|$ and its output is perfectly indistinguishable with the view of $V^*$ in the real protocol. 
> In hybrid 2, we replace the unopened commitments with commitments to a dummy string $1$, and $a_i,a_j$ of the opened commitments are randomly picked such that $a_i\neq a_j$. Now the behavior of the simulator is identical to the one described above. Thanks to compuatational hiding of the commitment scheme, $S$'s output is computationally indistinguishable with the modified simulator in hybrid 1. 

## The [[Proof of Knowledge (Classical)|PoK]] property
The knowldge extractor $\mathcal{E}^{P^*}(V,E)$ simply keeps rewinding $P^*$ and feeds $P^*$ with $e\in E$ without replacement. If $P^*$ fails to convince $V$ at any point, $\mathcal{E}$ terminates. If $\mathcal{E}$ runs out of all possible challenges in $E$, then it outputs $a_i$ for all $v_i\in V$ as a witness (i.e., the color assigments). 

### Running time of $\mathcal{E}$
Since $|E|<n^2$, the running time of $\mathcal{E}$ is at most $n^2$.

### Success probability of $\mathcal{E}$
$\mathcal{E}$ succeeds as long as $P^*$ sends valid responses to all possible $e\in E$ for a fixed set of commitments $c_1,\ldots,c_n$. This is because each $c_i$ can only be opened to a unique $a_i$ due to perfect binding and every adjacent $a_i,a_j$ are guaranteed to be distinct by the verification condition. To evaluate the success probability of $\mathcal{E}$, consider the matrix $M$ indexed by prover's random coin $\rho$ and challenge $e\in E$. We define $M$ such that $M(\rho,e)=1$ if $P^*$ with random coin $\rho$ convinces the verifier with challenge $e$, and $0$ otherwise. We also define $M$'s weight of the $\rho$-th row: $W_\rho = |\{e\in E \mid M(\rho,e)=1\}|$. Then $\mathcal{E}$'s success probability is given as follows:
    $$\Pr_{\rho,e}[M(\rho,e)=1 \land W_\rho = |E|]$$
which equals
    $$\Pr_{\rho,e}[M(\rho,e)=1] - \Pr_{\rho,e}[M(\rho,e)=1 \land W_\rho<|E|]\geq \Pr_{\rho,e}[M(\rho,e)=1] - \Pr_{\rho,e}[M(\rho,e)=1 \mid W_\rho<|E|]\geq \Pr_{\rho,e}[M(\rho,e)=1] - (1-1/|E|)$$

Note that the first term corresponds to the probability that $P^*$ convinces $V$. 
Therefore, we have that the protocol is knowledge sound with knowledge error $\kappa = 1-1/|E|$. 