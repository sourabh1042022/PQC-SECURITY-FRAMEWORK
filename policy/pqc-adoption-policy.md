# Post-Quantum Cryptography Adoption and Transition Policy

**Author:** Sourabh Kumar, Cybersecurity Analyst  
**Date:** July 2025  
**Standards:** NIST FIPS 203–205 | NSA CNSA 2.0 | ENISA | ETSI

---

## 1. Purpose and Scope

### 1.1 Purpose

This policy establishes enforceable organizational requirements for transitioning from quantum-vulnerable public-key cryptographic algorithms (RSA, ECC, DSA, DH) to NIST-standardized post-quantum cryptography (PQC). The goal is to ensure cryptographic systems remain secure against emerging quantum computing threats, including the risk of retrospective decryption of intercepted data ("harvest now, decrypt later").

This policy is aligned with:
- NIST Post-Quantum Cryptography Standardization Project (FIPS 203–205)
- NSA Commercial National Security Algorithm Suite 2.0 (CNSA 2.0)
- Global cryptographic migration advisories (ENISA, ETSI)

### 1.2 Scope

This policy applies to:
- All internal applications, APIs, services, infrastructure systems, and endpoints using public-key cryptography
- All third-party software, hardware appliances, and cloud-based products used within the organization
- All procurement activities involving systems that implement or rely upon public-key cryptographic functions
- All cryptographic deployments that protect data requiring confidentiality beyond the year 2030

**In scope:** Asymmetric cryptographic mechanisms (encryption, key exchange, digital signatures).

**Out of scope:** Symmetric algorithms (AES, HMAC) are not being replaced, but their configurations must follow strengthened parameters (AES-256, SHA-384+) to remain quantum-resistant.

---

## 2. Background: The Quantum Threat

Shor's algorithm running on a cryptographically relevant quantum computer (CRQC) can efficiently solve the integer factorization and discrete logarithm problems that underpin RSA, ECC, and Diffie-Hellman. This would break TLS handshakes, digital signatures, VPN tunnels, and encrypted email.

Grover's algorithm provides a quadratic speedup against symmetric ciphers, reducing AES-128 to an effective 64-bit security level — requiring migration to AES-256 and SHA-384+.

The **"Harvest Now, Decrypt Later"** threat model is driving immediate action: adversaries can capture and store encrypted traffic today and decrypt it once quantum capability matures. Any data requiring confidentiality for 10+ years is already at risk.

---

## 3. Policy Statements

### 3.1 Freeze on Quantum-Vulnerable Cryptographic Deployments

- Effective immediately, no system designed, procured, or deployed after this policy's issuance shall utilize quantum-vulnerable public-key algorithms (RSA, DSA, ECDSA, DH, ECDH) for any function requiring long-term data confidentiality (beyond 2030).
- Legacy systems already in production must be documented in the enterprise cryptographic inventory and scheduled for phased replacement per the PQC Transition Strategy.
- Any exception must be risk-justified, approved in writing by the PQC Working Group, and include a remediation timeline not exceeding 12 months.

### 3.2 Mandatory Cryptographic Agility

- All new software systems, firmware, services, and cryptographic integrations must be built to support algorithm agility.
- Hardcoded cryptographic algorithms, fixed key sizes, or static protocol assumptions are prohibited.
- Systems must allow pluggable or configurable cryptographic backends via supported libraries or policy engines (e.g., OpenSSL 3.0, BouncyCastle, Java JCE).
- Proof of crypto-agility design must be submitted at system design review or product acceptance testing stages.

### 3.3 Approved Post-Quantum Algorithms

All cryptographic upgrades and new deployments must use NIST-approved PQC algorithms:

| Function | Approved Algorithm | FIPS Standard |
|---|---|---|
| Key Encapsulation (KEM) | CRYSTALS-Kyber | FIPS 203 |
| Digital Signature (primary) | CRYSTALS-Dilithium | FIPS 204 |
| Digital Signature (constrained) | FALCON | — |
| Digital Signature (diversity fallback) | SPHINCS+ | FIPS 205 |

- Hybrid cryptographic schemes (e.g., ECDH + Kyber, ECDSA + Dilithium) are **mandatory** during the transition period to maintain backward compatibility.
- PQC algorithms must be implemented using validated cryptographic libraries where available.

### 3.4 Procurement and Vendor Requirements

