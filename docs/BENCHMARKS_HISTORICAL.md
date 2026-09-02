# Historical Benchmarks: ExecMesh vs. CPU Circom + Rapidsnark (Archival)

> **IMPORTANT NOTICE & COMPARATOR DISCLAIMER**:  
> The benchmarks presented in this document represent **historical engineering data** evaluated during earlier developmental milestones against a CPU baseline of **Circom C++ Native Witness Generator + Rapidsnark (x86_64 assembly & AVX)**.  
> **These results are NOT directly comparable to the current M66-R4-P6 matched campaign against ICICLE CUDA.**  
> For current production-shaped matched benchmarks against ICICLE CUDA, refer to [docs/BENCHMARKS_CURRENT_2026_09.md](BENCHMARKS_CURRENT_2026_09.md).

---

## 1. Historical Architecture & Test Setup

- **Hardware**: NVIDIA GeForce GTX 1650 (4 GB GDDR5) / Intel Xeon x86_64 host
- **Historical Comparator**:
  - Witness Engine: Circom 2.1.x Native C++ Single-Threaded Witness Calculator
  - Prover Engine: Rapidsnark (Intel Xeon x86_64 assembly & AVX)
  - Verification: `snarkjs groth16 verify` (v0.7.4)

---

## 2. Historical Circuit Measurements (Audited 20-Vector Invariant)

### 1. Batch Transaction Verifier (278,128 Constraints / 551,768 Wires)
Multi-transfer zkRollup block verifier evaluating Merkle path validations, balance updates, and EdDSA signature verifications.

| Prover Engine | Witness Time (p50) | Proving Time (p50) | Total Latency (p50) | Throughput | Verification |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Historical CPU Baseline** *(Circom C++ + Rapidsnark)* | 1,020 ms | 18,420 ms | **19.44 s** | 0.0588 proofs/s | 20/20 PASS |
| **ExecMesh GPU Stack** *(Historical Fused Implementation)* | 7,720 ms | 2,170 ms | **9.89 s** | **0.0893 proofs/s** | 20/20 PASS |
| **Observed Historical Delta** | — | — | **1.97x Faster** | **1.52x Higher** | 100% Valid |

### 2. SHA256-512 (62,528 Constraints / 62,561 Wires)
Cryptographic compression circuit evaluating two SHA-256 compression blocks.

| Prover Engine | Total Latency (p50) | Sustained Throughput | Verification |
| :--- | :--- | :--- | :--- |
| **Historical CPU Baseline** *(Circom C++ + Rapidsnark)* | 2,709 ms | 0.733 proofs/s | 20/20 PASS |
| **ExecMesh GPU Stack** *(Historical Fused Implementation)* | 2,441 ms | 0.495 proofs/s | 20/20 PASS |

### 3. Pedersen-512 (6,519 Constraints / 7,032 Wires)
BabyJubjub elliptic curve point compression circuit.

| Prover Engine | Total Latency (p50) | Verification |
| :--- | :--- | :--- |
| **Historical CPU Baseline** *(Circom C++ + Rapidsnark)* | ~1,850 ms | 20/20 PASS |
| **ExecMesh GPU Stack** *(Historical Fused Implementation)* | ~1,010 ms | 20/20 PASS |
