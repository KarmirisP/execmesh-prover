# ExecMesh Design-Partner Acceleration Program

We invite zero-knowledge development teams, rollup operators, and privacy protocol engineers to participate in our **Design-Partner Acceleration Pilot**:

---

## 1. What We Provide

1. **Independent Matched Benchmark**: We benchmark your existing proving pipeline (CPU witness + ICICLE/Rapidsnark) against ExecMesh on identical hardware.
2. **Comprehensive Pilot Report**: Detailed latency analysis across witness generation, polynomial calculation, and MSM/NTT proving, plus proofs/sec throughput and cost-per-proof projections.
3. **Cryptographic Parity Receipts**: $100\%$ byte-parity witness validation and SnarkJS verification logs for every evaluated proof vector.
4. **Production Architecture Blueprint**: Deployment guidance for dedicated on-premise, VPC, or cloud proving infrastructure.

---

## 2. Evaluation Modes

- **Prover-Only Evaluation**: Zero source code required. Send compiled `circuit.wasm` + `circuit.zkey` + `verification_key.json` + sample inputs.
- **Full GPU Witness + Prover Port**: Provide `.circom` source + build dependencies for custom GPU witness schedule compilation.

---

## 3. How to Apply

Email `partners@execmesh.io` with:
1. Target circuit framework and rough constraint/wire count.
2. Current proving latency, hardware target, and volume requirements.
3. Your primary optimization goal (latency reduction, throughput expansion, or cloud cost minimization).
