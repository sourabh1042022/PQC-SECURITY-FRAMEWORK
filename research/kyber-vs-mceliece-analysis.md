# CRYSTALS-Kyber vs Classic McEliece: A Comparative Analysis for Enterprise PQC Deployment

**Author:** Sourabh Kumar, Cybersecurity Analyst  
**Date:** July 2025  
**Context:** NIST Post-Quantum Cryptography Standardization Project

---

## Abstract

The advent of quantum computing poses a fundamental threat to classical public-key cryptography, necessitating the adoption of post-quantum cryptographic (PQC) algorithms. Among the algorithms evaluated by NIST, CRYSTALS-Kyber and Classic McEliece represent two structurally distinct approaches to quantum-resistant key encapsulation mechanisms (KEMs). This paper compares them across mathematical foundations, security assumptions, key sizes, performance, implementation practicality, and use-case suitability — with the goal of providing practitioners and decision-makers a grounded understanding of the trade-offs involved in selecting PQC algorithms for real-world deployment.

---

## 1. Introduction

At the heart of the quantum threat is **Shor's algorithm** — a quantum algorithm that efficiently solves integer factorization and discrete logarithm problems, rendering RSA, DSA, ECDSA, and Diffie-Hellman cryptographically obsolete once a sufficiently powerful quantum computer (CRQC) is realized.

Key Establishment Mechanisms (KEMs) are particularly vulnerable. They are foundational to TLS, SSH, VPN tunnels, and encrypted email. Under the **"Harvest Now, Decrypt Later"** threat model, organizations must adopt quantum-resistant KEMs immediately for systems protecting long-term confidential data.

NIST selected **CRYSTALS-Kyber** as the primary KEM standard (FIPS 203), while **Classic McEliece** remains under consideration as an alternate candidate for high-assurance use cases.

---

## 2. Algorithm Overview

### 2.1 CRYSTALS-Kyber

CRYSTALS-Kyber is a lattice-based KEM grounded in the **Module Learning with Errors (Module-LWE)** problem — a quantum-resistant variant of LWE formulated over structured polynomial rings.

**Design characteristics:**
- Operates on polynomial arithmetic over finite fields using fast Number Theoretic Transforms (NTT)
- IND-CCA2 secure — resistant to chosen ciphertext attacks
- Compact key and ciphertext sizes suitable for network transmission and constrained devices

**Key parameters (Kyber-768, NIST Security Level 3):**

| Parameter | Size |
|---|---|
| Public key | ~1,088 bytes |
| Private key | ~2,400 bytes |
| Ciphertext | ~1,088 bytes |
| Security | ~192-bit classical / 128-bit quantum |

**Operational flow:**
1. Sender uses the recipient's public key to encapsulate a symmetric key
2. Recipient decapsulates the ciphertext using their private key
3. The symmetric key is used in AES-GCM or equivalent for the session

**Library support:** liboqs, OpenSSL 3.0+, BoringSSL (experimental)

---

### 2.2 Classic McEliece

Classic McEliece is a code-based public-key encryption scheme originally proposed in **1978**. It relies on the hardness of decoding random linear error-correcting codes, specifically **binary Goppa codes** — a problem that has resisted both classical and quantum cryptanalysis for over 45 years.

**Design characteristics:**
- Encryption involves adding random error vectors to codewords, decodable only with the secret private key structure
- Very high encryption/decryption speed
- Extremely large public key sizes — the primary practical limitation

**Key parameters (Level 5 security):**

| Parameter | Size |
|---|---|
| Public key | ~261,120 bytes (~256 KB) |
| Private key | ~6,492 bytes |
| Ciphertext | ~128 bytes |
| Security | ~256-bit classical / 128-bit quantum |

**Best fit:** Root certificate authorities, firmware signing, satellite systems — environments where bandwidth is not the bottleneck but cryptanalytic robustness is paramount.

---

## 3. Security Assumptions and Cryptanalytic Maturity

### 3.1 Kyber: Lattice-Based Security

**Mathematical basis:** Module-LWE — given noisy linear equations over a polynomial ring, it is computationally infeasible (even for a quantum computer) to recover the secret vector.

**Strengths:**
- No known polynomial-time classical or quantum algorithm solves Module-LWE at Kyber's parameter levels
- Module-LWE reduces to worst-case lattice problems (GapSVP), providing strong theoretical backing
- Extensively studied during all rounds of the NIST PQC competition

**Caveats:**
- Lattice cryptography is a relatively newer field (~20 years of study)
- Algebraic structure introduces concerns about side-channel attacks during number theoretic transforms
- Potential vulnerability to future advances in lattice reduction algorithms (none currently practical)

---

### 3.2 McEliece: Code-Based Security

**Mathematical basis:** Decoding binary Goppa codes — the security rests on the assumption that decoding a general linear code is NP-hard.

**Strengths:**
- Underlying problem has resisted all known attacks — classical or quantum — for **45+ years**
- Not vulnerable to Shor's algorithm, Grover's algorithm, or any algebraic code analysis technique in use today
- Immune to certain side-channel attacks due to stateless nature and minimal arithmetic complexity

