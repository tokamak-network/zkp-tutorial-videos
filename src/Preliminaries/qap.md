# Quadratic Arithmetic Programs (QAPs) — Full Example
```admonish info
**Problem:** I know numbers \\( x,y \\) such that: 
1. \\( x\cdot y = 12 \\)
2. \\( x \\) is a perfect square, namely \\( x=s^2 \\)  
And I want to keep \\( x,y \\) secret. 
```

```admonish info
**What is a QAP?**
A Quadratic Arithmetic Program (QAP) is a way to encode computational constraints as polynomial equations. It allows us to prove we know secret values that satisfy certain relationships without revealing those values.
```

```admonish example
**Circom Implementation:**
Here's how you would implement the same computation in Circom:
```

```circom
pragma circom 2.0.0;

template PerfectSquare() {
    // Private inputs
    signal private input s;  // secret square root
    signal private input y;  // secret factor
    
    // Public inputs
    signal input public_constant;  // should be 12
    
    // Intermediate signals
    signal x;  // the perfect square
    
    // Constraints
    x <== s * s;           // s^2 = x (perfect square)
    public_constant <== x * y;  // x * y = 12
    
    // Output
    signal output out;
    out <== x;
}

component main = PerfectSquare();
```







We want to prove knowledge of secret numbers \\( s, x, y \in \mathbb{F}_p \\) such that:

1. \\( s \cdot s = x \\) (so \\( x \\) is a square),
2. \\( x \cdot y = 12 \\) (main constraint with public constant 12).

A valid solution is:

\\[
s = 2, \quad x = 4, \quad y = 3.
\\]

The public statement is:  
**“There exist values \\( s, x, y \\) such that these equations hold, with output equal to 12.”**

The prover knows the witness \\( (s,x,y) \\).


## Step 0. Circuit Picture

Before we dive into wires and polynomials, let’s look at the computation as a simple circuit:

```

s ----●×
\|     (Gate 1: s·s = x)
s ----●×----> x

x ----●×
\|     (Gate 2: x·y = 12)
y ----●×----> 12 (public)

```

* Gate 1 squares \\(s\\) to produce \\(x\\).  
* Gate 2 multiplies \\(x\\) with \\(y\\) to produce the public constant \\(12\\).

This is the structure we'll encode into R1CS and then QAP.

```admonish tip
**Visualization Tip:** Drawing the circuit first helps you understand which wires connect to which gates. This makes the later polynomial construction much clearer!
```

## Step 1. Wires and Assignments

We collect all the variables (constants, inputs, and witness) into a vector of **wire values**:

\\[
(a_0, a_1, a_2, a_3, a_4).
\\]

* \\( a_0 = 1 \\): the special **constant wire**, always equal to 1.
* \\( a_1 = s \\): the secret square root.
* \\( a_2 = x \\): the secret square value.
* \\( a_3 = y \\): the secret other factor.
* \\( a_4 = 12 \\): the **public input** constant.

So for our witness \\( (s=2,x=4,y=3) \\):

\\[
(a_0, a_1, a_2, a_3, a_4) = (1,2,4,3,12).
\\]

```admonish info
**Wire Assignment Strategy**
The key insight is that we assign each variable in our computation to a specific "wire" position. The constant wire \\(a_0=1\\) is always present, while other wires represent our secret and public values.
```

---

## Step 2. Assigning Evaluation Points

Every multiplication gate in the circuit is associated with one **evaluation point** in the field.

We have two multiplication gates:

* Gate 1: \\( s \cdot s = x \\).
* Gate 2: \\( x \cdot y = 12 \\).

We choose the evaluation points:

\\[
r_1 = 3, \quad r_2 = 4.
\\]

These are arbitrary distinct field elements.

```admonish warning
**Important:** The evaluation points must be distinct field elements. If two gates had the same evaluation point, the polynomial interpolation would fail.
```

---

## Step 3. Selector Polynomials

At each gate’s evaluation point, we record which wires are “active” in the left input, right input, and output. A `1` means the wire participates; a `0` means it does not.

