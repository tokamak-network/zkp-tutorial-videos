Perfect! Let’s extend the **Groth16 cheat sheet** to include the **mathematical construction** (polynomials, pairings, and how they fit into the SNARK). Here’s the updated **Markdown version**:



# 📘 Groth16 Cheat Sheet (Extended)

## **Groth16: Non-interactive linear proof system for QAP-based $R$**

Groth16 is a SNARK for verifying satisfiability of a **Quadratic Arithmetic Program (QAP)**.



### 🛠️ **Algorithms**

1. **Setup**:

   $$
   (\tau, \sigma) \leftarrow \texttt{Setup}(pp_\lambda, \text{QAP})
   $$

   * Generates a **Common Reference String (CRS)** using a trusted setup.
   * Encodes the QAP’s polynomials into group elements using random secret parameters.

2. **Prove**:

   $$
   \pi \leftarrow \texttt{Prove}(\sigma, a, c)
   $$

   * Computes a proof $\pi$ for public input $a$ and private witness $c$.
   * Encodes polynomial relationships as group elements.

3. **Verify**:

   $$
   b \leftarrow \texttt{Verify}(\sigma, a, \pi)
   $$

   * Verifies $\pi$ using pairings.



### 🧮 **Mathematical Construction**

#### Quadratic Arithmetic Program (QAP)

Given a circuit represented as R1CS:

* $\{u_k(X), v_k(X), w_k(X)\}$: polynomials encoding constraints.
* Witness vector $\mathbf{d} = (d_0, d_1, \dots, d_{m-1})$.

The **QAP condition**:

$$
\left( \sum_k d_k u_k(X) \right) \cdot \left( \sum_k d_k v_k(X) \right) - \sum_k d_k w_k(X) = t(X) \cdot h(X)
$$

✅ *The circuit is satisfied if the left-hand side is divisible by the vanishing polynomial $t(X)$.*



#### Polynomial Commitments

* Prover commits to polynomials as group elements:

  $$
  [P(X)]_1 = \{P(\tau)G\}
  $$

  using secret $\tau$ generated in setup.



#### Pairing Check

Verifier checks:

$$
e(A, B) = e(C, G_T)
$$

where:

* $A, B, C$: proof elements in $\mathbb{G}_1, \mathbb{G}_2$.
* $e: \mathbb{G}_1 \times \mathbb{G}_2 \to \mathbb{G}_T$: bilinear pairing.

✅ *If this pairing check holds, the proof is valid.*



### 📜 **Security Properties**

1. **Perfect Completeness**
2. **Statistical Knowledge Soundness**
3. **Zero-Knowledge (Optional)**:
   Achieved by adding random blinding factors during proof generation.



### ⚡ **Key Features**

| Property                 | Groth16              |
|  | -- |
| Proof size               | 3 group elements     |
| Verifier time            | 1 pairing check      |
| Trusted setup            | Circuit-specific CRS |
| Supports Zero-Knowledge? | ✅ Yes                |
