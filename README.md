# ExecMesh GPU Circom/Groth16 Proving
### GPU Witness Generation + Native GPU Groth16 Prover

ExecMesh is a proprietary, end-to-end zero-knowledge acceleration stack that unifies **GPU-native Circom witness generation** and **native CUDA Groth16 proving** on commodity hardware.

---

## 1. What Exists Today

- **Proprietary GPU Circom Witness Engine**: Hardware-accelerated witness calculation generating $100\%$ byte-identical binary WTNS v2 output against canonical Circom WASM/C++.
- **Proprietary Native Groth16 GPU Prover**: Fast CUDA BN254 Groth16 prover with persistent QAP memory residency and Pippenger MSM / NTT acceleration.
- **Independent Cryptographic Verification**: $100\%$ of generated proofs strictly verify against standard verification keys using standard open-source verifiers (`snarkjs groth16 verify`).
- **Commodity 4 GB VRAM Architecture**: Verified on the current 4 GB configuration through the AnonAadhaar 1.104M-variable / $2^{21}$-domain workload. Larger workloads require preflight qualification and/or additional VRAM.
- **Automated Customer Circuit Onboarding**: Standardized preflight and evaluation tooling for external Circom/Groth16 circuits.

---

## 2. Verified Benchmark Evidence

We believe in complete transparency and reproducible engineering evidence. All benchmarks are conducted on identical hardware and verified cryptographically.

### Current Matched Campaign (AnonAadhaar, 1,104,354 Variables / $2^{21}$ Domain)
*Hardware: NVIDIA GeForce GTX 1650 (4 GB GDDR5) / Intel Xeon E3-1245 v2 @ 3.40 GHz (4C/8T)*

| Pipeline Component | Node/Circom CPU Witness (W=8) + ICICLE CUDA Groth16 | ExecMesh Native GPU Stack (P3 Witness + P5A Prover) | Paired Comparison & Verdict |
| :--- | :---: | :---: | :---: |
| **Witness Generation Phase** | 52.08 s / N16 (3.255 s/witness) | 54.80 s / N16 (3.425 s/witness) | ExecMesh ~5.2% slower in matched campaign |
| **Groth16 Prover Phase** | 30.28 s / N16 (1.893 s/proof) | 34.90 s / N16 (2.181 s/proof) | ExecMesh ~15.2% slower in matched campaign |
| **Total Compute Path** | **82.36 s / N16 (5.147 s/proof)** | **90.86 s / N16 (5.679 s/proof)** | **Point estimate: ExecMesh 10.3% slower**<br>*(95% CI: `[-22.86 s, +73.87 s]` per 48 proofs)* |
| **Cryptographic Verification** | **240 / 240 PASS (100.0%)** | **240 / 240 PASS (100.0%)** | **100.0% Byte Parity & SnarkJS Pass** |

> **Matched Campaign Verdict**: In the five-pair matched campaign ($K=3 \times N=16 = 48\text{ proofs/replicate}$, total 240 proofs/arm), **no statistically significant compute-path difference was detected between ExecMesh Native and the reference pipeline**. Run-to-run system noise on the commodity test environment exceeded the architectural difference.

- **Detailed Matched Benchmarks**: See [docs/BENCHMARKS_CURRENT_2026_09.md](docs/BENCHMARKS_CURRENT_2026_09.md)
- **Direct GPU Witness Q&A**: See [docs/GPU_WITNESS_AND_PIPELINE_RESULTS.md](docs/GPU_WITNESS_AND_PIPELINE_RESULTS.md)
- **Historical Benchmarks (vs Rapidsnark)**: See [docs/BENCHMARKS_HISTORICAL.md](docs/BENCHMARKS_HISTORICAL.md)

---

## 3. Product & Delivery Modalities

1. **Engine Licensing**: Direct binary daemon / library integration into existing prover fleets (e.g. rollup sequencers, proof aggregators).
2. **Managed Proving Service**: Cloud proving API (`POST /v1/jobs/submit`) for automated proof dispatch, worker health monitoring, and cryptographic receipt settlement.
3. **Private Appliance**: Dedicated deployment inside customer VPC / on-premise infrastructure where private witnesses never leave customer security boundaries.

---

## 4. Documentation

- [GPU Witness Generation & Full Pipeline Results](docs/GPU_WITNESS_AND_PIPELINE_RESULTS.md)
- [Current Benchmark Methodology & Results](docs/BENCHMARKS_CURRENT_2026_09.md)
- [Historical Benchmarks (vs Rapidsnark)](docs/BENCHMARKS_HISTORICAL.md)
- [Technical Architecture](docs/TECHNICAL_OVERVIEW.md)
- [Cryptographic Security & Blinding Model](docs/SECURITY.md)
- [Supported Circuit Scope & Boundaries](docs/SUPPORTED_CIRCUITS.md)
- [Design Partner Program](docs/DESIGN_PARTNER.md)
- [Current System Limitations](docs/LIMITATIONS.md)

---

## 5. Design Partner Evaluation

We offer **Design-Partner Acceleration Pilots** for teams running production Circom/Groth16 circuits:
- **Prover-Only Evaluation**: Provide compiled `circuit.wasm`, `circuit.zkey`, `verification_key.json`, and sample `inputs.json` (zero source code required).
- **Custom GPU Witness Schedule Port**: Provide `.circom` source and dependencies for custom GPU compilation.
- **Deliverables**: Comprehensive benchmark report comparing your current pipeline against ExecMesh on matched hardware, with independent verification receipts.

To schedule a pilot or request a private evaluation bundle, email `partners@execmesh.io`.

---

## License

Documentation and schemas are published under the [Apache 2.0 License](LICENSE). ExecMesh binaries and compiler toolchains are proprietary software governed by the ExecMesh Evaluation License.


---

## 3. Technical Demonstration Video

A complete end-to-end recorded run of our self-serve client evaluation suite (`./demo/verify_all.sh`) on NVIDIA GeForce GTX 1650 (4 GB VRAM):

- **Current Production Pipeline Demo (September 2026)**: [execmesh_gpu_witness_pipeline_demo_20260902.mp4](execmesh_gpu_witness_pipeline_demo_20260902.mp4)  
  *Demonstrates: Hardware preflight → Node/Circom CPU witness vs ExecMesh GPU witness (100% byte-parity) → Native Groth16 GPU proof generation → SnarkJS cryptographic verification.*
- **Historical BatchTx / Rapidsnark Demo (Archival August 2026)**: [docs/archive/historical_batchtx_rapidsnark_demo_20260816.mp4](docs/archive/historical_batchtx_rapidsnark_demo_20260816.mp4)
