# ExecMesh Design Partner Program

## Bring Your Own Circuit (BYOC) Evaluation

ExecMesh is currently working with select zero-knowledge engineering teams, rollup operators, and privacy infrastructure developers to benchmark and accelerate proprietary Circom circuits.

### How the Evaluation Process Works

1. **Circuit Intake & Confidentiality**:
   - Design partners provide their `.circom` circuit definition, proving key (`.zkey`), and reference input/output vectors.
   - All client intellectual property is handled under standard mutual Non-Disclosure Agreements (NDA).

2. **Automated Compilation & Optimization**:
   - The ExecMesh compiler ingests the circuit topology, optimizes signal schedules, and compiles fused GPU execution binaries.
   - Circuit topology is verified across multi-vector invariance suites to guarantee mathematical equivalence with Circom reference witnesses.

3. **Evaluation Package Delivery**:
   - Design partners receive a relocatable, self-contained `execmesh-package-v1` evaluation bundle.
   - Partners benchmark proof latency and verify output proofs locally on their own GPU clusters without exposing internal compiler source code.

---

## Requesting Access

To request participation in the ExecMesh Design Partner Program or to obtain the standalone client evaluation bundle, please reach out to:

- **Email**: `partners@execmesh.io`
- **Technical Inquiries**: `eval@execmesh.io`
