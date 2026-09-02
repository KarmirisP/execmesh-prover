# ExecMesh Technical Architecture & Prover Pipeline

## 1. High-Level Architecture

ExecMesh delivers end-to-end zero-knowledge proving acceleration by orchestrating two specialized, high-performance GPU daemons under strict $4\text{ GB}$ VRAM lifecycle management:

```text
[ Customer Input JSON ]
           │
           ▼
┌────────────────────────────────────────────────────────┐
│             ExecMesh Prover Architecture               │
│                                                        │
│  ┌──────────────────────────────────────────────────┐  │
│  │ 1. Persistent GPU Witness Daemon (P3 Engine)     │  │
│  │    • Montgomery field arithmetic in CUDA         │  │
│  │    • In-place topological signal evaluation      │  │
│  │    • 100% byte-identical binary WTNS v2 output   │  │
│  └────────────────────────┬─────────────────────────┘  │
│                           │ WTNS v2 Buffer             │
│  ┌────────────────────────▼─────────────────────────┐  │
│  │ 2. VRAM Lifecycle & Pre-Dispatch Validator       │  │
│  │    • Strict 4 GB memory residency transition     │  │
│  │    • <1 ms binary WTNS structural validation     │  │
│  └────────────────────────┬─────────────────────────┘  │
│                           │ Validated Witness          │
│  ┌────────────────────────▼─────────────────────────┐  │
│  │ 3. Native Groth16 GPU Prover Daemon (P5A Engine) │  │
│  │    • Persistent QAP polynomial memory cache      │  │
│  │    • High-occupancy Pippenger MSM on BN254       │  │
│  │    • Radix-2 Coset NTT evaluations               │  │
│  │    • Hardware CSPRNG blinding (r, s in Fr*)      │  │
│  └────────────────────────┬─────────────────────────┘  │
└───────────────────────────┼────────────────────────────┘
                            │
                            ▼
           [ Standard SnarkJS-Verifiable Proof ]
```

---

## 2. Core Subsystems

### 2.1 Persistent GPU Witness Engine (P3 Daemon)
- Computes non-linear and linear constraint assignments directly on GPU threads.
- Avoids host thread scheduling bottlenecks when executing complex cryptographic primitives (e.g. Poseidon, MiMC, SHA256).
- Emits canonical binary WTNS v2 files with exact cryptographic byte-parity against Circom WASM/C++.

### 2.2 Native Groth16 GPU Prover (P5A Daemon)
- Maintains the Groth16 proving key (`circuit.zkey`) and pre-calculated QAP coefficients resident in host/device memory.
- Performs Multi-Scalar Multiplications (MSM) on $G_1$ and $G_2$ elliptic curve points in parallel.
- Computes radix-2 Number Theoretic Transforms (NTT) for quotient polynomial evaluation $H(x)$.

### 2.3 Hardened Worker Subsystem & Bounded Subprocesses
- Pre-validates binary WTNS headers and variable counts in $<1\text{ ms}$ before allocating GPU memory.
- Supervises native CUDA processes with dedicated watchdog threads, enforcing strict wall-clock execution deadlines and zero-leakage recovery.
