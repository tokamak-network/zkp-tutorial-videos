
# Recalling Quadratic Arithmetic Programs (QAPs) 

## 1. Introduction 
Zero-Knowledge Proofs (ZKPs) allow a prover to convince a verifier that they know a solution to a problem without revealing the solution itself.  
Modern constructions like **zk-SNARKs** rely on a mathematical abstraction known as a **Quadratic Arithmetic Program (QAP)** to represent computations as verifiable polynomial equations.

In this lecture, we will explore:
- How to transform computations into circuits
- How to encode circuits into QAPs
- How QAPs ensure correctness
- A worked-out example: proving knowledge of a solution to `x³ + x + 5 = 35`

---

## 2. From Computations to Circuits
Any computation can be broken into arithmetic operations over a finite field (addition and multiplication).  
These operations are represented as **arithmetic circuits**, where:
- **Wires** carry values (variables).
- **Gates** perform arithmetic operations (+, ×).

**Example problem:**

```

x^3 + x + 5 = 35

````

We know that `x = 3` is a solution.

### Step 1: Code Representation
```python
def Evaluate(x):
    return x**3 + x + 5
````

### Step 2: Flattening

Flattening means breaking complex expressions into a sequence of elementary operations:

```python
def Evaluate(x):
    var1 = x * x
    var2 = var1 * x
    var3 = var2 + x
    out  = var3 + 5
```

### Step 3: Circuit

* **Gate 1**: `var1 = x * x`
* **Gate 2**: `var2 = var1 * x`
* **Gate 3**: `var3 = var2 + x`
* **Gate 4**: `out  = var3 + 5`

This circuit captures the computation structure.

---

## 3. From Circuits to QAP

To verify computations algebraically, we encode the circuit using matrices:

* **L**: Left inputs
* **R**: Right inputs
* **O**: Outputs

### Variable Ordering

We define the variable vector:

```
S = [1, x, out, var1, var2, var3]
```

Here:

* `1` is a constant wire.
* `x` is the input.
* `out` is the final output.
* `var1, var2, var3` are intermediate variables.

### Example Diagram

Below is a worked example of transforming the computation into a QAP (flattening, circuit, and constraint matrices):

![QAP Example](d25e4aca-8a27-4e83-8668-09320b346076.png)

---

## 4. Valid Assignments

If `x = 3`, then:

```
var1 = 9
var2 = 27
var3 = 30
out  = 35
```

So:

```
S = [1, 3, 35, 9, 27, 30]
```

---

## 5. The QAP Equation

The central consistency condition is:

```
(L · S) * (R · S) = (O · S)
```

where `*` is element-wise multiplication.

This ensures each gate is satisfied:

* Gate 1: `3 * 3 = 9`
* Gate 2: `9 * 3 = 27`
* Gate 3: `27 + 3 = 30`
* Gate 4: `30 + 5 = 35`

✅ All constraints satisfied.

---

## 6. From QAPs to Polynomials

So far, we worked with matrices. In zk-SNARKs, these are transformed into **polynomials**.
Each row of `L, R, O` is interpolated into a polynomial using Lagrange interpolation.

The prover constructs the polynomial identity:

```
(Σ Li(x)·Si) * (Σ Ri(x)·Si) - (Σ Oi(x)·Si) = 0
```

for all gate indices `i`.

Thus, correctness reduces to proving that this polynomial is identically zero.

---

## 7. Soundness via Random Evaluation

A malicious prover might try to cheat by making the polynomial correct only at a few points.

To prevent this:

* The verifier picks a random evaluation point `t`.
* If the polynomial is not identically zero, it can only vanish at a small number of points (bounded by its degree).
* Therefore, with overwhelming probability, a false proof will be caught.

---

## 8. Zero-Knowledge via Blinding

To ensure **zero-knowledge**, the prover adds randomness (blinding factors) to the witness values and polynomials.

* This hides the actual witness values.
* The verifier only checks correctness, not the private input.
* Thus, the computation is proven without revealing secrets.

---

## 9. Summary

* Computations are flattened into circuits of arithmetic gates.
* Circuits are encoded as QAPs with constraint matrices `L, R, O`.
* Valid assignments satisfy `(L·S) * (R·S) = (O·S)`.
* QAPs are transformed into polynomial identities for zk-SNARKs.
* Random evaluation ensures **soundness**.
* Blinding ensures **zero-knowledge**.

This forms the mathematical foundation of efficient and privacy-preserving zk-SNARK systems.
