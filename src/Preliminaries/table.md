| Symbol / Notation        | Meaning / Description                                                                 |
|--------------------------|----------------------------------------------------------------------------------------|
| `n`                      | Number of rows in the circuit (number of constraints)                                 |
| `m`                      | Number of columns in the circuit (number of wires)                                    |
| `t(X)`                   | Vanishing polynomial over evaluation domain `X`                                       |
| `X`                      | Evaluation domain (set of inputs for polynomial constraints), size `n`                |
| `ω_n`                    | Primitive `n`-th root of unity                                                        |
| `S_max`                  | Maximum number of subcircuit copies used in field-programmable circuit                |
| `U`, `V`, `W`            | R1CS matrices representing left, right, and output linear combinations                 |
| `u_k(X), v_k(X), w_k(X)` | QAP polynomials representing each column vector from R1CS                             |
| `d = (d_0, ..., d_{m-1})`| Wire assignment vector (includes both public inputs and private witness)              |
| `a`, `c`                 | Public input and private witness respectively                                         |
| `σ`                      | CRS (common reference string) from Groth16 setup                                      |
| `τ`                      | Trapdoor used in simulation                                                           |
| `π`                      | SNARK proof                                                                            |
| `π_arith`                | Proof part corresponding to arithmetic checks (Groth16-based)                         |
| `π_perm`                 | Proof part for permutation argument (PlonK-style)                                     |
| `π_bind`                 | New polynomial binding argument tying `π_arith` and `π_perm` together                 |
| `π_fp`                   | Final full proof = (`π_arith`, `π_perm`, `π_bind`)                                   |
| `𝔽`                      | Finite field used for all arithmetic                                                   |
| `G`, `H`                 | Generators of the bilinear groups `𝔾₁` and `𝔾₂`                                        |
| `e(·,·)`                 | Bilinear pairing map from `𝔾₁ × 𝔾₂ → 𝔾_T`                                              |
| `C`                      | Circuit structure (gates, wiring, etc.)                                               |
| `C_fp`                   | Field-programmable circuit composed of subcircuits and wiring info                    |
| `W_sub`                  | Set of wiring permutations for subcircuits                                            |

# testing 

<iframe
  src="https://zkrepl.dev/?code=template_example"
  width="100%"
  height="500"
  style="border:1px solid #ccc;"
>
</iframe>
