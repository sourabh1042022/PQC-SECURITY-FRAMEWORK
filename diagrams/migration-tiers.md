# PQC Migration Tier Framework

A risk-based classification system for prioritizing cryptographic migration from quantum-vulnerable to post-quantum algorithms.

---

## Tier Classification

### Tier 1 — Immediate (Year 1–2)

**Risk profile:** Long-term sensitive data + high external exposure + non-upgradable systems

**Examples:**
- Internet-facing TLS endpoints handling financial or medical data
- VPN gateways and remote access infrastructure
- Identity providers and certificate authorities
- Long-lived embedded systems (IoT, OT, hardware appliances) with 10+ year lifespans
- Data pipelines storing records requiring 10+ year confidentiality

**Action:** Begin PQC pilot immediately; deploy hybrid cryptography in production by Year 2

---

### Tier 2 — Planned (Year 2–3)

**Risk profile:** Medium-sensitivity data + moderate system lifespan + primarily internal services

**Examples:**
- Internal APIs and microservices
- Internal authentication systems
- Development and staging infrastructure
- Corporate email and collaboration tools
- Database encryption for non-critical data

**Action:** Adopt PQC following Tier 1 pilot learnings; new deployments default to PQC-only where feasible

---

### Tier 3 — Deferred or Retire (Year 4+)

**Risk profile:** Low-sensitivity data + short-lived data + systems near planned decommission

**Examples:**
- Legacy systems scheduled for retirement within 3–5 years
- Short-lived session data (session tokens, ephemeral logs)
- Internal tools with limited data sensitivity and short operational lifespans

**Action:** Migrate or deprecate; do not invest in PQC upgrades for systems near end-of-life

---

## Risk Scoring Dimensions

Each system is scored across four dimensions to determine tier placement:

| Dimension | Low (1) | Medium (2) | High (3) |
|---|---|---|---|
| Data confidentiality horizon | < 3 years | 3–10 years | 10+ years |
| System lifespan | < 3 years | 3–8 years | 8+ years |
| External exposure | Internal only | Internal + partner-facing | Internet-facing |
| Business criticality | Non-critical | Important | Mission-critical |

**Scoring:**
- Total score 9–12 → **Tier 1**
- Total score 5–8 → **Tier 2**
- Total score 4 or below → **Tier 3**

---

## Hybrid Cryptography During Transition

All Tier 1 and Tier 2 systems use **hybrid cryptography** during the transition period:

```
Session Key = HKDF(Classical_Secret || PQC_Secret)
```

If either algorithm remains unbroken, the session remains secure. This provides:
- Backward compatibility with systems not yet PQC-capable
- Defense-in-depth against implementation immaturity of new PQC algorithms
- Cryptographic diversity across algorithm families (lattice + classical)

---

## Approved Algorithm Mapping

| Function | Current (Deprecated) | Replacement |
|---|---|---|
| Key exchange (TLS) | ECDHE | CRYSTALS-Kyber + hybrid ECDHE |
| Key exchange (SSH) | ECDH / DH | CRYSTALS-Kyber |
| Digital signature | ECDSA / RSA | CRYSTALS-Dilithium |
| Code signing | RSA-PSS | CRYSTALS-Dilithium |
| Constrained signing | ECDSA | FALCON |
| High-assurance fallback | — | SPHINCS+ |
| Symmetric encryption | AES-128 | AES-256 (Grover mitigation) |
| Hashing | SHA-256 | SHA-384 or SHA-512 |
