# GPU Witness Generation and Full Pipeline Results

This page directly answers the three core technical questions regarding ExecMesh's architecture and performance:
1. **How fast is ExecMesh GPU witness generation versus CPU Circom?**
2. **How does the ExecMesh native Groth16 GPU prover compare with ICICLE CUDA?**
3. **How does the full ExecMesh native GPU pipeline compare with the full CPU witness + ICICLE CUDA pipeline?**

All measurements presented below are derived from the production-shaped **M66-R4-P6 Matched Campaign** on the AnonAadhaar circuit ($1,104,354$ variables / $2^{21}$ domain) using an NVIDIA GTX 1650 ($4\text{ GB}$) and an Intel Xeon E3-1245 v2 CPU ($4\text{C}/8\text{T}$).

---

## Question 1: CPU Witness vs. ExecMesh GPU Witness

### TABLE 1: Node/Circom CPU Witness (W=8) vs. ExecMesh GPU Witness

| Metric / Workload | Node/Circom Multi-Process CPU Witness (W=8) | ExecMesh P3 Persistent GPU Witness Daemon | Delta & Behavior |
| :--- | :---: | :---: | :---: |
| **Batch Time (48 executions)** | **156.24 s** | **164.40 s** | +8.16 s (+5.2%) |
| **Throughput per Batch (N16)** | **52.08 s / N16** | **54.80 s / N16** | +2.72 s / N16 |
| **Latency per Witness** | **3.255 s / witness** | **3.425 s / witness** | +0.170 s / witness |
| **Cryptographic Byte Parity** | Reference Canonical | **240 / 240 Byte-Identical to Circom** | **100.0% Match** |

**Engineering Takeaway**: On this large-scale circuit ($1.1\text{M}$ wires), ExecMesh GPU witness calculation achieved $100\%$ byte-level parity with Circom C++/WASM, while running at near parity with an 8-way parallel multi-process CPU witness pool (within $5.2\%$).

---

## Question 2: ICICLE CUDA Prover vs. ExecMesh Native GPU Prover

### TABLE 2: Prover Component Comparison

| Prover Engine | Benchmark Protocol / Context | Batch Proving Time (48 Proofs) | Per-Proof Latency | Verification |
| :--- | :--- | :---: | :---: | :---: |
| **ICICLE CUDA Groth16 Prover** | Matched P6 Campaign (Same-Run) | **90.83 s** (30.28 s/N16) | **1.892 s** | 240/240 PASS |
| **ExecMesh P5A Native Groth16 Prover** | Matched P6 Campaign (Same-Run) | **104.69 s** (34.90 s/N16) | **2.181 s** | 240/240 PASS |
| *ExecMesh P5A-R2 Isolated Run* | *Isolated Component Benchmark (P5A-R2)* | *29.728 s / N16* | *1.858 s* | *160/160 PASS* |
| *ICICLE Prior Calibration* | *Isolated Component Benchmark (P2)* | *32.119 s / N16* | *2.007 s* | *16/16 PASS* |

*(Note: The italicized rows represent cross-campaign component calibrations and are provided for context; the top two rows represent the strict same-campaign matched evaluation).*

**Engineering Takeaway**: The ExecMesh P5A native prover (featuring persistent QAP coefficient caching and Pippenger MSM) runs within the same operational latency band as ICICLE CUDA (~$1.85$–$2.18\text{ s}$ per $2^{21}$-domain proof on a 4GB GTX 1650).

---

## Question 3: Full End-to-End Pipeline Comparison

### TABLE 3: Full CPU+ICICLE Pipeline vs. Full ExecMesh Native GPU Stack

| Pipeline Architecture | Execution Breakdown (Witness + Prover) | Total Compute Path (48 Proofs) | Throughput | 95% Paired Confidence Interval |
| :--- | :--- | :---: | :---: | :---: |
| **CPU / ICICLE Pipeline**<br>*(Node/Circom W=8 + ICICLE CUDA)* | 156.24 s witness + 90.83 s prover | **247.07 s** (82.36 s/N16, 5.15 s/proof) | **0.194 proofs/s** | Baseline |
| **ExecMesh Native GPU Pipeline**<br>*(P3 GPU Witness + P5A Native Prover)* | 164.40 s witness + 104.69 s prover | **272.57 s** (90.86 s/N16, 5.68 s/proof) | **0.176 proofs/s** | **[-22.86 s, +73.87 s]**<br>*(Point estimate: +10.32%)* |

```text
EVALUATION SUMMARY:
In the 240-proof matched campaign, no statistically significant difference was detected
between the two full pipelines. The point estimate favored CPU+ICICLE by 10.3%, but host
system noise on the commodity machine exceeded the architectural delta.
```

---

## Why Performance is Circuit- and Lifecycle-Dependent

GPU witness acceleration does not automatically deliver a $2\times$ or $5\times$ speedup on every arbitrary circuit:
- On circuits dominated by high constraint-to-signal ratios or non-linear hashing (e.g. historical BatchTx / Poseidon), GPU witness generation significantly outpaces single-threaded CPU calculation.
- On circuits with large linear constraint chains or when compared against highly parallelized 8-core CPU witness pools (e.g. AnonAadhaar), GPU witness calculation performs at approximate parity with multi-core CPUs.
- **Why Run a Pilot?** The ExecMesh Design-Partner Pilot exists specifically to benchmark your team's specific circuit geometry and tell you exactly how your proving pipeline behaves on matched hardware.
