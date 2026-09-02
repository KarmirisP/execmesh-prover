# ExecMesh Security & Data Handling Policy

## 1. Zero-Knowledge Cryptographic Blinding Model

ExecMesh strictly preserves the zero-knowledge property of the Groth16 proof system:
- In each proof generation cycle, the prover samples fresh, independent blinding scalars $r, s \in \mathbb{F}_r^*$ using a hardware-backed cryptographically secure pseudo-random number generator (CSPRNG).
- Blinded proof elements $A \in G_1$, $B \in G_2$, and $C \in G_1$ reveal zero computational information about private witness wires beyond the validity of the public statement.

---

## 2. Ephemeral Execution Mode (`--ephemeral-inputs`)

For customer evaluations and hosted proving:
- Private inputs and derived witness files (`.wtns`) stored in temporary scratch space are automatically unlinked and purged upon receipt generation.
- **Ephemeral Cleanup Receipt**: An execution receipt confirming the unlinking and purging of scratch files is provided with proof outputs.
- *(Note: While ExecMesh purges files from its active application workflows, forensic hardware-level sanitization depends on the underlying infrastructure).*

---

## 3. Quarantined Ingestion & Provenance

- Customer-provided compiled artifacts are stored in isolated read-only quarantine paths (`0444`).
- All assets are tracked via immutable SHA-256 digests in machine-readable `execmesh_circuit_manifest.json` files.
- Customer proprietary assets are never committed to public repositories or shared remote backups.
