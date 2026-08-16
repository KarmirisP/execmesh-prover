# ExecMesh Benchmark Methodology & Results

## Benchmark Setup

All published benchmarks are conducted using reproducible, multi-vector test matrices evaluated on identical reference hardware.

### Hardware Baseline
- **GPU**: NVIDIA GeForce GTX 1650 (4 GB GDDR5, Compute Capability 7.5)
- **CPU**: Intel Xeon E5-2686 v4 / Core i7 class (8 logical cores, x86_64 SIMD)
- **OS**: Linux 5.15 / Ubuntu 22.04 LTS

### Baseline Prover Stack
- **Witness Engine**: Circom 2.1.x Native C++ Witness Generator binary
- **Prover Engine**: Rapidsnark (Intel Xeon optimized x86_64 assembly & AVX-512)
- **Verification**: `snarkjs groth16 verify` (v0.7.4)

---

## Empirical Benchmark Results (Audited 20-Vector Invariant)

### 1. Batch Transaction Verifier (278,128 Constraints)
Simulates a multi-transfer zkRollup block verifier with Merkle path validations, balance checks, and EdDSA signature verifications.

| Prover Engine | Witness Time (p50) | Proving Time (p50) | Total Latency (p50) | Throughput | Verification |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Conventional CPU Stack** | 1,020 ms | 18,420 ms | **19.44 s** | 0.0588 proofs/s | 20/20 PASS |
| **ExecMesh GPU Stack** | 7,720 ms | 2,170 ms | **9.89 s** | **0.0893 proofs/s** | 20/20 PASS |
| **Advantage / Speedup** | — | — | **1.97x Faster** | **1.52x Higher** | Valid |

### 2. SHA256-512 (62,528 Constraints)
Standard cryptographic compression circuit evaluating two SHA-256 compression blocks.

| Prover Engine | Total Latency (p50) | Sustained Throughput | Verification |
| :--- | :--- | :--- | :--- |
| **Conventional CPU Stack** | 2,709 ms | 0.733 proofs/s | 20/20 PASS |
| **ExecMesh GPU Stack** | 2,441 ms | 0.495 proofs/s | 20/20 PASS |

### 3. Pedersen-512 (6,519 Constraints)
BabyJubjub elliptic curve point compression circuit frequently utilized in privacy protocols.

| Prover Engine | Total Latency (p50) | Witness D2H Overhead | Verification |
| :--- | :--- | :--- | :--- |
| **Conventional CPU Stack** | ~1,850 ms | Full host witness transfer | 20/20 PASS |
| **ExecMesh GPU Stack** | ~1,010 ms | 0 Bytes (Device-Resident) | 20/20 PASS |

---

## Key Takeaways

1. **Massive Acceleration on Large Rollup Circuits**: On complex circuits with high constraint-to-signal ratios, ExecMesh delivers **~2x end-to-end wall-clock speedup** on commodity 4GB GPUs.
2. **Deterministic Cryptographic Verification**: 100% of generated proofs strictly verify against standard verification keys using third-party verification tools (`snarkjs`).
3. **CSPRNG Blinding**: Every proof is independently randomized with fresh blinding scalars in $\mathbb{F}_r^*$, preserving full zero-knowledge security guarantees.
