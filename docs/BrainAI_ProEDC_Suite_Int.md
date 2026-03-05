# 🛡️ ProSuite Industrial

**Enterprise Cryptography & Zero-Trust Architecture** *by BrainAI Inhaber : Sascha Alexander Köhne*

Welcome to the official documentation and source code repository of **ProSuite Industrial**. The ProSuite is a modular, deterministic, and highly secure cryptographic ecosystem designed specifically for OT (Operational Technology) environments, Cloud infrastructures, and Zero-Trust networks.

We don't rely on *Bruteforce*, we rely on *Physics*.

---

## 📚 Documentation & Manuals

The complete technical documentation is available in PDF format (rendered dynamically via CI/CD from the Markdown sources). Please select the appropriate module below:

### 1. ProEDC (Industrial Encryption Suite)

The flagship of the suite. A highly optimized, symmetric encryption system with AEAD integrity protection (Armored Envelope), engineered for massive data streams and zero-footprint RAM utilization.

* 📄 **System Integration Guide** (`ProEDC_Handbook_Int.pdf`)
* 📄 **CLI Reference** (`ProEDC_CLI_Handbook_Int.pdf`)

### 2. ProED (Core Engine)

The native low-level engine powering ProEDC. Offers kernel-space and user-space implementations for maximum performance.

* 📄 **Engine Architecture Manual** (`ProED_Handbook_Int.pdf`)
* 📄 **Command Line Reference** (`ProED_CLI_Handbook_Int.pdf`)

### 3. ProHash (Deterministic Data Integrity)

A physically deterministic 256-bit sponge hashing algorithm ("The Shredder"). Implemented in pure software as well as a dedicated FPGA hardware core.

* 📄 **Software API & Wrapper Guide** (`ProHash_Handbook_Int.pdf`)
* 📄 **FPGA Hardware Manual** (`ProHash_Hardware_Int.pdf`)
* 📄 **Software CLI Reference** (`ProHash_CLI_Handbook_Int.pdf`)

### 4. ProKey (Hardware Entropy & TRNG)

The Hardware Security Module (HSM) for generating absolutely random physical entropy for secure keys (AES, RSA, ECC).

* 📄 **Hardware Entropy SDK Guide** (`ProKey_Handbook_Int.pdf`)
* 📄 **Hardware CLI Reference** (`ProKey_CLI_Handbook_int.pdf`)

### 5. ProChat (Zero-Trust Communication)

The application layer. A decentralized, P2P-based messenger and data vault that unifies the entire ProSuite (ProEDC, ProHash, ProKey) in a WebAssembly (WASM) and Node.js environment.

* 📄 **System & Architecture Manual** (`ProChat_Handbook_Int.pdf`)
* 📄 **WASM & Server Integration** (`wasm_serverconfig.pdf`)

### ⚖️ Legal & Compliance

* 📄 **Master License Agreement (EULA)** (`Lizenz.pdf`)

---

## 🚀 Quick Start (Wrapper Suite)

All our modules are written natively in C and provide memory-safe, zero-copy wrappers for modern high-level languages:

* **C# / .NET:** Direct P/Invoke integration via `Marshal`.
* **Java:** Native Access via `JNA` for Spring Boot and Enterprise Backends.
* **JavaScript / Node.js:** High-Speed I/O via `ffi-napi` and V8 Buffers.
* **Python:** Scripting and automation via `ctypes`.
* **VB.NET:** Optimized for industrial legacy PLCs and SCADA systems.

For further information on integrating the modules into your software stack, please refer to the respective Integration Guides listed above.

---

| © 2026 Sascha Köhne | ✉️ **Contact:** koehne83@googlemail.com | 🛡️ **ProTU-Verified** |
| --- | --- | --- |
| CEO / System Architect | BrainAI Inhaber : Sascha Alexander Köhne | We don't need **BRUTEFORCE**, we know **PHYSICS** |
