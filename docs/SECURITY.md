# ExecMesh Cryptographic Security & Disclosure

## Groth16 Compliance & Zero-Knowledge Guarantee

ExecMesh produces Groth16 zk-SNARK proofs over the BN254 curve ($y^2 = x^3 + 3$).

### Zero-Knowledge Randomization (CSPRNG Blinding)
The Groth16 proving protocol achieves computational zero-knowledge by randomizing proof points using blinding scalars $r, s \in \mathbb{F}_r^*$:

$$A = \alpha + \sum_{i=0}^m a_i L_i(x) + r \delta$$
$$B = \beta + \sum_{i=0}^m b_i R_i(x) + s \delta$$
$$C = \frac{\sum_{i=l+1}^m a_i (u_i(x) + v_i(x) + w_i(x)) + h(x) t(x) + s A + r B - r s \delta}{\delta}$$

ExecMesh samples $r$ and $s$ at runtime directly from a cryptographically secure random entropy source (`/dev/urandom`). Proofs generated from identical inputs are guaranteed to possess distinct group elements and unique SHA-256 digests while remaining universally verifiable.

---

## Artifact Integrity & Package Security

ExecMesh packages utilize cryptographic integrity gating:
- Every relocatable package (`manifest.json`) includes SHA-256 digests of all schedule binaries, proving keys, and verification parameters.
- Prover runtimes verify artifact integrity prior to GPU execution.
- Proof generation fails gracefully if artifact tampering or checksum mismatches are detected.