| Gate                | \\(r_q\\) | Left (\\(u_i\\)) | Right (\\(v_i\\)) | Output (\\(w_i\\)) |
| ------------------- | ------- | -------------- | --------------- | ---------------- |
| 1 (\\(s\cdot s = x\\))  | \\(3\\)   | \\(u_1=1\\) (s)  | \\(v_1=1\\) (s)   | \\(w_2=1\\) (x)    |
| 2 (\\(x\cdot y = 12\\)) | \\(4\\)   | \\(u_2=1\\) (x)  | \\(v_3=1\\) (y)   | \\(w_4=1\\) (12)   |

* At \\(X=3\\): only the \\(s\\) wire is in the left and right, output is \\(x\\).  
* At \\(X=4\\): left is \\(x\\), right is \\(y\\), output is the public constant \\(12\\).  

All other selectors are zero.


For each wire \\( a_i \\), we define **selector polynomials** \\( u_i(X), v_i(X), w_i(X) \\).

* \\( u_i(X) \\): controls whether wire \\( a_i \\) is used in the **left input** of a gate.
* \\( v_i(X) \\): controls whether wire \\( a_i \\) is used in the **right input**.
* \\( w_i(X) \\): controls whether wire \\( a_i \\) is used in the **output**.

At each evaluation point \\( r_q \\), we set these values based on the gate.

```admonish info
**Selector Polynomials Explained**
Think of selector polynomials as "switches" that turn wires on or off at specific gates. At each evaluation point, exactly one wire is active for left input, one for right input, and one for output.
```

---

### Gate 1 (\\( X=3 \\)): \\( s \cdot s = x \\)

* Left input is \\( s \\): so \\( u_1(3) = 1 \\).
* Right input is \\( s \\): so \\( v_1(3) = 1 \\).
* Output is \\( x \\): so \\( w_2(3) = 1 \\).
* All other selectors at \\( X=3 \\) are 0.

### Gate 2 (\\( X=4 \\)): \\( x \cdot y = 12 \\)

* Left input is \\( x \\): so \\( u_2(4) = 1 \\).
* Right input is \\( y \\): so \\( v_3(4) = 1 \\).
* Output is the constant 12: so \\( w_4(4) = 1 \\).  
  (Here the “1” means wire \\( a_4 \\) is included, and recall \\( a_4=12 \\)).
* All other selectors at \\( X=4 \\) are 0.

---

## Step 4. Building the QAP Polynomials

We interpolate the selectors into polynomials.

For each wire \\( a_i \\):

* \\( u_i(X) \\) is a polynomial with known values at \\( X=3,4 \\).
* \\( v_i(X) \\) is similar.
* \\( w_i(X) \\) is similar.

For example:

* For \\( u_1(X) \\): we need \\( u_1(3)=1, u_1(4)=0 \\).  
  Using Lagrange interpolation:

  \\[
  u_1(X) = \frac{X-4}{3-4}.
  \\]

* For \\( u_2(X) \\): we need \\( u_2(3)=0, u_2(4)=1 \\).

  \\[
  u_2(X) = \frac{X-3}{4-3}.
  \\]

And similarly for each \\( u_i,v_i,w_i \\).  
(This step shows how the 1's and 0's in the selector table turn into real polynomials.)

```admonish tip
**Lagrange Interpolation Shortcut:** When you have values at just two points, the interpolating polynomial is always linear: \\(f(X) = \\frac{X-b}{a-b}f(a) + \\frac{X-a}{b-a}f(b)\\)
```

---

## Step 5. Global Polynomials \\( U,V,W \\)

Given a witness vector \\( (a_0,\dots,a_4) \\), we build:

\\[
U(X) = \sum_{i=0}^4 a_i \, u_i(X), \quad
V(X) = \sum_{i=0}^4 a_i \, v_i(X), \quad
W(X) = \sum_{i=0}^4 a_i \, w_i(X).
\\]

So \\( U,V,W \\) are linear combinations of the selectors weighted by the actual wire values.

---

## Step 6. The Divisibility Condition

The QAP condition is:

