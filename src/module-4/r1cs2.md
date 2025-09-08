# Lecture Notes on Section 2.4: Circuit, Rank-1 Constraint System, and Quadratic Arithmetic Program (QAP)



## Goal

To explain how arithmetic computations are represented as constraints in SNARKs using:

* **Arithmetic Circuits**,
* **Rank-1 Constraint Systems (R1CS)**, and
* **Quadratic Arithmetic Programs (QAP)**.

These structures are foundational for Groth16 and used throughout the SNARK construction in Section 3.



## 1. Arithmetic Circuits

An arithmetic circuit is a **directed acyclic graph (DAG)** composed of:

* **Input wires**: values like `x`, `y`, etc.
* **Gates**:

  * **Addition (+)**
  * **Multiplication (×)**
* **Output wires**: final results.

### Example:

Let’s compute:

$$
z = (x + 1)(y + 2)
$$

This can be represented as:

* Gate 1: `a = x + 1`
* Gate 2: `b = y + 2`
* Gate 3: `z = a * b`

We label all wires: `x, y, a, b, z`



## 2. Rank-1 Constraint System (R1CS)

An R1CS encodes a circuit as a set of **algebraic constraints**, where each constraint is of the form:

$$
\langle \mathbf{u}_i, \mathbf{d} \rangle \cdot \langle \mathbf{v}_i, \mathbf{d} \rangle = \langle \mathbf{w}_i, \mathbf{d} \rangle
$$

where:

* `\mathbf{d}` is the full vector of wire assignments (including a 1 for constants),
* `\mathbf{u}_i`, `\mathbf{v}_i`, and `\mathbf{w}_i` are **row vectors** from matrices `U`, `V`, and `W`,
* `\langle \cdot, \cdot \rangle` is the dot product.

### Intuition:

Each constraint enforces:

$$
(↵\text{left linear combination}) \times (\text{right linear combination}) = \text{output linear combination}
$$

This is general enough to express both addition and multiplication.

### For our example:

Let’s assume wire assignment vector:

$$
\mathbf{d} = (1, x, y, a, b, z)
$$

* Index 0: constant 1
* Index 1: `x`
* Index 2: `y`
* Index 3: `a = x + 1`
* Index 4: `b = y + 2`
* Index 5: `z = a * b`

#### Constraint 1 (a = x + 1):

* Left: `x + 1` → use `x` and constant
* Right: `1`
* Output: `a`

#### Constraint 2 (b = y + 2):

* Same structure

#### Constraint 3 (z = a \* b):

* Left: `a`
* Right: `b`
* Output: `z`

These become rows in matrices `U`, `V`, and `W`.



## 3. Quadratic Arithmetic Program (QAP)

QAP is a **polynomial encoding** of R1CS.

Each column of `U`, `V`, and `W` becomes a **polynomial**:

* `u_k(X), v_k(X), w_k(X)` — one for each wire (column).

We evaluate these polynomials over a domain `\mathcal{X} = \{r_1, ..., r_n\}` (usually roots of unity).

Let:

$$
A(X) = \sum_k d_k u_k(X), \quad B(X) = \sum_k d_k v_k(X), \quad C(X) = \sum_k d_k w_k(X)
$$

Then define:

$$
p(X) = A(X) \cdot B(X) - C(X)
$$

A valid witness implies:

$$
p(X) = t(X) \cdot h(X)
$$

where:

* `t(X)` is the **vanishing polynomial** over the domain: `t(X) = \prod_{x \in \mathcal{X}} (X - x)`
* `h(X)` is some quotient polynomial.

✅ If `p(X)` is divisible by `t(X)`, then all R1CS constraints are satisfied.



## Why This Matters

* **Groth16** uses the QAP representation to generate SNARK proofs.
* Provers commit to evaluations of `A(X), B(X), C(X)`.
* Verifiers check the divisibility relation via **pairing-based cryptography**.

You’ll see these polynomials again in the **arithmetic argument (`π_arith`)** of Section 3.



## Summary

| Concept            | Purpose                                     |
|  | - |
| Arithmetic Circuit | Visual structure of computation             |
| R1CS               | Matrix-based constraint system for circuits |
| QAP                | Polynomial encoding of R1CS for SNARKs      |

