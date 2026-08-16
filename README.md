# ExecMesh: Device-Resident GPU Zero-Knowledge Groth16 Prover

<p align="center">
  <strong>High-Throughput, Low-Latency ZK-SNARK Prover with Zero Host-to-Device Witness Overhead</strong>
</p>

---

## Overview

In conventional zero-knowledge proving pipelines, witness generation occurs on the CPU (in Node.js/WASM or native C++), serializing millions of field elements across the PCIe bus before GPU-accelerated multi-scalar multiplication (MSM) and number-theoretic transform (NTT) can begin. This memory boundary forms a major throughput and latency bottleneck.

**ExecMesh** is a hardware-accelerated zero-knowledge proving system that fuses Circom circuit evaluation and Groth16 proving directly inside NVIDIA GPU device memory. By keeping witness calculation entirely on-device, ExecMesh achieves **zero host-to-device witness memory transfer (0 bytes D2H)**, cutting end-to-end latency and maximizing GPU compute utilization.

---

## Video Demonstration

A full synchronized live terminal demonstration ([`execmesh_demo.mp4`](execmesh_demo.mp4), 2m 44s) is included in this repository, showing:
1. **Hardware Baseline Inspection**: Standard commodity setup (NVIDIA GeForce GTX 1650 4GB VRAM & Intel Xeon CPU).
2. **Preflight Diagnostics (`execmesh doctor`)**: Verification of GPU compute architecture, CUDA driver, available VRAM, and Circom toolchain dependencies.
3. **End-to-End Proving (`execmesh demo`)**: Device-resident witness generation, Groth16 proof generation, snarkjs verification, and non-deterministic CSPRNG blinding ($r, s \in \mathbb{F}_r^*$).
4. **Live Side-by-Side Benchmark (`execmesh demo --live-compare batchtx`)**: Direct invocation comparison on the 278k constraint BatchTx circuit measuring a 2.50x live speedup over conventional CPU witness + Rapidsnark.
5. **Audited Performance Matrix (`execmesh demo --comparison batchtx`)**: Review of published 20-vector benchmark matrices.

---

## Key Technical Highlights

- **Device-Resident Witness Execution**: Circuit constraints and signal assignments are compiled into optimized CUDA execution schedules that execute entirely on GPU cores.
- **Unified Memory Pipeline**: Witness pointers are passed directly into the fused BN254 MSM/NTT GPU engine without roundtripping through host RAM or PCIe.
- **Production Cryptographic Security**: Strict Groth16 zero-knowledge compliance with active CSPRNG blinding scalars ($r, s \in \mathbb{F}_r^*$), generating unique verifiable proofs on every invocation.
- **Relocatable Package Architecture (`execmesh-package-v1`)**: Self-contained circuit packages with cryptographic SHA-256 integrity gating and standard `snarkjs` compatibility.

---

## Published Benchmark Matrix

All figures are measured from audited, reproducible multi-vector benchmark suites on commodity hardware (NVIDIA GeForce GTX 1650 4GB VRAM vs. Intel Xeon CPU baseline):

| Circuit | Constraints / Signals | Conventional Baseline (p50)<br>*(Circom C++ &rarr; Rapidsnark)* | ExecMesh GPU (p50)<br>*(Device-Resident)* | Latency Advantage |
| :--- | :--- | :--- | :--- | :--- |
| **Pedersen(512)** | 6,519 constraints / 7,032 wires | ~1,850 ms | ~1,010 ms | **1.83x** |
| **SHA256(512)** | 62,528 constraints / 62,561 wires | 2,709 ms | 2,441 ms | **1.11x** |
| **BatchTx(278k)** | 278,128 constraints / 551,768 wires | 19.44 s | 9.89 s | **1.97x** |

*Detailed benchmark methodology and reproducibility instructions are documented in [docs/BENCHMARKS.md](docs/BENCHMARKS.md).*

---

## Documentation

- [Technical Architecture & Deep Dive](docs/TECHNICAL_OVERVIEW.md)
- [Benchmark Protocol & Results](docs/BENCHMARKS.md)
- [Cryptographic Security & Blinding Model](docs/SECURITY.md)
- [Supported Circuits & Bounded Circom Support](docs/SUPPORTED_CIRCUITS.md)
- [Design Partner Program & Custom Circuit Evaluation](docs/DESIGN_PARTNER.md)

---

## Client Evaluation Package

Evaluation binaries and pre-compiled benchmark packages are available for qualifying zero-knowledge development teams and rollup operators.

- **Encrypted Evaluation Distribution**: `ExecMesh_Client_Evaluation_v1.tar.gz.gpg`
- **Cloud Download Link**: [Google Drive Secure Distribution](https://drive.google.com/open?id=1cvjOe6tSwALVGe1OblBesp9SCqt3swkJ)
- **SHA-256 Integrity Digest**: `57c12d95a4b198c99551afb3b0140bb679c20be65206b46991814545a706282e`

To request the symmetric decryption key, evaluation license, or to benchmark your team's custom Circom circuits, see the [Design Partner Program](docs/DESIGN_PARTNER.md) or email `partners@execmesh.io`.

---

## License

The documentation and specifications in this repository are published under the [Apache 2.0 License](LICENSE). ExecMesh binaries and compiler toolchains are proprietary software governed by the [ExecMesh Evaluation License](docs/DESIGN_PARTNER.md).
