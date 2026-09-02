# ExecMesh Supported Circuits & Framework Boundaries

## 1. Supported Cryptographic Scope

- **Proof System**: Groth16 (`snarkjs` / `circom_runtime` compatible)
- **Elliptic Curve**: `BN254` (`alt_bn128`)
- **Circuit Framework**: Circom (`v2.0.0` through `v2.2.x`)
- **Witness Format**: Binary WTNS v2 (256-bit prime field canonical representation)
- **Circuit Capacity**: Verified on the current 4 GB configuration through the AnonAadhaar 1.104M-variable / $2^{21}$-domain workload. Larger workloads require preflight qualification and/or additional VRAM. Larger circuits supported via expanded memory or multi-batch scheduling.

---

## 2. Onboarding Requirements by Evaluation Mode

### Mode A: Prover-Only Evaluation (Zero Source Code Required)
To evaluate the ExecMesh Native Groth16 GPU Prover against your existing witness pipeline:
- Provide: `circuit.wasm`, `circuit.zkey`, `verification_key.json`, and sample `inputs/*.json`.
- *(Zero proprietary circuit source code required).*

### Mode B: Custom GPU Witness Porting
To evaluate the proprietary ExecMesh GPU Witness Engine on a new circuit:
- If the circuit is not yet compiled into an ExecMesh GPU schedule, the onboarding toolchain flags `CPU_PRE_DISPATCH_PORT_REQUIRED`.
- Compiling a custom GPU witness schedule requires: `.circom` source files, include dependencies (e.g. `circomlib`), and parameter definitions.
