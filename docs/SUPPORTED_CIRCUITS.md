# Supported Circuits & Bounded Circom Features

## Supported Circom Language Subset

ExecMesh compiles and executes a deterministic, bounded subset of the Circom 2.x language designed for high-performance GPU scheduling:

### Fully Supported Constructs
- Multi-dimensional signal arrays and nested components.
- Standard arithmetic gates (addition, subtraction, multiplication, division, modulo in $\mathbb{F}_r$).
- Bitwise operators, shifts, and comparisons (`<`, `<=`, `>`, `>=`, `==`, `!=`).
- Standard circomlib components:
  - `Bitify` (Num2Bits, Bits2Num)
  - `Comparators` (LessThan, GreaterThan, IsZero, IsEqual)
  - `Gates` (AND, OR, XOR, NOT, NAND, NOR)
  - `Sha256` / `Sha256compression`
  - `Pedersen` / `BabyJubjub` point additions and scalar multiplications
  - `Poseidon` / `MiMC` sponge hash functions
  - `MerkleTree` verifiers and inclusion proofs
  - `EdDSAPoseidonVerifier` / `EdDSAMiMCVerifier`

### Architectural Bounds & Dynamic Constructs
ExecMesh enforces strict static schedule compilation for GPU memory alignment:
- Circuit loop bounds must be statically evaluatable at compilation time.
- Dynamic runtime recursion is bounded by static maximum stack depth.
- Invariant topology verification requires passing $N \ge 20$ sample execution vectors during package gating.
