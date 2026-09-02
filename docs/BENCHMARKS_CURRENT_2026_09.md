# ExecMesh Benchmark Results: Current Production-Shaped Matched Campaign (September 2026)

## 1. Evaluation Methodology & Test Protocol

All performance results in this document reflect the final production-shaped **M66-R4-P6 Matched-Live Benchmark Campaign**, executed under an audited 5-pair alternating replication protocol ($K=3 \times N=16 = 48\text{ proofs/replicate}$, totaling 240 reference proofs vs 240 ExecMesh native proofs).

### Hardware & Environment Baseline
- **GPU**: NVIDIA GeForce GTX 1650 (4,096 MiB GDDR5, Compute Capability 7.5)
- **CPU**: Intel Xeon E3-1245 v2 @ 3.40 GHz (4 physical cores / 8 logical threads, AVX support)
- **Host OS**: Linux (WSL2 / Ubuntu 22.04 LTS, kernel 6.6)
- **NVIDIA Driver**: 591.86 / **CUDA Runtime**: 12.0

### Benchmark Workload: AnonAadhaar Circuit
- **Circuit Geometry**: $1,104,354$ Variables ($nVars$), $3$ Public Signals, QAP Domain Size $2^{21}$ ($2,097,152$ elements).
- **Test Set**: $16$ unique canonical test vectors derived from real RSA-2048 passport signatures, repeated across $K=3$ passes per replicate to evaluate cold startup, cache warming, and sustained throughput.

---

## 2. Compared Architectures (Explicit Breakdown)

1. **CPU / ICICLE Pipeline (`CPU_ICICLE_PIPELINE`)**:
   - **Witness Engine**: Node.js / Circom native WASM/C++ runtime executed with 8-way multi-process concurrency ($W=8$).
   - **Proving Engine**: ICICLE CUDA Groth16 Prover (`v0.1.0` / BN254 backend) using Pippenger MSM and radix-2 NTT kernels.
   - **Verification**: SnarkJS v0.7.5 (`snarkjs groth16 verify`).

2. **ExecMesh Native GPU Pipeline (`EXECMESH_NATIVE_PIPELINE`)**:
   - **Witness Engine**: ExecMesh P3 Persistent GPU Witness Daemon (SHA-256: `3866f7409cb6...`) with compiled fusion schedule.
   - **Lifecycle Management**: Explicit $4\text{ GB}$ VRAM release and transition gating.
   - **Proving Engine**: ExecMesh P5A Native CUDA Groth16 Daemon (SHA-256: `bc09f4b8...`) with persistent QAP memory residency.
   - **Verification**: SnarkJS v0.7.5 (`snarkjs groth16 verify`).

---

## 3. Matched Campaign Results (240 vs 240 Proofs)

### 3.1 Timing & Throughput Summary

| Proving Stage / Metric | Node/Circom CPU Witness (W=8) + ICICLE CUDA | ExecMesh Native GPU Stack (P3 Witness + P5A Prover) | Paired Comparison |
| :--- | :---: | :---: | :---: |
| **Witness Generation (48 proofs)** | **156.24 s** (52.08 s/N16, 3.255 s/proof) | **164.40 s** (54.80 s/N16, 3.425 s/proof) | ExecMesh ~5.2% slower in matched campaign |
| **Groth16 Prover (48 proofs)** | **90.83 s** (30.28 s/N16, 1.892 s/proof) | **104.69 s** (34.90 s/N16, 2.181 s/proof) | ExecMesh ~15.2% slower in matched campaign |
| **Total Compute Path (48 proofs)** | **247.067 s** (82.356 s/N16, 5.147 s/proof) | **272.571 s** (90.857 s/N16, 5.679 s/proof) | **Point Estimate: ExecMesh +25.50 s (+10.32%)** |
| **Sustained Throughput** | **0.1943 proofs/second** | **0.1761 proofs/second** | Delta: -0.018 proofs/sec |

### 3.2 Statistical Significance & Confidence Interval

Across the 5 alternating matched replicates:
- **Paired Mean Delta $(B - A)$**: $+25.504\text{ s}$ per 48-proof replicate ($+10.32\%$).
- **Sample Standard Deviation ($s_d$)**: $38.943\text{ s}$.
- **95% Student-t Confidence Interval (df=4)**: **`[-22.862 s, +73.869 s]`**.

```text
SCIENTIFIC VERDICT:
"No statistically significant compute-path difference was detected between ExecMesh Native
and the Node/Circom CPU witness + ICICLE CUDA reference pipeline in this five-pair matched
campaign. The point estimate favored the reference by 10.3%, with substantial host system
and runtime variance on the commodity test machine."
```

### 3.3 Cryptographic Correctness Audit (100.0% PASS)

| Correctness Dimension | Node/Circom + ICICLE CUDA | ExecMesh Native GPU Stack | Status |
| :--- | :---: | :---: | :---: |
| **SnarkJS Verification** | 240 / 240 PASS (100.0%) | 240 / 240 PASS (100.0%) | **VERIFIED** |
| **Witness Byte Parity** | 240 / 240 Canonical Reference | 240 / 240 Byte-Identical to Circom | **VERIFIED** |
| **Failed / Malformed Proofs** | 0 | 0 | **ZERO DEFECTS** |
