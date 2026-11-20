# hash-calculator-and-john-the-riper-week-3
# Password Auditing & Cryptographic Hashing: John the Ripper and Hash Calculators

> **Disclaimer:** The tools and concepts documented in this repository are strictly intended for educational purposes, authorized penetration testing, system administration, and digital forensics. Unauthorized password cracking or network auditing is illegal.

## Overview

This repository explores two fundamental utilities in cybersecurity and cryptography:
1. **John the Ripper (JtR):** An open-source, offline password security auditing and recovery tool.
2. **Hash Calculators:** Cryptographic utilities used to generate fixed-length message digests (hashes) from arbitrary input data (strings or files) to verify integrity and secure credentials.

---

## 1. John the Ripper (JtR)

**John the Ripper** is a fast, flexible password cracker maintained by Openwall. Rather than attacking live login portals, it performs **offline attacks** against extracted password hashes (such as `/etc/shadow` in Linux or `SAM` dumps in Windows) to identify weak, predictable, or reused credentials.

### Key Features & Attack Modes
* **Single Crack Mode:** Generates candidate passwords using information already associated with the target account (usernames, full names, home directories) combined with common mangling rules.
* **Wordlist (Dictionary) Mode:** Tests hashes against a list of known passwords or dictionary words, optionally applying rule-based mutations (e.g., substituting `a` with `@` or appending numbers).
* **Incremental Mode:** A smart brute-force approach that uses character frequency statistics to test the most probable character combinations first.
* **Mask Mode:** Targets passwords with a known or suspected structure (e.g., an uppercase letter followed by 5 lowercase letters and 2 digits).
* **`*2john` Utilities:** Built-in helper scripts (`zip2john`, `ssh2john`, `pdf2john`, `keepass2john`) that extract crackable hashes from encrypted archives, documents, and private keys.

### Basic Usage
```bash
# Auto-detect hash format and crack using default modes
john hashes.txt

# Crack using a specific wordlist (e.g., rockyou.txt)
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt

# Specify a hash format explicitly
john --format=raw-md5 hashes.txt

# View previously cracked passwords stored in the pot file
john --show hashes.txt
Advantages
​Broad Format Support: Natively detects and supports hundreds of hash types across Linux, Windows, macOS, web applications, and encrypted file formats.
​Intelligent Candidate Generation: Single crack and statistical incremental modes often recover passwords faster than naive brute-force methods.
​Stealth & Speed (Offline Execution): Generates zero network traffic or failed-login logs on target systems and bypasses account lockout policies.
​Cross-Platform & Open-Source: Runs on Linux, Windows, and macOS with extensive community rule sets (especially via the Jumbo build).
​Disadvantages
​Steep Learning Curve: Operates primarily via the command line with complex syntax for custom rules and masks; lacks a native GUI in standard builds.
​Hardware-Dependent Performance: While it supports OpenCL/GPU acceleration, its raw GPU throughput on fast hashes generally lags behind dedicated GPU crackers like Hashcat.
​Ineffective Against Modern Slow Hashes + Strong Passwords: Properly salted passwords hashed with memory-hard algorithms (e.g., Argon2id, bcrypt, scrypt) remain computationally infeasible to crack if the password has high entropy.
​Passive Remediation Only: Identifies weak passwords after the fact rather than actively enforcing password policies or preventing breaches.
​2. Hash Calculator
​A Hash Calculator (available as CLI tools like sha256sum, GUI tools like HashCalc/QuickHash, or programming libraries) computes a deterministic, one-way cryptographic digest from an input string or file.
​Common Supported Algorithms
​Legacy / Broken (Non-Cryptographic Security): MD5, SHA-1
​Modern Cryptographic Standards: SHA-256, SHA-512, SHA-3, BLAKE2
​Checksums (Error Detection): CRC32
​Basic CLI Usage
# Calculate the SHA-256 hash of a file
sha256sum file.iso

# Calculate the MD5 hash of a text string
echo -n "MySecretPassword" | md5sum

Advantages
​Data Integrity Verification: Even a single-bit change in a file produces a completely different hash (the avalanche effect), making it effortless to detect file corruption or tampering during downloads and transfers.
​Fast Computation & Lookup: Generates fixed-length digests rapidly, enabling constant-time O(1) lookups in hash tables, database indexing, and file deduplication.
​One-Way Security: Cryptographic hashes are designed to be preimage-resistant, protecting sensitive data like stored passwords from direct plaintext exposure.
​Standardized & Deterministic: The exact same input always produces the exact same output across any platform or calculator implementation.
​Disadvantages
​Irreversibility: Because hashing is a one-way function, lost data cannot be decrypted or recovered from its hash value.
​Vulnerability to Lookup & Rainbow Tables: Without salting, deterministic hashes allow attackers to precompute digests for common inputs and instantly match them.
​Collision Risks in Older Algorithms: Mapping infinite possible inputs to a fixed-length output guarantees mathematical collisions. Outdated algorithms like MD5 and SHA-1 are vulnerable to practical collision attacks.
​Not Suitable Alone for Password Storage: Standard fast hashes (like raw SHA-256) compute too quickly, making them vulnerable to high-speed brute-force cracking unless combined with salting and key-stretching algorithms (such as Argon2 or bcrypt).
​Comparison Summary
[22:41, 11/20/2026] Chiko⚽🥷🏽: Feature / AspectJohn the RipperHash Calculator
Primary PurposePassword auditing & offline hash crackingGenerating digests for integrity & verification
DirectionReverse-lookup (guess \rightarrow hash \rightarrow match)Forward-only (input \rightarrow fixed-size hash)
Typical Use CasePenetration testing, forensic recovery, security auditsVerifying ISO checksums, digital forensics, deduplication
Resource UsageHigh CPU/GPU utilization over extended periodsLightweight, near-instant execution
[22:41, 11/20/2026] Chiko⚽🥷🏽: Defensive Best Practices
​Enforce High-Entropy Passphrases: Use long, unpredictable passwords (14+ characters) to resist dictionary, rule, and incremental attacks.
​Use Memory-Hard Hashing: Store credentials using Argon2id, bcrypt, or scrypt with unique per-user salts rather than raw MD5 or SHA hashes.
​Implement Multi-Factor Authentication (MFA): Ensure that even a compromised or cracked password does not grant unauthorized account access.
