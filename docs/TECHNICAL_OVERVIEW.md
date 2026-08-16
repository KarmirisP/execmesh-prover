# ExecMesh Technical Architecture

## The Witness Memory Bottleneck

Standard zero-knowledge proving architectures divide proof generation into two distinct phases executed across heterogeneous hardware boundaries:

1. **Witness Generation (CPU)**: An arithmetic interpreter (e.g. `witnesscalc`, `snarkjs` WASM, or native C++ compiled from Circom) processes input signals and computes all intermediate wire assignments in host RAM.
2. **Proving Engine (GPU)**: The host process serializes the entire witness array ($10^5$ to $10^7$ 32-byte field elements) and transfers it over PCIe to GPU VRAM for Multi-Scalar Multiplication (MSM) and Number-Theoretic Transform (NTT) acceleration.

In large-scale zkRollups and transaction verifiers, this PCIe synchronization overhead introduces substantial latency, bus contention, and significant host memory pressure.

---

## The ExecMesh Fused Pipeline

ExecMesh eliminates the CPU-to-GPU memory transfer boundary by translating Circom constraint topologies directly into fused GPU computation graphs.

```text
[ Input JSON ]
      │
      ▼
┌────────────────────────────────────────────────────────┐
│                   ExecMesh GPU Runtime                 │
│                                                        │
│  ┌──────────────────────────────────────────────────┐  │
│  │ 1. Device-Resident Witness Kernel                │  │
│  │    • Montgomery field arithmetic in VRAM         │  │
│  │    • In-place signal propagation                 │  │
│  │    • Direct device pointer handoff (0 bytes D2H) │  │
│  └────────────────────────┬─────────────────────────┘  │
│                           │ Device Witness Pointer     │
│  ┌────────────────────────▼─────────────────────────┐  │
│  │ 2. Fused CUDA BN254 Groth16 Prover               │  │
│  │    • High-occupancy Pippenger MSM                │  │
│  │    • Radix-2 NTT / Coset Evaluations             │  │
│  │    • Hardware CSPRNG Blinding (r, s in Fr*)      │  │
│  └────────────────────────┬─────────────────────────┘  │
└───────────────────────────┼────────────────────────────┘
                            │
                            ▼
               [ Verified Groth16 Proof ]
```

### Key Innovations:
1. **Zero-Copy Device Handoff**: The full witness vector is never allocated in host memory or transferred across PCIe. The GPU witness kernel outputs device pointers directly to the proving engine.
2. **Kernel Fusion & Topology Gating**: Constant-folding and operation fusion reduce total kernel launches, maximizing warp occupancy on commodity GPUs.
3. **Reproducible ABI (`execmesh-package-v1`)**: Relocatable packages contain pre-compiled schedule binaries, initialization layouts, and cryptographic verification metadata, enabling seamless deployment across containerized proving clusters.
