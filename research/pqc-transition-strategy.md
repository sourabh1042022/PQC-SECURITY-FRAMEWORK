# PQC Transition Strategy

**Author:** Sourabh Kumar, Cybersecurity Analyst  
**Date:** July 2025  
**Aligned to:** NIST FIPS 203–205 | NSA CNSA 2.0 | ENISA

---

## 1. Executive Summary

Classical public-key algorithms (RSA, ECC, Diffie-Hellman) will be rendered insecure once a cryptographically relevant quantum computer (CRQC) becomes available. This document outlines a risk-managed, phased transition strategy to post-quantum cryptography (PQC), prioritizing action based on data sensitivity, system lifespan, and cryptographic agility.

The strategy is structured into three phases:

| Phase | Name | Focus |
|---|---|---|
| Phase 1 | Discovery | Cryptographic asset inventory |
| Phase 2 | Risk Assessment | Tiered prioritization by risk |
| Phase 3 | Pilot & Rollout | Hybrid deployment and full migration |

---

## 2. Threat Landscape

### The Quantum Threat to Public-Key Cryptography

**Shor's algorithm** — running on a CRQC — can solve integer factorization and discrete logarithm problems in polynomial time, breaking RSA, ECC, and DH.

**Grover's algorithm** — provides quadratic speedup against symmetric ciphers, reducing AES-128 to an effective 64-bit security level.

### Harvest Now, Decrypt Later

> Adversaries can passively capture encrypted traffic today and retain it until quantum decryption capability is available.

Data requiring long-term confidentiality (financial records, medical data, legal contracts, intellectual property) is already at risk under current cryptographic protections.

**Timeline:** A CRQC is estimated to be viable within 10–15 years. Cryptographic transitions take years to complete. Organizations cannot wait.

### Regulatory Response

| Body | Action |
|---|---|
| NIST | Finalized FIPS 203 (Kyber), 204 (Dilithium), 205 (SPHINCS+) |
| NSA | Published CNSA 2.0 mandating quantum-resistant algorithm adoption |
| ENISA / ETSI | Issued PQC migration advisories and crypto-agility requirements |

---

## 3. Approved PQC Migration Targets

### Key Encapsulation (replacing RSA / ECDH)

**Primary:** CRYSTALS-Kyber (FIPS 203 / ML-KEM)
- Compact keys (~1,088 bytes public key at Kyber-768)
- Fast operations, suitable for TLS, SSH, VPN, APIs
- Standardized and production-ready

### Digital Signatures (replacing ECDSA / RSA signatures)

| Algorithm | Standard | Best For |
|---|---|---|
| CRYSTALS-Dilithium | FIPS 204 | General-purpose — default replacement |
| FALCON | — | Constrained environments (IoT, smart cards) |
| SPHINCS+ | FIPS 205 | Diversity fallback — hash-based, non-lattice |

### Symmetric Algorithms (strengthen, not replace)

| Current | Action |
|---|---|
| AES-128 | Upgrade to AES-256 |
| SHA-256 | Upgrade to SHA-384 or SHA-512 |
| AES-256, SHA-384+ | Retain as-is — quantum-safe |

---

## 4. Phase 1 — Cryptographic Inventory (Discovery)

### Objective

Identify and catalog all cryptographic assets across systems, applications, services, and third-party dependencies.

### What to Capture

For each cryptographic usage instance, record:
- Algorithm type and key length
- Function (TLS, S/MIME, VPN, SSH, code signing, etc.)
- Cryptographic library or implementation
- System owner and business unit
- Data sensitivity and confidentiality lifespan
- Whether the system supports crypto-agility

### Discovery Methods

**Automated:**
- Network TLS scans (`testssl.sh`, ZMap) to detect RSA/ECC usage on endpoints
- Source code and binary scanning for hardcoded algorithm references
- Certificate inventory crawling (internal/public CAs)
- Infrastructure agent scanning via Ansible, Chef, or SCCM

**Manual:**
- System owner interviews to surface undocumented cryptographic uses
- Third-party vendor disclosure requests
- Legacy asset review for non-upgradable devices

### Completion Metrics

- Inventory coverage (% of systems scanned and documented)
- Algorithm diversity map (RSA vs ECC vs others)
- TLS/SSH endpoint census
- Hardcoded / non-agile crypto count
- Systems eligible for hybrid crypto pilot

---

## 5. Phase 2 — Risk Assessment and Prioritization

### Risk Dimensions

Each cryptographic usage instance is evaluated across four dimensions:

**A. Data Sensitivity and Confidentiality Horizon**
- Data requiring confidentiality for 10+ years is high-risk under "Harvest Now, Decrypt Later"
- Examples: financial records, PHI, legal contracts, national security data

**B. System Lifespan and Upgradability**
- Embedded systems, IoT devices, and legacy infrastructure with 8–10+ year lifespans and no planned upgrades are prioritized for early PQC integration