All third-party software, cloud services, and hardware products must:
- Disclose supported cryptographic algorithms at the time of acquisition or renewal
- Support or commit to supporting NIST PQC algorithms within 12 months
- Demonstrate crypto-agility or hybrid capability
- Include language in contracts requiring timely PQC support, algorithm substitution capability, and patching rights for cryptographic upgrades

### 3.5 Governance and Oversight

A dedicated **PQC Working Group**, chaired by the CISO or appointed delegate, is responsible for:
- Maintaining the cryptographic asset inventory
- Tracking industry changes and updates to PQC standards
- Approving exceptions and monitoring remediation timelines
- Reporting quarterly status to the Information Security Steering Committee

---

## 4. Roles and Responsibilities

### 4.1 Chief Information Security Officer (CISO)
- Owns overall accountability for the PQC transition strategy and policy enforcement
- Chairs or delegates leadership of the PQC Working Group
- Reviews exception requests and escalates unresolved violations to executive risk committees
- Ensures integration of PQC risk into enterprise security posture reporting

### 4.2 PQC Working Group
- Serves as the primary governance body for post-quantum readiness and compliance
- Maintains and updates the enterprise cryptographic inventory
- Reviews new system designs for crypto-agility and PQC readiness
- Approves or denies exceptions, documenting all deviations
- Coordinates pilot deployments, algorithm migration roadmaps, and hybrid crypto strategies
- Monitors external developments (NIST standardization, FIPS updates, PQC vulnerabilities)

### 4.3 Engineering and Development Teams
- Design and implement all new systems to be PQC-compliant and crypto-agile by default
- Ensure all deployed cryptographic libraries support NIST-approved PQC algorithms or hybrid modes
- Collaborate with the PQC Working Group during system design reviews and threat modeling
- Remove or refactor hardcoded cryptographic primitives where discovered

### 4.4 Infrastructure and Operations Teams
- Deploy and maintain cryptographic libraries, middleware, and protocol stacks supporting PQC
- Upgrade or retire systems based on tiered risk as defined by the PQC Transition Strategy
- Integrate PQC monitoring and key management into existing logging and configuration management systems

### 4.5 Procurement and Vendor Management
- Enforce vendor cryptographic disclosure and PQC support requirements in all contracts and renewals
- Evaluate supplier risk based on crypto-agility, algorithm flexibility, and FIPS roadmap alignment
- Escalate non-compliant vendors to the PQC Working Group for risk acceptance or vendor isolation decisions

### 4.6 Compliance, Audit, and Risk Management
- Audit systems and processes for adherence to this policy and PQC implementation requirements
- Monitor for unauthorized use of quantum-vulnerable algorithms
- Maintain records of exception approvals, remediation actions, and risk mitigation deadlines
- Report non-compliance incidents to the CISO and Risk Steering Committee

---

## 5. Algorithms Targeted for Deprecation

The following algorithms are quantum-vulnerable and must be fully deprecated:

| Algorithm | Category | Vulnerability |
|---|---|---|
| RSA (all key sizes) | Asymmetric encryption & signature | Shor's algorithm |
| ECDSA | Digital signature | Shor's algorithm |
| ECDH / X25519 / X448 | Key exchange | Shor's algorithm |
| DSA | Digital signature | Shor's algorithm |
| Diffie-Hellman (classic) | Key exchange | Shor's algorithm |
| AES-128 | Symmetric (strengthen only) | Grover's algorithm (effective 64-bit) |
| SHA-256 | Hash (strengthen only) | Grover's algorithm |

---

## 6. References

- [NIST FIPS 203 — ML-KEM (Kyber)](https://csrc.nist.gov/pubs/fips/203/final)
- [NIST FIPS 204 — ML-DSA (Dilithium)](https://csrc.nist.gov/pubs/fips/204/final)
- [NIST FIPS 205 — SLH-DSA (SPHINCS+)](https://csrc.nist.gov/pubs/fips/205/final)
- [NSA CNSA 2.0](https://media.defense.gov/2022/Sep/07/2003071834/-1/-1/0/CSA_CNSA_2.0_ALGORITHMS_.PDF)
- [ENISA Post-Quantum Cryptography](https://www.enisa.europa.eu/topics/cryptography)
- [Open Quantum Safe Project (liboqs)](https://openquantumsafe.org/)