\\[
U(X)\cdot V(X) - W(X) = H(X)\cdot t(X),
\\]

where

\\[
t(X) = (X-3)(X-4).
\\]

This means:

* At \\( X=3 \\), the polynomial identity enforces Gate 1.
* At \\( X=4 \\), it enforces Gate 2.

So if divisibility holds, **all gates are satisfied simultaneously**.

```admonish info
**The Magic of Divisibility**
This is the core insight of QAPs: instead of checking each gate individually, we encode all constraints into a single polynomial equation. If the divisibility condition holds, we know all gates are satisfied at once.
```

---

## Step 7. Checking with Our Witness

Plugging in \\( (s,x,y) = (2,4,3) \\):

* At \\( X=3 \\):  
  \\( U(3) = s = 2,\quad V(3)=s=2,\quad W(3)=x=4. \\)  
  Check: \\( 2\cdot 2 - 4 = 0 \\).

* At \\( X=4 \\):  
  \\( U(4)=x=4,\quad V(4)=y=3,\quad W(4)=12. \\)  
  Check: \\( 4\cdot 3 - 12 = 0 \\).

✅ Both conditions hold → the witness is valid.

```admonish tip
**Verification Strategy:** Instead of checking each gate separately, we can verify the entire computation by checking one polynomial divisibility condition. This is much more efficient!
```

---

# Summary of Notation

* \\( a_i \\): wire values (constant, public input, or witness).
* \\( u_i(X), v_i(X), w_i(X) \\): selector polynomials for wire \\( i \\).
* \\( U(X), V(X), W(X) \\): linear combinations of selectors with actual \\( a_i \\).
* \\( t(X)=(X-3)(X-4) \\): target polynomial, zero at each gate’s evaluation point.
* QAP condition: \\( U(X)V(X)-W(X) \\) divisible by \\( t(X) \\).
# Groth16 for the example
Now that we have the **QAP fully clear**, let's walk step by step into **Groth16** using *our exact example*. I'll carefully connect every piece of notation so that students see how Groth16 enforces the divisibility condition **in the exponent**.

```admonish warning
**Trusted Setup Required:** Groth16 requires a trusted setup ceremony where trapdoor values are generated and then destroyed. If these values are ever revealed, the entire system becomes insecure.
```

---

We want to prove knowledge of \\( (s,x,y) \\) such that

1. \\( s \cdot s = x \\)  
2. \\( x \cdot y = 12 \\)

with witness \\( (s=2,x=4,y=3) \\).  
Public input: \\( 12 \\).

We already expressed this as a QAP with evaluation points \\( 3,4 \\) and target polynomial

\\[
t(X) = (X-3)(X-4).
\\]

The condition for correctness is:

\\[
U(X)V(X)-W(X) = H(X)\,t(X).
\\]

Groth16 now builds a **succinct, zero-knowledge proof system** for this condition.

---

## Step 1. Setup

A trusted setup picks random trapdoor values:

\\[
\alpha, \beta, \gamma, \delta, \tau \in \mathbb{F}_p^*.
\\]

These are **never revealed**.

Then the setup publishes a **Common Reference String (CRS)** with group elements in:

* \\( G_1 \\), generator \\( g_1 \\)  
* \\( G_2 \\), generator \\( g_2 \\)  
* pairing \\( e:G_1\times G_2 \to G_T \\) with  

  \\[
  e(g_1^a,g_2^b) = e(g_1,g_2)^{ab}.
  \\]

The CRS contains:

* Encodings of powers of \\( \tau \\): \\( g_1^{\tau^i}, g_2^{\tau^i} \\) (so prover can evaluate polynomials at \\( \tau \\) without knowing it).
* Encodings of selectors \\( u_i(\tau), v_i(\tau), w_i(\tau) \\).
* Special encodings with \\( \alpha, \beta, \gamma, \delta \\) for soundness/zero-knowledge.

**Key idea:** CRS allows prover to “blindly” commit to \\( U(\tau), V(\tau), W(\tau) \\) and related terms.

---

## Step 2. Prove

Given a witness \\( (s=2,x=4,y=3) \\):