**C. Exposure to External Threat Actors**
- Internet-facing APIs, web services, VPN gateways, mobile apps, and third-party-facing data pipelines are high-priority

**D. Business Criticality and Impact Scope**
- Identity providers, secure boot mechanisms, customer-facing portals, and high-availability systems are critical-path infrastructure

### Migration Tier Classification

| Tier | Risk Profile | Target Timeline |
|---|---|---|
| Tier 1 | Long-term sensitive data, high exposure, non-upgradable | Year 1–2 |
| Tier 2 | Medium sensitivity, moderate lifespan, internal services | Year 2–3 |
| Tier 3 | Low sensitivity, short-lived data, near decommission | Year 4+ |

### Review Cycles

Reassess risk tiers annually or when triggered by:
- New regulatory deadlines
- Vulnerabilities discovered in PQC algorithms
- Unexpected quantum computing advancements
- Scheduled infrastructure upgrades or product lifecycle events

---

## 6. Phase 3 — Pilot, Hybrid Deployment, and Full Rollout

### 6.1 Pilot Program

Validate PQC algorithms in controlled environments before enterprise-wide deployment.

**Pilot objectives:**
- Compatibility with existing infrastructure and protocols
- Performance and latency impact
- Key and certificate size implications on storage and bandwidth
- Interoperability across client, server, and third-party systems

**Example pilot candidates:**
- TLS handshake using Kyber-based KEM on internal test web services
- Code signing using Dilithium on internal CI/CD pipelines
- VPN tunnels using hybrid ECDH + Kyber key exchange
- CA-issued dual-signature certificates (ECDSA + Dilithium)

### 6.2 Hybrid Cryptography During Transition

Due to vendor limitations and ecosystem immaturity, a hybrid approach is used during the transition period.

**How it works:**
1. Two independent key exchanges are performed (e.g., ECDH + Kyber)
2. Both shared secrets are combined using a secure KDF (e.g., HKDF)
3. Session key is derived from the combined entropy

**Why hybrid:**
- If either algorithm remains unbroken, the session remains secure
- Mitigates implementation immaturity of PQC algorithms
- Maintains interoperability with legacy clients
- Provides cryptographic diversity

**Example — TLS 1.3 hybrid handshake:**
```
Client ←→ Server: ECDHE key exchange (classical)
Client ←→ Server: Kyber KEM (post-quantum)
Session key = HKDF(ECDHE_secret || Kyber_secret)
```

### 6.3 Full Rollout Timeline

| Year | Milestone |
|---|---|
| Year 1 | Complete pilots; finalize crypto-agility architecture; onboard PQC libraries into enterprise SDKs |
| Year 2 | Migrate all Tier 1 systems; deploy PQC-ready certificates and TLS endpoints |
| Year 3 | Migrate Tier 2 systems; begin new deployments with native PQC-only support where feasible |
| Year 4–5 | Migrate remaining Tier 3 assets or deprecate; remove fallback legacy crypto entirely |

---

## 7. Governance

### PQC Working Group (PQC-WG)

A cross-functional team under CISO authority responsible for:

**Composition:**
- Cryptographic engineering leads
- Application security architects
- Infrastructure and network security leads
- Procurement / compliance representatives
- Risk and legal advisors

**Responsibilities:**
- Maintain and review the cryptographic inventory and risk registry
- Approve PQC algorithms, libraries, and integration patterns
- Coordinate pilot programs and deployment schedules
- Track NIST/FIPS updates and NSA CNSA 2.0 adoption
- Report quarterly progress to executive leadership

### Development Enablement

- Maintain standardized PQC-ready SDKs and wrappers (Kyber, Dilithium, SPHINCS+)
- Define PQC integration guidelines for all development teams
- Mandate cryptographic abstraction layers in all new application designs
- Ban hardcoded algorithm identifiers in favor of modular configs

### Enforcement

- PQC policy compliance enforced via CI/CD hooks (static analysis for deprecated algorithms)
- Change control workflows require cryptographic impact analysis for all new or updated systems
- Exception processes with mitigation deadlines logged to PQC-WG monthly

---

## 8. References

- [NIST Post-Quantum Cryptography Standardization](https://csrc.nist.gov/Projects/post-quantum-cryptography)
- [NIST FIPS 203 — ML-KEM](https://csrc.nist.gov/pubs/fips/203/final)
- [NIST FIPS 204 — ML-DSA](https://csrc.nist.gov/pubs/fips/204/final)
- [NIST FIPS 205 — SLH-DSA](https://csrc.nist.gov/pubs/fips/205/final)
- [NSA CNSA 2.0](https://media.defense.gov/2022/Sep/07/2003071834/-1/-1/0/CSA_CNSA_2.0_ALGORITHMS_.PDF)
- [ENISA Post-Quantum Cryptography](https://www.enisa.europa.eu/topics/cryptography)
- [Open Quantum Safe Project](https://openquantumsafe.org/)
- [liboqs — Open-source PQC library](https://github.com/open-quantum-safe/liboqs)
