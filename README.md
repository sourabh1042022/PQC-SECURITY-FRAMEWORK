# Post-Quantum Cryptography Security Framework

A practitioner-level framework for enterprise transition from quantum-vulnerable cryptographic algorithms to NIST-standardized post-quantum cryptography (PQC). Includes a formal adoption policy, phased migration strategy, and comparative algorithm research.

---

## Overview

Classical public-key algorithms — RSA, ECC, Diffie-Hellman — will be rendered insecure once a cryptographically relevant quantum computer (CRQC) becomes available. The **"Harvest Now, Decrypt Later"** threat model means that encrypted data intercepted today can be stored and decrypted in the future, making immediate action necessary for any system protecting long-term confidential data.

This framework provides:
- An enforceable enterprise policy for PQC adoption
- A phased, risk-managed migration strategy
- A comparative analysis of the two leading NIST PQC candidates

---

## Repository Structure

```
pqc-security-framework/
├── README.md
├── policy/
│   └── pqc-adoption-policy.md          # Enforceable PQC transition policy
├── research/
│   ├── pqc-transition-strategy.md      # Phased migration strategy (3 phases)
│   └── kyber-vs-mceliece-analysis.md   # Algorithm comparison research paper
└── diagrams/
    └── migration-tiers.md              # Risk-tiered migration framework
```

---

## Key Documents

### 1. PQC Adoption Policy
Establishes enforceable organizational requirements for transitioning away from quantum-vulnerable cryptography. Covers:
- Freeze on new quantum-vulnerable deployments
- Mandatory crypto-agility requirements
- Approved PQC algorithm baseline (Kyber, Dilithium, SPHINCS+)
- Procurement and vendor requirements
- Governance structure (PQC Working Group)

**Standards aligned to:** NIST FIPS 203–205, NSA CNSA 2.0, ENISA, ETSI

### 2. PQC Transition Strategy
Three-phase migration roadmap:
- **Phase 1 — Discovery:** Cryptographic asset inventory across all systems, APIs, and third-party dependencies
- **Phase 2 — Risk Assessment:** Tiered prioritization based on data sensitivity, system lifespan, exposure, and business criticality
- **Phase 3 — Pilot & Rollout:** Hybrid cryptography deployment (classical + PQC in parallel), followed by full PQC migration over 4–5 years

### 3. Kyber vs McEliece Analysis
Technical comparison of the two primary NIST PQC candidates for key encapsulation:

| Property | CRYSTALS-Kyber | Classic McEliece |
|---|---|---|
| Basis | Lattice (Module-LWE) | Code (Goppa codes) |
| Public key size | ~1,088 bytes | ~261,120 bytes |
| Ciphertext size | ~1,088 bytes | ~128 bytes |
| Cryptanalytic history | ~15 years | ~45 years |
| NIST status | Standardized (FIPS 203) | Alternate candidate |
| Best use case | General enterprise, TLS | High-assurance, static keying |

---

## Standards & Frameworks Referenced

| Standard | Relevance |
|---|---|
| NIST FIPS 203 | Kyber (ML-KEM) standardization |
| NIST FIPS 204 | Dilithium (ML-DSA) standardization |
| NIST FIPS 205 | SPHINCS+ standardization |
| NSA CNSA 2.0 | Commercial National Security Algorithm Suite — quantum-safe mandates |
| ENISA PQC Guidelines | European cryptographic migration advisories |
| ETSI QSC | Quantum-safe cryptography standards |

---

## Migration Tier Summary

| Tier | Risk Profile | Target Timeline |
|---|---|---|
| Tier 1 | Long-term sensitive data, high exposure, non-upgradable systems | Year 1–2 |
| Tier 2 | Medium sensitivity, moderate lifespan, internal services | Year 2–3 |
| Tier 3 | Low sensitivity, short-lived data, legacy systems near decommission | Year 4+ |

---

## Threat Model

**Primary concern — Harvest Now, Decrypt Later:**
Adversaries intercept and store encrypted traffic today with the intent to decrypt it once quantum computing matures. Any data requiring confidentiality beyond 2030 is already at risk under current classical cryptographic protections.

**Affected algorithms (targeted for deprecation):**
- RSA (all key sizes)
- ECDSA, ECDH (all curves)
- DSA
- Diffie-Hellman (classic)

**Safe algorithms (to be strengthened, not replaced):**
- AES-256 (Grover's algorithm halves effective key length — AES-128 becomes 64-bit equivalent)
- SHA-384 / SHA-512

---

## Author

**Sourabh Kumar** — Cybersecurity Analyst  
[LinkedIn](https://www.linkedin.com/in/sourabh-kumar-73b549227/) | [Medium](https://medium.com/@sourabhkumar1043)

> Produced as part of internship research at Gardiyan System Security Technologies, July 2025.
> All content is original research. No proprietary or confidential organizational data is included.

---

## Disclaimer

This framework is published for educational and professional reference purposes. It is based on publicly available NIST, NSA, and ENISA guidance. Organizations should adapt policies and migration timelines to their specific risk profile, regulatory environment, and technology stack.
