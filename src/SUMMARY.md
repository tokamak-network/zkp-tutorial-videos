# Summary

[Introduction](./introduction.md)

- [Preliminaries](./Preliminaries/kzg.md)
  - [Notations](./Preliminaries/table.md)
  - [KZG Polynomial Commitments](./Preliminaries/kzg.md)
  - [Groth16](./Preliminaries/groth16.md)
  - [R1CS](./Preliminaries/r1cs.md)
  - [QAP](./Preliminaries/qap.md)
   - [Exercise](./Preliminaries/exercise.md)
   - [Table](./Preliminaries/table.md)
  

- [Module 1: Foundational Concepts](./module-1/introduction.md)
  - [Proof Systems at a Glance](./module-1/proof-systems.md)
  - [What is Verifiable Computation?](./module-1/verifiable-computation.md)
  - [Introduction to SNARKs](./module-1/snark-intro.md)
  - [Why SNARKs are important for Blockchains](./module-1/snarks-and-blockchains.md)

- [Module 2: SNARK Architectures — From Circuit-Specific to Universal](./module-2/introduction.md)
  - [The Groth16 Approach: A Circuit-Specific Trusted Setup](./module-2/groth16.md)
  - [The Shift to Universal Setups: Sonic, Marlin, and PlonK](./module-2/universal-setups.md)
  - [The Trade-Offs: Efficiency, Updatability, and Universality](./module-2/trade-offs.md)

- [Module 3: The Challenge and the Proposed Solution](./module-3/introduction.md)
  - [The Problem with RAM Circuits in SNARKs](./module-3/ram-circuits.md)
  - [The Proposed Solution: A Field-Programmable SNARK](./module-3/field-programmable.md)
  - [Deconstructing Computation: Arithmetic and Copy Constraints](./module-3/constraints.md)

- [Module 4: The Protocol in Depth](./module-4/introduction.md)
  -  [R1CS and QAP](./module-4/qap.md)
  -  [R1CS and QAP](./module-4/r1cs.md)
  - [The Arithmetic Argument: Bivariate Groth16 for Subcircuits](./module-4/arithmetic-argument.md)
  - [The Copy Constraint Argument: Bivariate Permutation Check](./module-4/permutation-argument.md)
  - [Connecting the Arguments: The Polynomial Binding Argument](./module-4/binding-argument.md)
  - [Bringing It All Together: The Protocol Step-by-Step](./module-4/protocol.md)

- [Module 5: Applications and Analysis](./module-5/introduction.md)
  - [Beyond Preprocessing: Achieving Verifiable Machine Computation](./module-5/machine-computation.md)
  - [Comparing the Performance: Proof Size and Complexity](./module-5/performance.md)
  - [Security Analysis Part I: Completeness and Soundness](./module-5/security-part1.md)
  - [Security Analysis Part II: Zero-Knowledge and Knowledge Soundness](./module-5/security-part2.md)

- [Module 6: Optional Prerequisites & Advanced Extensions](./module-6/introduction.md)
  - [From Circuits to Polynomials: The R1CS and QAP Transformation](./module-6/r1cs-qap.md)
  - [Plook-up & Its Role in Look-up Arguments](./module-6/plookup.md)
  - [Future of SNARKs: Recursive Proofs and Folding Schemes](./module-6/future-of-snarks.md)