**Caveats:**
- Limited diversity in Goppa code constructions — theoretically vulnerable if future mathematical breakthroughs uncover structural weaknesses
- Security does not reduce to a worst-case problem in the same clean way lattice problems do

---

### Security Profile Summary

| Property | CRYSTALS-Kyber | Classic McEliece |
|---|---|---|
| Underlying problem | Module-LWE (Lattice) | Goppa Code Decoding (Code) |
| Cryptanalytic exposure | ~15 years | ~45+ years |
| Known quantum attacks | None practical | None |
| Parameter confidence | Strong, evolving | Extremely conservative |
| NIST status | Finalized (FIPS 203) | Alternate candidate |
| Side-channel resistance | Moderate (hardened impls needed) | Strong (simple operations) |

---

## 4. Performance and Deployment Comparison

### 4.1 Key Size and Bandwidth

| Metric | CRYSTALS-Kyber (768) | Classic McEliece (Level 5) |
|---|---|---|
| Public key | ~1,088 bytes | ~261,120 bytes (~256 KB) |
| Private key | ~2,400 bytes | ~6,500 bytes |
| Ciphertext | ~1,088 bytes | ~128 bytes |

Kyber is highly compact and practical for TLS, SSH, mobile, and IoT. McEliece's 256 KB public key makes it impractical for protocols requiring frequent key exchange.

### 4.2 Computational Speed

| Operation | Kyber-768 | Classic McEliece |
|---|---|---|
| Key generation | ~0.5 ms | ~1.2 ms |
| Encryption | ~0.1 ms | < 0.05 ms |
| Decryption | ~0.15 ms | < 0.03 ms |

McEliece is faster at encryption/decryption but slower at key generation. Both are operationally acceptable — the key size difference is the deciding factor in most deployment decisions.

### 4.3 Protocol Compatibility

| Factor | CRYSTALS-Kyber | Classic McEliece |
|---|---|---|
| TLS 1.3 | Native hybrid support | Impractical (key size) |
| SSH | Supported (hybrid modes) | Impractical |
| IPsec / VPN | Supported | Impractical |
| Code signing | Supported | Suitable (static keying) |
| IoT / embedded | Suitable | Memory-constrained |
| Firmware / satellite | Suitable | Ideal |

### 4.4 Full Deployment Factor Summary

| Factor | CRYSTALS-Kyber | Classic McEliece |
|---|---|---|
| Key & ciphertext size | Very compact | Extremely large public key |
| Runtime speed | Fast | Very fast |
| Protocol compatibility | Native/hybrid ready | Impractical for handshakes |
| Integration maturity | Production-ready | Experimental / limited |
| Ecosystem support | Widespread (FIPS 203) | Niche / high-assurance |

---

## 5. Conclusion and Recommendation

Kyber and McEliece embody different ends of the security-practicality spectrum.

**Kyber delivers:**
- Strong theoretical guarantees via Module-LWE hardness
- Efficient performance across all operations
- Compact key sizes enabling seamless integration into TLS, SSH, and modern protocols
- Widespread industry support with NIST standardization (FIPS 203)

**McEliece delivers:**
- Unmatched historical resilience — 45+ years of cryptanalytic exposure with no breaks
- Ultra-conservative security posture against future quantum cryptanalysis
- Fast encryption/decryption for static environments
- But: prohibitive public key sizes and poor protocol compatibility

### Recommendation for Enterprise Deployment

| Use Case | Recommended Algorithm |
|---|---|
| General enterprise (TLS, APIs, web services) | CRYSTALS-Kyber |
| Mobile and IoT | CRYSTALS-Kyber |
| VPN and SSH | CRYSTALS-Kyber (hybrid with ECDH during transition) |
| Code signing (CI/CD) | CRYSTALS-Dilithium |
| Firmware validation / satellite uplinks | Classic McEliece |
| Long-term offline key storage | Classic McEliece |
| Government-grade cold storage | Classic McEliece |

**Recommended transition approach:**
1. Adopt **Kyber-768** (or Kyber-1024 for higher threat models) as the default KEM across all systems
2. Deploy **hybrid modes** (ECDH + Kyber) during migration to preserve backward compatibility
3. Retain **McEliece** as a fallback for offline, static keying environments where ultimate resilience is prioritized over bandwidth

This dual-strategy ensures both crypto-agility and long-term survivability against cryptographically relevant quantum computers.

---

## References

- [NIST FIPS 203 — ML-KEM (Kyber)](https://csrc.nist.gov/pubs/fips/203/final)
- [CRYSTALS-Kyber Algorithm Specification](https://pq-crystals.org/kyber/)
- [Classic McEliece — NIST Submission](https://classic.mceliece.org/)
- [Open Quantum Safe — liboqs](https://openquantumsafe.org/)
- [NIST PQC Standardization Project](https://csrc.nist.gov/Projects/post-quantum-cryptography)
- [NSA CNSA 2.0 Algorithm Guidance](https://media.defense.gov/2022/Sep/07/2003071834/-1/-1/0/CSA_CNSA_2.0_ALGORITHMS_.PDF)
