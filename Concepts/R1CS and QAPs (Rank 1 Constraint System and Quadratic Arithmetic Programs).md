---
aliases:
  - R1CS
tags: 
created: 2023-11-07
---
# R1CS

The big question is how to represent the algebraic circuits constructed in a proof system so they're verifiable by the verifier, given a solution to those constraints. So we define a language called R1CS whose basic structure is :

$$s.A * s.B = s.C$$

Here, $s$ is the solution to the R1CS matrices, and $A$, $B$, and $C$ hold the gate constraints in each of them. We'll go on to explain how the constraints are set from the circuit into these matrices, using logic gates.

First, we need every constraint to decompose into a statement of the form $A * B = C$ or $A + B = C$. For example, $x^3 + x + 5 == 35$ is a constraint that could be decomposed into:

- **1st gate** : $temp1 = x * x$
- **2nd gate** : $y = temp1 * x$
- **3rd gate** : $temp2 = y + x$
- **4th gate** : $out = temp2 + 5$

So the constraint is now equivalent, just decomposed into simpler constraints that we can easily encode in the R1CS matrices. Since we're dealing with logic gates, we keep at most 3 variables in a constraint — this intuitively makes sense, since a logic gate has 2 inputs and 1 output, so it's sensible to represent them this way for easy encoding.

> **Note** — the $*$ here represents the Hadamard product in algebra, which is like a dot product except with element-wise multiplication, returning a matrix of the same size rather than a scalar (as in the dot product).

For now, think of $s$ as containing all the variables involved in the constraints, plus a dummy variable for a constant value:

$$s = [1, x, out, temp1, y, temp2]$$
$s$ contains the solution, or what we call the assignments to all these variables.

## Worked example — the matrices

Using this variable ordering, the $A$, $B$, $C$ matrices for all 4 gates work out to:

**A**

$$
A =
\begin{bmatrix}
0 & 1 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 1 & 0 & 0 \\
0 & 1 & 0 & 0 & 1 & 0 \\
5 & 0 & 0 & 0 & 0 & 1
\end{bmatrix}
$$

**B**

$$
B =
\begin{bmatrix}
0 & 1 & 0 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 & 0 & 0 \\
1 & 0 & 0 & 0 & 0 & 0 \\
1 & 0 & 0 & 0 & 0 & 0
\end{bmatrix}
$$

**C**

$$
C =
\begin{bmatrix}
0 & 0 & 0 & 1 & 0 & 0 \\
0 & 0 & 0 & 0 & 1 & 0 \\
0 & 0 & 0 & 0 & 0 & 1 \\
0 & 0 & 1 & 0 & 0 & 0
\end{bmatrix}
$$

Row 1 = 1st gate ($temp1 = x * x$), row 2 = 2nd gate ($y = temp1 * x$), row 3 = 3rd gate ($temp2 = y + x$), row 4 = 4th gate ($out = temp2 + 5$).

If we do $s.A * s.B  = s.C$  then we can see that we arrive at the first constraint 
$x * x = temp1$ and so on for the other constraints.

Note that gates 3 and 4 (the additions) are encoded as multiplications by the constant $1$ in $B$ -this is the standard trick for folding an addition constraint into the same $A * B = C$ form as a multiplication gate.

As you can see, in the equation $s.A * s.B = s.C$, each row of these matrices represents one constraint. We do this for all the constraints and combine all the vectors into matrices $A$, $B$, and $C$. 

Now, if the verifier gives a random challenge - say, "verify at these assignments" - the prover would give an answer. But to verify it the trivial way, the verifier would need to carry out the whole computation, which would be very inefficient. Each row vector of each matrix $A$, $B$, and $C$ represents one decomposed constraint, i.e. one gate.

So we arrive at the concept of **Quadratic Arithmetic Programs (QAPs)**, which encode the whole R1CS system into polynomials -  and once we move into that domain, there's a lot of clever manipulation that becomes possible.

Essentially, we interpolate the **columns** of the R1CS matrices into polynomials using Lagrange interpolation.

# Lagrange Interpolation

Given $n$ points, we can define a polynomial with $n - 1$ coefficients. We construct $n$ separate polynomials, each of which passes through exactly one point and is strictly zero at all the others - for points other than these, we don't care what the polynomial does elsewhere. This unique polynomial, defined per point, is summed across all $n$ of them to get the final interpolated polynomial.

So instead of the original matrices (4 gates × 6 variables), we now get 6 sets of degree-3 polynomials - one polynomial per column/variable.
# The Actual Working

Let's call the first constraint's first value $c_{11}$, its second value $c_{12}$, and so on, and similarly $c_{21}, c_{22}, \dots$ for the second constraint, and so forth for the rest. So the points defined for interpolation, for the first column, would be $(1, c_{11}), (2, c_{21}), (3, c_{31}), (4, c_{41})$, and similarly for the other columns.

Since the columns are interpolated as polynomials from these points, evaluating at $x = 1$ recovers only the first constraint's value for that column, since the polynomial unique to $x = 1$ is zero at every other point, and this holds for every column simultaneously. So evaluating all the columns at $x = 1$ gives us back all the first constraint's values as a vector. **Sanity check** — since there are 6 columns, evaluating all 6 polynomials gives a 6-tuple vector, which is exactly the original constraint vector in the R1CS matrix.

Similarly, evaluating all the columns at $x = 2$ gives us the second value for each column, extracting the second constraint's row vector, and so on for all 4 constraints in our example. That's how we recover all our constraints from the interpolated column polynomials.

That's the major intuition here. We define things the same way for other values of $x$, one for each gate in the circuit.

## Verifying QAP

Each matrix ($A$, $B$, $C$) is interpolated column by column, giving 6 polynomials per matrix (one per variable) - 18 in total. To check a specific assignment $s$, we take the linear combination of each matrix's column polynomials, weighted by $s$:

$$A(x) = \sum_i s_i \cdot A_i(x), \quad B(x) = \sum_i s_i \cdot B_i(x), \quad C(x) = \sum_i s_i \cdot C_i(x)$$

where $A_i(x)$, $B_i(x)$, $C_i(x)$ are the individual column polynomials. This gives one combined polynomial per matrix for this particular assignment.

We then define the target polynomial $t(x) = A(x) \cdot B(x) - C(x)$. Naively checking that this holds at every gate would mean evaluating $t(x)$ at every one of the $n$ points individually.

Instead, we include another polynomial in this system, called $Z$, to make verification faster. We define:

$$Z = (x-1)(x-2)(x-3)\dots$$

depending on the number of gates/decomposed constraints. Since $Z$ is zero at every point corresponding to a gate, checking that $t(x)$ is correct at all gates simultaneously reduces to checking that $t(x)$ is evenly divisible by $Z(x)$, i.e. that $h(x) = t(x)/Z(x)$ exists as a polynomial with no remainder. If it does, we can conclude that all the computation encoded was done correctly.

If we try to falsify any solution to the matrices, the resulting $t(x)$ changes and would no longer hit zero at one of the points $1, 2, 3, \dots$, so it no longer divides evenly by $Z$, and we can distinguish falsified solutions from correct ones this way.

---

Lots of reference drawn from this article: [Quadratic Arithmetic Programs: from Zero to Hero](https://medium.com/@VitalikButerin/quadratic-arithmetic-programs-from-zero-to-hero-f6d558cea649) — provides an amazing explanation of the steps of the above method.