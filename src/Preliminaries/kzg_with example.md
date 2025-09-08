#  Lecture: KZG Polynomial Commitments

##   Overview

In this lecture, we introduce **KZG polynomial commitments**, a cryptographic primitive that allows one to:

- **Commit** to a polynomial $f(X)$ succinctly (single group element).
- Later **prove** and **verify** the evaluation $f(z) = y$ at any point $z$ efficiently.  

KZG commitments are the backbone of many modern **zk-SNARKs** (e.g., Groth16, Plonk) and play a key role in compressing large polynomial arguments into constant-size proofs.  

We will:  
  Motivate the need for polynomial commitments in interactive proof systems (recall Sumcheck).  
  Introduce the **KZG scheme** and its pairing-based construction.  
  Prove its correctness and security properties (binding and hiding).  
  Discuss its applications in zero-knowledge proofs.  



##   Prerequisites

You should already be comfortable with:  
- Finite fields and polynomials ($\mathbb{F}_p[X$$$).  
- The **Sumcheck protocol** and its use of polynomial evaluations.  
- Group operations and basic discrete log assumptions.  

Optional but helpful:  
- Familiarity with **bilinear pairings**.  



##   Why Polynomial Commitments?

Imagine a prover wants to convince a verifier that they know a polynomial $f(X)$:  
1. They could send all coefficients of $f$ — but this isn’t succinct!  
2. They could send $f(z)$ at some point $z$ — but the verifier can’t trust this without additional evidence.  

We need a scheme where:  
- The prover **commits** to $f(X)$ once.  
- For any $z$, they can produce a **succinct proof** that $f(z)$ is correct.  
- The verifier can check this efficiently without seeing $f$.  

KZG commitments achieve this in **O(1)** verifier time and **O(1)** proof size.  



##   Connection to Sumcheck

In the **Sumcheck protocol**, the prover repeatedly sends univariate polynomials to the verifier, who checks their evaluations at random points.  
- But what if the verifier doesn't trust the prover to send the correct polynomials?  
- Polynomial commitments allow us to **bind the prover to a polynomial** and verify evaluations later.  

This is the key to making interactive protocols **non-interactive** (via Fiat-Shamir) and succinct.


  

#   Warmup: Bilinear Pairings Refresher

Before we define KZG commitments, let’s recall the basics of **bilinear pairings**, which are the cryptographic foundation of this scheme.



##  What is a Bilinear Pairing?

A **bilinear pairing** is a map:

$$
e : \mathbb{G}_1 \times \mathbb{G}_2 \to \mathbb{G}_T
$$

between two (possibly the same) cyclic groups $\mathbb{G}_1, \mathbb{G}_2$ of prime order $p$, with target group $\mathbb{G}_T$, satisfying:

1. **Bilinearity**:
   $$
   e(g_1^a, g_2^b) = e(g_1, g_2)^{ab}
   $$
   for all $g_1 \in \mathbb{G}_1, g_2 \in \mathbb{G}_2$, and $a, b \in \mathbb{Z}_p$.

2. **Non-degeneracy**:
   $$
   e(g_1, g_2) \ne 1
   $$
   if $g_1, g_2$ are generators.

3. **Efficient computability**: There is an efficient algorithm to compute $e(g_1, g_2)$.

Common instantiations:
- **Type-1 pairing**: $\mathbb{G}_1 = \mathbb{G}_2$.
- **Type-3 pairing**: asymmetric groups ($\mathbb{G}_1 \ne \mathbb{G}_2$).



##   Why Pairings in Polynomial Commitments?

Pairings let us check relations like:

$$
e(g^a, g^b) = e(g, g)^{ab}
$$

This will allow a verifier to check that a quotient polynomial $w(X)$ is correctly related to a committed polynomial $f(X)$ without seeing $f$.



##   Example: Simple Pairing Equation

Let’s check bilinearity:

- Choose $g_1 = g^u$, $g_2 = g^v$ for some $u, v$.
- Compute:
  $$
  e(g_1, g_2) = e(g^u, g^v) = e(g, g)^{uv}
  $$

This property will be critical in KZG: it lets the verifier check correctness of a polynomial evaluation proof with a single pairing equation.



##   Security Assumption: Knowledge of Exponent (KoE)

KZG relies on a **trusted setup** where a secret $\tau$ is used to compute powers:
$$
\{g, g^\tau, g^{\tau^2}, \dots, g^{\tau^d}\}
$$

Security assumes no one knows $\tau$. This is called the **structured reference string (SRS)**.



  **Now we’re ready to define KZG polynomial commitments!**
#   KZG Polynomial Commitments: Commit, Open, Verify

Let’s define the KZG polynomial commitment scheme step by step.



##   1. Setup (Trusted Setup / SRS)

- Let $\mathbb{G}$ be a cyclic group of prime order $p$, with generator $g$.  
- A secret $\tau \in \mathbb{F}_p$ is sampled, and the **Structured Reference String (SRS)** is generated:  

$$
\text{SRS} = \big\{g, g^\tau, g^{\tau^2}, \dots, g^{\tau^d} \big\}
$$

This is published and used by all parties.  
  *No one should know $\tau$ — security relies on this.*



##   2. Commit to a Polynomial

Suppose the prover wants to commit to a polynomial:  

$$
f(X) = a_0 + a_1 X + a_2 X^2 + \cdots + a_d X^d
$$

The **commitment** is:  

$$
C_f = g^{f(\tau)} = g^{a_0 + a_1\tau + a_2\tau^2 + \cdots + a_d\tau^d}
$$

  *This is a single group element, regardless of degree!*  



## 🔑 3. Open (Prove Evaluation)

To prove $f(z) = y$ at some point $z \in \mathbb{F}_p$:  

1. Compute the **witness polynomial**:  
   $$
   w(X) = \frac{f(X) - y}{X - z}
   $$
   (This works because $f(z) - y = 0$.)

2. Commit to $w(X)$:  
   $$
   \pi = g^{w(\tau)}
   $$

$\pi$ is the **evaluation proof** sent to the verifier.



##   4. Verify

The verifier checks that:

$$
e(C_f / g^y, g) = e(\pi, g^{\tau - z})
$$

**Why does this work?**
- By construction:
  $$
  f(\tau) - y = (\tau - z)\cdot w(\tau)
  $$
- So:
  $$
  g^{f(\tau)-y} = \big(g^{w(\tau)}\big)^{\tau-z}
  $$
- Pairings give:
  $$
  e(g^{f(\tau)-y}, g) = e(g^{w(\tau)}, g^{\tau-z})
  $$

This verifies the evaluation.



##   Intuition Summary

| Step       | Action                                     | Size |
||--||
| **Commit** | $C_f = g^{f(\tau)}$                      | 1    |
| **Open**   | $\pi = g^{w(\tau)}$                      | 1    |
| **Verify** | Check pairing equation                     | 1    |

The **proof size** is constant, and **verification** only requires one pairing check.



##  Worked Example (Toy Field $\mathbb{F}_{17}$)

**Setup:**  
- $\tau = 3$, $g = 2$ (generator of $\mathbb{G}$ of order 17).  
- $f(X) = 5 + 4X + X^2$.  

**Commit:**  
$$
C_f = g^{f(\tau)} = 2^{5 + 4\cdot3 + 3^2} = 2^{5 + 12 + 9} = 2^{26} \pmod{17}
$$
$$
2^{26} \bmod 17 = 2^{26 \bmod 16} = 2^{10} = 1024 \bmod 17 = 4
$$

So $C_f = 4$.  

**Open at $z=2$:**
- $f(2) = 5 + 4\cdot2 + 2^2 = 5+8+4=17 \equiv 0 \pmod{17}$.  
- $w(X) = \frac{f(X)-0}{X-2}$.  

**Witness commitment:**
$$
\pi = g^{w(\tau)} = \cdots
$$

(*Leave as exercise or compute step by step for students.*)

**Verify:**
Check pairing equation.  



## 🔒 Security Properties

  **Binding:** Prover cannot open $C_f$ to a different polynomial without solving a hard problem (discrete log).  

  **Hiding (Optional):** If needed, can add randomness to commitment:  
$$
C_f' = C_f \cdot g^r
$$



##   Applications

- Used in **Groth16** and **Plonk** zk-SNARKs for succinctness.  
- Turns interactive protocols (e.g., Sumcheck) into **non-interactive proofs**.  



#  Polynomial Commitment Schemes (General Definition)

Before introducing KZG commitments, let’s define the **abstract concept** of a polynomial commitment scheme.  

A **polynomial commitment scheme** allows a prover to commit to a polynomial $f(X)$ and later prove that $f(z) = y$ at any evaluation point $z$, in a way that is:  
  **Succinct** (commitments and proofs are small), and  
  **Verifiable** (the verifier doesn’t need to know $f$).  



##   Components of a Polynomial Commitment Scheme

A polynomial commitment scheme consists of the following algorithms:  

1. **Setup** ($1^\lambda, d \to \text{pp}$)  
   - Generates public parameters $\text{pp}$ for polynomials of degree up to $d$, where $\lambda$ is the security parameter.  
   - May involve a **trusted setup** or be transparent.  

2. **Commit** ($\text{pp}, f(X) \to C_f$)  
   - Prover commits to polynomial $f(X)$ and produces a succinct commitment $C_f$.  

3. **Open** ($\text{pp}, f(X), z \to \pi$)  
   - Prover computes an **evaluation proof** $\pi$ for $f(z) = y$.  

4. **Verify** ($\text{pp}, C_f, z, y, \pi \to \{0,1\}$)  
   - Verifier checks that $f(z) = y$ is consistent with $C_f$ and accepts/rejects.



##   Correctness

If all parties are honest, the verifier always accepts:

$$
\text{Verify}(\text{pp}, C_f, z, f(z), \pi) = 1
$$



## 🔒 Security Properties

A secure polynomial commitment scheme satisfies:  

- **Binding**  
  - A commitment $C_f$ binds the prover to a unique polynomial $f(X)$.  
  - Prover cannot open $C_f$ to two distinct evaluations $(z, y)$ and $(z, y')$ for $y \ne y'$.  

- **Hiding (Optional)**  
  - The commitment hides all information about $f(X)$ beyond what is revealed by openings.  
  - *Note*: Some schemes (like KZG) are not hiding by default but can be extended to be hiding.  



##   Example Usage

1. Prover computes $C_f$ for $f(X)$.  
2. Later, verifier challenges with $z$.  
3. Prover responds with $\pi$ and $y = f(z)$.  
4. Verifier runs $\text{Verify}$ and accepts if $f(z)$ is correct.  

| Step       | Prover                              | Verifier        |
||--||
| Commit     | $C_f \leftarrow \text{Commit}(f)$  | Stores $C_f$   |
| Open       | $(y, \pi)$ for challenge $z$     | Sends $z$      |
| Verify     |                                      | Check $C_f, z, y, \pi$ |



##   Instantiations

There are several polynomial commitment schemes with different tradeoffs:  

| Scheme      | Trusted Setup? | Proof Size | Verifier Time | Example Usage      |
|-|-||||
| **KZG**     | Yes            | Constant   | Fast          | Groth16, Plonk     |
| **FRI**     | No             | Logarithmic| Faster        | STARKs             |
| **IPA**     | No             | Logarithmic| Fast          | Bulletproofs       |

In this lecture, we focus on **KZG commitments**, which are pairing-based and achieve **constant-size proofs**.



  **Next:** Let’s see how KZG realizes this general interface.

#   Example: Encoding a Problem as a Polynomial

To see why polynomials are so powerful in SNARKs, let’s walk through a simple example.  



## 🎯 The Problem

Objective: Prove that there exists $x$ such that the Fibonacci Square sequence modulo a prime satisfies:
* $a_0 = 1, a_1 = x$
* $a_{1022} = 2338775057$
* $a_{n+2} = a_{n+1}^2 + a_n^2 \ (\text{mod prime})$

## 🔄 Step 1: Representing the Computation
  Define $f(x)$, the trace polynomial, such that:
    $$
    f(g^i) = a_i \quad \text{for } i = 0, 1, \dots, 1022
    $$
    *Rewrite the sequence constraints:*

$a_0 = 1$: $f(x) = 1 \ \text{for } x = g^0$
 $a_{1022} = 2338775057$: $f(x) = 2338775057 \ \text{for } x = g^{1022}$
$a_{n+2} = a_{n+1}^2 + a_n^2$: 
$$ f(g^2x) = f(gx)^2 + f(x)^2 \ \text{for } x = g^i, \, 0 \leq i \leq 1020
        $$
        
**Step 2: From Polynomial Constraints to Roots**
Use the concept of polynomial roots:
* $g^0$ is a root of $f(x) - 1$ for $a_0 = 1$.
* $g^{1022}$ is a root of $f(x) - 2338775057$ for $a_{1022} = 2338775057$.
*  $g^i, 0 \leq i \leq 1020$, are roots of $f(g^2x) - f(gx)^2 - f(x)^2$ for the recurrence relation.
**Key Insight**: Reformulate each constraint in terms of roots of polynomials.
**Step 3 - From Roots to Rational Functions**
Leverage the theorem:
    $$ z \text{ is a root of } p(x) \iff (x - z) \text{ divides } p(x)    $$
Reformulate constraints as rational functions:
*   
* $\frac{f(x) - 1}{x - g^0}$: Polynomial for $a_0 = 1$.
* $\frac{f(x) - 2338775057}{x - g^{1022}}$: Polynomial for $a_{1022} = 2338775057$.
* $\frac{f(g^2x) - f(gx)^2 - f(x)^2}{\prod_{i=0}^{1020}(x - g^i)}$: Polynomial for the recurrence relation.
 Simplify the denominator:
    $$
    \prod_{i=0}^{1023}(x - g^i) = x^{1024} - 1
    $$


** Composition Polynomial:**

Combine the rational functions $p_0(x), p_1(x), p_2(x)$ into a single polynomial $CP(x)$:
    $$   CP(x) = \alpha_0 \cdot p_0(x) + \alpha_1 \cdot p_1(x) + \alpha_2 \cdot p_2(x)    $$

If $CP(x)$ is a polynomial, then with high probability, $p_0(x), p_1(x), p_2(x)$ are also polynomials.
          Commit to $CP(x)$ using a Merkle tree.


**Summary and Next Steps**
Constraints on the sequence $a_n$ were reduced to constraints on $f(x)$, then to roots, and finally to rational functions.
          $p_0(x), p_1(x), p_2(x)$: Rational functions that must be polynomials.
          Combined into $CP(x)$ for efficiency.
Next Steps
Prove that $CP(x)$ is a polynomial.
 Commit to $CP(x)$ using a Merkle tree.
    #   Example: Encoding a Problem Using Polynomials in SNARKs

In this section, we’ll walk through a **realistic example** to see how a computational problem can be **encoded as polynomial constraints**, which is the foundation of SNARK systems.  

This process highlights **why polynomials and polynomial commitments are central to SNARK implementations**.



## 🎯 The Problem

**Objective:** Prove that there exists $x$ such that the **Fibonacci Square sequence** modulo a prime satisfies:  

- $a_0 = 1,\ a_1 = x$  
- $a_{1022} = 2338775057$  
- $a_{n+2} = a_{n+1}^2 + a_n^2 \pmod{\text{prime}}$

Our goal is to encode this computation into **polynomial constraints** and later prove their validity succinctly.



## 🔄 Step 1: Representing the Computation

Define $f(X)$, the **trace polynomial**, such that:  

$$
f(g^i) = a_i \quad \text{for } i = 0, 1, \dots, 1022
$$

Here $g$ is a generator of a multiplicative subgroup of the field.  

We can rewrite the sequence constraints as follows:  

1. **Initial condition:**  
   $$
   f(g^0) = 1
   $$

2. **Final condition:**  
   $$
   f(g^{1022}) = 2338775057
   $$

3. **Recurrence relation:**  
   $$
   f(g^2 X) = f(gX)^2 + f(X)^2 \quad \text{for } X = g^i,\ 0 \leq i \leq 1020
   $$

This representation encodes all values of the sequence in the polynomial $f(X)$.



##   Step 2: From Polynomial Constraints to Roots

Using the concept of **polynomial roots**, we reformulate the constraints:  

1. $g^0$ is a root of $f(X)- 1$ for the initial condition.  
2. $g^{1022}$ is a root of $f(X) - 2338775057$ for the final condition.  
3. $g^i,\, 0 \leq i \leq 1020$, are roots of:  

$$
f(g^2 X) - f(gX)^2 - f(X)^2
$$

This transforms the problem into proving that certain points are **roots** of specific polynomials.



## 🧮 Step 3: From Roots to Rational Functions

We leverage the fact:  

$$
z \text{ is a root of } P(X) \iff (X - z) \text{ divides } P(X)
$$

Rewriting constraints as **rational functions**:  

1. Initial condition:  
   $$
   p_0(X) = \frac{f(X) - 1}{X - g^0}
   $$

2. Final condition:  
   $$
   p_1(X) = \frac{f(X) - 2338775057}{X - g^{1022}}
   $$

3. Recurrence relation:  
   $$
   p_2(X) = \frac{f(g^2X) - f(gX)^2 - f(X)^2}{\prod_{i=0}^{1020}(X - g^i)}
   $$

###   Simplify the denominator

We note:  

$$
\prod_{i=0}^{1023}(X - g^i) = X^{1024} - 1
$$

This simplification is crucial for efficient computation.



##   Step 4: Composition Polynomial

We combine the rational functions $p_0(X), p_1(X), p_2(X)$ into a **single polynomial**:  

$$
CP(X) = \alpha_0 \cdot p_0(X) + \alpha_1 \cdot p_1(X) + \alpha_2 \cdot p_2(X)
$$

Here $\alpha_0, \alpha_1, \alpha_2$ are random scalars chosen by the verifier.  

  **Key Insight**: If $CP(X)$ is a polynomial, then (with high probability) each $p_i(X)$ is also a polynomial.

This step reduces the task of checking *many constraints* to checking a **single polynomial**.



## 🔒 Step 5: Commit to the Polynomial

We now commit to $CP(X)$:  
- Using a **Merkle tree** or a **polynomial commitment scheme** (e.g., KZG).  
- This binds the prover to $CP(X)$ without revealing it.  



##   Summary & Next Steps

We reduced constraints on the sequence $(a_n)$ to constraints on $f(X)$, then to **roots of polynomials**, and finally to **rational functions**.  

- $p_0(X), p_1(X), p_2(X)$: Rational functions encoding the constraints.  
- $CP(X)$: Composition polynomial that aggregates constraints.  
- **Commitment to $CP(X)$**: Essential for succinct proof and verifier efficiency.

###   Next:
- Prove that $CP(X)$ is a polynomial.  
- Commit to $CP(X)$ using a Merkle tree or KZG commitment.  
- Use this setup in a SNARK to verify correctness of the sequence.

# KZG for multiple prover
Sure — here’s a concrete **example** of generalized KZG commitments with:

* $n = 2$ provers,
* a degree-$3$ polynomial $f(X)$,
* secret-shared coefficients.



### **Setup**

Let the polynomial be:

$$
f(X) = f_0 + f_1 X + f_2 X^2 + f_3 X^3
$$

We assume the coefficients are **additively shared** between two provers $P_1$ and $P_2$, so for each coefficient:

$$
f_j = f_j^{(1)} + f_j^{(2)} \quad \text{for } j = 0, 1, 2, 3
$$

Then:

* $P_1$ knows: $f^{(1)}(X) = f_0^{(1)} + f_1^{(1)} X + f_2^{(1)} X^2 + f_3^{(1)} X^3$
* $P_2$ knows: $f^{(2)}(X) = f_0^{(2)} + f_1^{(2)} X + f_2^{(2)} X^2 + f_3^{(2)} X^3$

They do **not** know the full $f(X)$, only their share.



### **Step 1: Local Commitment**

Each prover locally computes a commitment to their share using public parameters:

$$
\text{pp} = \left( g_1^\tau, g_1^{\tau^2}, g_1^{\tau^3}, g_1^{\tau^4} \right)
$$

Then:

* $P_1$ computes:

  $$
  c_1 = \prod_{j=0}^3 (g_1^{\tau^j})^{f_j^{(1)}} = g_1^{f^{(1)}(\tau)}
  $$
* $P_2$ computes:

  $$
  c_2 = \prod_{j=0}^3 (g_1^{\tau^j})^{f_j^{(2)}} = g_1^{f^{(2)}(\tau)}
  $$

The final commitment is:

$$
c = c_1 \cdot c_2 = g_1^{f^{(1)}(\tau)} \cdot g_1^{f^{(2)}(\tau)} = g_1^{f(\tau)}
$$

So the provers never reveal their shares, but together produce a valid KZG commitment to the full polynomial.



### **Step 2: Local Evaluation and Proof**

Let’s evaluate at $x = 5$. Each prover computes:

$$
q^{(i)}(X) = \frac{f^{(i)}(X) - f^{(i)}(5)}{X - 5}
$$

Then they compute:

$$
\pi_i = g_1^{q^{(i)}(\tau)}
$$

and broadcast $\pi_1$, $\pi_2$. The final proof is:

$$
\pi = \pi_1 \cdot \pi_2 = g_1^{q(\tau)}
$$

The verifier then checks:

$$
e(\pi, g_2^{\tau - 5}) = e(c \cdot g_1^{-f(5)}, g_2)
$$
Here’s a simplified and clear version of the **“5.4 Traditional bottlenecks: FFTs and MSMs”** subsection, ready for your document:



**5.4 Traditional Bottlenecks: FFTs and MSMs (Simplified)**

In SNARK provers, two of the most computationally expensive operations are Fast Fourier Transforms (FFTs) and multi-scalar multiplications (MSMs). FFTs are used to evaluate and interpolate polynomials efficiently, and MSMs are used to compute expressions of the form $\sum_i s_i \cdot v_i$, where $s_i$ are field elements and $v_i$ are elliptic curve points.

In a single-prover setting, both operations are well-optimized using libraries that run on a single machine. However, in a multi-prover setting (i.e., in MPC), they become performance bottlenecks.

For FFTs, the main challenge is that the polynomial inputs are secret-shared across multiple provers. Naively evaluating these polynomials would require costly communication. Fortunately, FFTs are linear operations, meaning each party can apply the FFT locally to its share. This avoids interaction and drastically reduces overhead.

For MSMs, the situation is more difficult. Scalar multiplication on elliptic curves is expensive in MPC because group operations are non-linear and cannot be parallelized as easily. A naive approach would simulate each group operation interactively, leading to high communication and computation costs.

This section motivates the need to redesign these two building blocks—FFTs and MSMs—in an MPC-friendly way so that they can be computed securely and efficiently across distributed parties without undermining the performance of the overall SNARK prover.


### SumCheck and ProdCheck (Simplified)

Many SNARKs need to prove that a committed polynomial $f(X)$ satisfies certain global properties over a set of points $\Omega = \{1, \omega, \omega^2, \dots, \omega^{n-1} \}$, where $\omega$ is a root of unity.

Two common checks are:

* **SumCheck**: Used in SNARKs like Marlin
  The prover wants to prove:

  $$
  \sum_{x \in \Omega} f(x) = c
  $$

  where $c$ is a public value and $c_f$ is a commitment to $f$.

* **ProdCheck**: Used in Plonk
  The prover wants to prove:

  $$
  \prod_{x \in \Omega} f(x) = c
  $$

In both cases, the goal is to prove that the sum or product over the evaluations of a secret-shared polynomial equals a claimed value, without revealing the polynomial itself.



### SumCheck in MPC

SumCheck is relatively easy to adapt to MPC:

* The prover defines a new polynomial:

  $$
  f'(X) = \frac{f(X) - c/|\Omega|}{X}
  $$
* Then proves that:

  $$
  f(X) = X \cdot f'(X) + \frac{c}{|\Omega|}
  $$
* Since polynomial commitments are linear, this relation can be proven using the standard commitment scheme.

The key point is: all steps involve operations on coefficients, so if $f$ is secret-shared, $f'$ can be computed locally by each party.



### ProdCheck in MPC

ProdCheck is more challenging because it involves computing a sequence of **partial products**:

$$
t_i = \prod_{j=0}^i f(\omega^j)
$$

Computing these in MPC seems to require many rounds of interaction — but the authors use a clever technique to do it in **constant rounds**, based on a classic method by Bar-Ilan and Beaver.

#### How it works (high-level steps):

1. The provers generate random shared values $r_0, \dots, r_n$ and their inverses.
2. They use a secure multiplication protocol to compute shared products of the form $r_{i-1} \cdot t_i \cdot r_i^{-1}$ and open them.
3. They then recover the actual partial products $t_i$ using public inverses and the open values.

This allows them to compute the full product sequence in parallel, with only a **constant number of communication rounds** — which is critical for making ProdCheck practical in an MPC setting.
 concrete example of how the MPC version of **ProdCheck** works using 2 provers and 3 evaluation points. The goal is to compute a product like this in a distributed (MPC) setting:

### Goal:

Each prover holds secret shares of $f(\omega^0), f(\omega^1), f(\omega^2)$, and we want to compute the partial products:

* $t_0 = f(\omega^0)$
* $t_1 = f(\omega^0) \cdot f(\omega^1)$
* $t_2 = f(\omega^0) \cdot f(\omega^1) \cdot f(\omega^2)$

…but without any party seeing the actual values of $f(\omega^i)$. Let’s now follow the 3-step protocol:



### Step 1: Generate random shared values

The provers generate shared random values:

* $r_0, r_1, r_2 \in F$
* and their inverses: $r_0^{-1}, r_1^{-1}, r_2^{-1}$

These values are random and shared, but their inverses are publicly known (or committed).



### Step 2: Multiply in secret and open intermediate values

Let’s define:

* $t_0 = f(\omega^0)$
* $t_1 = t_0 \cdot f(\omega^1)$
* $t_2 = t_1 \cdot f(\omega^2)$

Each $t_i$ is computed recursively, but we don’t compute and share the actual $t_i$. Instead, we compute and open:

$$
u_i = r_{i-1} \cdot t_i \cdot r_i^{-1}
$$

for $i = 1, 2$. These $u_i$ values are opened publicly — they reveal random-looking values but **not** $t_i$ directly, because they are masked.

For example:

* $u_1 = r_0 \cdot t_1 \cdot r_1^{-1}$
* $u_2 = r_1 \cdot t_2 \cdot r_2^{-1}$

The multiplication is done securely using a **multiplication protocol** over shares.



### Step 3: Recover the actual $t_i$

Now that $u_1, u_2$ are public, and the random values $r_0, r_1, r_2$ and their inverses are known, we can recover:

* $t_1 = u_1 \cdot r_0^{-1} \cdot r_1$
* $t_2 = u_2 \cdot r_1^{-1} \cdot r_2$

This gives us the real values of $t_i$, even though each prover only ever worked on their secret shares.



### Why this works

The clever part is: by **masking** the partial product $t_i$ with random values $r_{i-1}, r_i^{-1}$, and then opening the result, we:

* Avoid revealing the original secrets
* Keep the number of communication rounds constant (just a few rounds for opening and multiplications)
* Get the correct $t_i$ values for ProdCheck
