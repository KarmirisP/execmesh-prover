# ExecMesh System Boundaries & Current Technical Limitations

ExecMesh is engineered for high-throughput zero-knowledge acceleration, but maintains explicit operational boundaries in its current production release:

1. **Curve & Proof System Scope**: Current production releases exclusively accelerate **BN254 (alt_bn128) Groth16** circuits. Other curves (BLS12-381) and proof systems (Plonk, STARKs) are not supported.
2. **GPU Witness Generation Porting**: GPU witness acceleration requires precompiled schedule topologies. Unported circuits fall back to optimized CPU witness pre-dispatch (`CPU_PRE_DISPATCH_PORT_REQUIRED`) while using the native GPU Groth16 prover.
3. **Hardware Requirements**: Requires NVIDIA GPUs with Compute Capability $\ge 7.5$ (Turing architecture or newer, e.g. GTX 1650, RTX 20/30/40 series, A10/A100/H100) and CUDA Driver $\ge 525$.
4. **Data Retention**: Application-level ephemeral purging deletes scratch inputs upon receipt creation; forensic secure erasure depends on host infrastructure.