1. **Compute polynomial values at \\( \tau \\):**

   \\[
   U(\tau), \quad V(\tau), \quad W(\tau).
   \\]

   Then compute the quotient:

   \\[
   H(\tau) = \frac{U(\tau)V(\tau)-W(\tau)}{t(\tau)}.
   \\]

2. **Randomize for zero-knowledge:** pick random scalars \\( r,s \\).

3. **Form the proof (3 elements):**

   \\[
   A = g_1^{\alpha + U(\tau) + r\delta} \in G_1,
   \\]

   \\[
   B = g_2^{\beta + V(\tau) + s\delta} \in G_2,
   \\]

   \\[
   C = g_1^{\;W'(\tau) + H(\tau)t(\tau)/\delta + A s + r B - rs\delta} \in G_1.
   \\]

   * \\( W'(\tau) \\) = part of \\( W(\tau) \\) that depends on the **witness** (not the public inputs).  
   * Randomizers \\( r,s \\) hide the witness.

4. **Send proof:**

   \\[
   \pi = (A,B,C).
   \\]

---

## Step 3. Verify

The verifier knows the **public input** \\( 12 \\).  
From the CRS, they only need a small subset (the “verifier key”).

They check one **pairing product equation**:

\\[
e(A,B) \stackrel{?}{=}
e(g_1^\alpha, g_2^\beta) \cdot
e\!\big(\text{public input encoding}, g_2^\gamma\big) \cdot
e(C, g_2^\delta).
\\]

* **First term:** ensures \\( A \\) and \\( B \\) are consistent with \\( \alpha,\beta \\).
* **Second term:** enforces the **public input** (here \\( 12 \\)) is correctly included.
* **Third term:** checks \\( C \\) encodes the witness contributions and quotient \\( H(\tau) \\).

If the equality holds, the proof is valid.

---

## Step 4. Why This Works

* The prover convinces the verifier that there exists some \\( H(X) \\) such that

  \\[
  U(X)V(X)-W(X) = H(X) t(X).
  \\]

* But all evaluations are hidden inside group elements.  
* Thanks to bilinearity:

  \\[
  e(g_1^{U(\tau)}, g_2^{V(\tau)}) = e(g_1,g_2)^{U(\tau)V(\tau)},
  \\]

  so the verifier can check the relation **in the exponent** with one pairing equation.

---

:::info
### Summary
* **Proof size:** always 3 group elements (\\( A \in G_1, B \in G_2, C \in G_1 \\)).
* **Verification cost:** always 1 pairing equation (≈3 pairings).
* **Independence of circuit size:** works for tiny circuits like ours or very large ones.

This is the magic of Groth16: it compresses a big polynomial divisibility check into **3 elliptic curve points and 1 pairing equation**.

```admonish tip
**Proof Size Advantage:** Unlike other proof systems where proof size grows with circuit complexity, Groth16 always produces exactly 3 group elements regardless of how complex your computation is!
```

**Disadvantage**: Groth16 requires a trusted setup, and its CRS is circuit-specific (i.e., a new CRS must be generated for each circuit). 
:::

# Groth16 (formal)

We work over a prime field \\( \mathbb{F}_p \\) and asymmetric bilinear groups \\( (G_1,G_2,G_T,e) \\) of order \\( p \\), with generators \\( g_1\in G_1, g_2\in G_2 \\), and pairing

\\[
e:G_1\times G_2\to G_T,\qquad e(g_1^x,g_2^y)=e(g_1,g_2)^{xy}.
\\]

## QAP instance

Let \\( \{u_i(X),v_i(X),w_i(X)\}_{i=0}^m\subset \mathbb{F}_p[X] \\) and a target polynomial \\( t(X) \\) (vanishing on the chosen evaluation points) define a QAP.  
A vector of **wire values** is \\( a=(a_0,\dots,a_m) \\) with \\( a_0=1 \\). We split indices as:

* public indices \\( i\in\{0,\dots,\ell\} \\) (these are known to the verifier),
* witness indices \\( i\in\{\ell+1,\dots,m\} \\) (kept secret).

Define

\\[
U(X)=\sum_{i=0}^m a_i\,u_i(X),\quad
V(X)=\sum_{i=0}^m a_i\,v_i(X),\quad
W(X)=\sum_{i=0}^m a_i\,w_i(X).
\\]

A **valid assignment** satisfies the divisibility relation

\\[
U(X)V(X)-W(X)=H(X)\,t(X)\qquad\text{for some }H(X)\in\mathbb{F}_p[X].
\\]

### (For our example)

* Gates: \\( s\!\cdot\! s=x \\) at \\( X=3 \\), and \\( x\!\cdot\! y=12 \\) at \\( X=4 \\).
* \\( t(X)=(X-3)(X-4) \\).
* The “constant 12” appears through the public wire \\( a_4=12 \\) and its selector \\( w_4(4)=1 \\).
* Public set can be \\( \{a_0=1, a_4=12\} \\) (so \\( \ell=4 \\)); witness \\( (a_1,a_2,a_3)=(s,x,y) \\).  
  (Any equivalent public/witness split is fine as long as constants are in the public part.)

---

## Algorithms

### 1) Setup\\((\text{QAP}) \to (\text{CRS}_\text{prover}, \text{CRS}_\text{verifier})\\)

Sample trapdoors \\(\alpha,\beta,\gamma,\delta,\tau \xleftarrow{\$}\mathbb{F}_p^\*\\).

Publish the **CRS** with the following elements (shown grouped conceptually; concrete implementations optimize the layout):

* Powers of \\( \tau \\) (for polynomial evaluation):

  \\[
  g_1^{\tau^j}\ (0\le j<\deg t),\qquad g_2^{\tau^j}\ (0\le j<\deg t).
  \\]

* Selectors evaluated at \\( \tau \\) (for linear combinations):

  \\[
  g_1^{u_i(\tau)},\quad g_2^{v_i(\tau)},\quad g_1^{w_i(\tau)}\quad\text{for all }i.
  \\]

* “Toxic-waste” masks for knowledge-soundness/zero-knowledge:

  \\[
  g_1^\alpha,\ g_1^\beta,\ g_1^\delta;\qquad
  g_2^\beta,\ g_2^\gamma,\ g_2^\delta.
  \\]

* Split encodings of the combined selector \\( \beta u_i(\tau)+\alpha v_i(\tau)+w_i(\tau) \\):

  \\[
  g_1^{\frac{\beta u_i(\tau)+\alpha v_i(\tau)+w_i(\tau)}{\gamma}}\quad (0\le i\le \ell)\quad\text{(public indices)},
  \\]

  \\[
  g_1^{\frac{\beta u_i(\tau)+\alpha v_i(\tau)+w_i(\tau)}{\delta}}\quad (\ell< i\le m)\quad\text{(witness indices)}.
  \\]

* Quotient support:

  \\[
  g_1^{\frac{\tau^j t(\tau)}{\delta}}\quad \text{for } 0\le j<\deg t - 1
  \\]

  (since \\( \deg H = \deg t - 1 \\) for a size-\\( n \\) QAP).

The **verifier key** is a small subset:

\\[
\text{VK}=\Big(g_1,\ g_2,\ g_1^\alpha,\ g_2^\beta,\ g_2^\gamma,\ g_2^\delta,\ \{\,g_1^{\frac{\beta u_i(\tau)+\alpha v_i(\tau)+w_i(\tau)}{\gamma}}\ :\ 0\le i\le \ell\,\}\Big).
\\]

*(Intuition: all CRS elements are just group encodings of the various quantities at the hidden point \\( \tau \\), masked by \\( \alpha,\beta,\gamma,\delta \\).)*  
This matches the construction in Groth16 (3-element proof, 1 pairing-check).

---

### 2) Prove\\((\text{CRS}_\text{prover}, \text{QAP}, a) \to \pi=(A,B,C)\\)

Given a satisfying assignment \\( a=(a_0,\dots,a_m) \\):

1. Compute the evaluations at \\( \tau \\):

   \\[
   U(\tau),\quad V(\tau),\quad W(\tau).
   \\]

   Compute

   \\[
   H(\tau)=\frac{U(\tau)V(\tau)-W(\tau)}{t(\tau)}.
   \\]

2. Sample \\( r,s \xleftarrow{\$}\mathbb{F}_p \\) (zero-knowledge).

3. Form the proof elements (by linear combinations of CRS entries, *no* trapdoor knowledge needed):

   \\[
   A \ :=\ g_1^{\ \alpha + U(\tau) + r\delta}\ \in G_1,
   \\]

   \\[
   B \ :=\ g_2^{\ \beta + V(\tau) + s\delta}\ \in G_2,
   \\]

   \\[
   C \ :=\ g_1^{\ \sum_{i=\ell+1}^{m} a_i\cdot\frac{\beta u_i(\tau)+\alpha v_i(\tau)+w_i(\tau)}{\delta}\ +\ \frac{H(\tau)\,t(\tau)}{\delta}\ +\ As\ +\ rB\ -\ rs\delta}\ \in G_1.
   \\]

Return \\( \pi=(A,B,C) \\).

*(Intuition: \\( A \\) commits to \\( U(\tau) \\) (with \\( \alpha \\) and \\( r\delta \\) masks), \\( B \\) commits to \\( V(\tau) \\) (with \\( \beta \\) and \\( s\delta \\) masks), and \\( C \\) bundles the **witness-only** part of \\( \beta U+\alpha V+W \\), the quotient term \\( H(\tau)t(\tau) \\), and the randomizers in a way that the verifier can check one pairing identity.)*

**(For our example)**

* Public indices include \\( a_0=1 \\) and \\( a_4=12 \\); witness indices are \\( a_1=s,a_2=x,a_3=y \\).
* The “public constants” (1 and 12) will appear later only in the verifier’s *public-input* term; the witness parts enter \\( C \\).

---

### 3) Verify\\((\text{VK}, \text{QAP}, \text{public } a_{0.. \ell}, \pi=(A,B,C)) \to \{0,1\}\\)

Using the public inputs \\( a_0,\dots,a_\ell \\) and VK, compute the **public input commitment**

\\[
\mathsf{PI}\ :=\ \prod_{i=0}^{\ell}\Big(g_1^{\frac{\beta u_i(\tau)+\alpha v_i(\tau)+w_i(\tau)}{\gamma}}\Big)^{a_i}\ \in G_1.
\\]

Accept iff the single pairing equation holds:

\\[
\boxed{ \ e(A,B)\ \stackrel{?}{=}\ e(g_1^\alpha, g_2^\beta)\ \cdot\ e(\mathsf{PI},\, g_2^\gamma)\ \cdot\ e(C,\, g_2^\delta)\ }\ .
\\]

*(Intuition: this identity encodes, in the exponent, the equality*

\\[
(\alpha+U(\tau)+r\delta)\cdot(\beta+V(\tau)+s\delta)
=\ \alpha\beta\ +\ \underbrace{\textstyle\sum_{i=0}^{\ell} a_i(\beta u_i(\tau)+\alpha v_i(\tau)+w_i(\tau))}_{\text{public}}\ +\ (\text{witness-part}+H(\tau)t(\tau)+\text{rand.}),
\\]

*thereby enforcing \\( U(\tau)V(\tau)-W(\tau)=H(\tau)t(\tau) \\) without revealing witness values.)*

**(For our example)**

* \\( \mathsf{PI} \\) includes the contribution of \\( a_0=1 \\) and \\( a_4=12 \\) via their pre-encoded \\( \tfrac{\beta u_i(\tau)+\alpha v_i(\tau)+w_i(\tau)}{\gamma} \\) terms.
* Nothing about \\( s,x,y \\) (the witness) appears in \\( \mathsf{PI} \\); their contribution is hidden inside \\( C \\).

```admonish warning
**Circuit-Specific CRS:** Each circuit requires its own Common Reference String (CRS). You cannot use the same CRS for different circuits, even if they have the same number of gates.
```
