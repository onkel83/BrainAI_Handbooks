## Revision History

| Version | Date | Author | Status | Description |
| --- | --- | --- | --- | --- |
| 1.0.0 | 2026-01-01 | S. Köhne | Stable | Initial Commercial Suite CLI |
| 1.1.0 | 2026-02-26 | S. Köhne | Stable | Hybrid Ring-0, Audit-Logging & Secure Wipe (PRO Edition) |

<div style="page-break-after: always;"></div>

### 1. Product Overview

The **ProEDC CLI Suite** is the primary administration tool for the commercial ProED series. Unlike the standard version, ProEDC integrates a complete license management system, advanced cryptographic utility functions (KeyGen, Hashing), and a tamper-proof audit trail.

**Core Features:**

* **Hybrid Acceleration (PRO Only):** Automatic detection and utilization of the `ProKM` kernel driver for lossless Ring-0 performance.
* **Audit-Proof Logging:** Optional logging of operations, including cryptographic input and output hashes for seamless traceability.
* **Secure Data Shredding (PRO Only):** Multiple overwriting of source files after successful processing to meet the highest data protection standards.
* **Auto-Keygen:** Automatic creation of secure 256-bit keys if parameters are missing during the encryption process.
* **Integrated Suite:** Combines file encryption, key generation, and ProHash verification in a single binary.

### 2. Technical Specifications

#### 2.1 Command-Line Parameters (CLI)

The suite follows the syntax: `proedc [MODE] [OPTIONS]`.

**Modes:**

* `-e, --encrypt`: Encryption mode.
* `-d, --decrypt`: Decryption mode.
* `-g, --keygen`: Generation of cryptographic keys.
* `--hash`: Creation of ProHash values for files or strings.

**Options:**

* `-i, --input <path>`: Path to the input file.
* `-o, --output <path>`: Path to the output file.
* `-k, --key <path>`: Path to the key file.
* `-s, --string <text>`: Text for direct hashing (without a file).
* `-b, --bits <n>`: Key length in bits (Default: 256).
* `-L, --log`: **(PRO)** Enables detailed audit logging.
* `-D, --delete`: **(PRO)** Securely wipe the source file after success.

<div style="page-break-after: always;"></div>

### 3. Global Error Management

ProEDC uses an expanded status model that accounts for license and hardware states.

| **Exit Code** | **Constant** | **Description** | **Criticality** |
| --- | --- | --- | --- |
| **0** | PEDC_SUCCESS | Operation completed successfully. | None |
| **1** | PEDC_ERR_PARAM | Invalid parameters or missing arguments. | Medium |
| **2** | PEDC_ERR_AUTH | MAC check failed (file manipulated). | High |
| **3** | PEDC_ERR_FILE_IO | Error reading/writing files. | Medium |
| **-7** | PEDC_ERR_LIMIT | License limit reached (e.g., TRIAL expiration). | Fatal |

<div style="page-break-after: always;"></div>

### 4. Implementation Examples

#### 4.1 Encryption with Auto-Keygen & Audit Log (PRO)

In this example, a file is encrypted. Since no key is specified, ProEDC automatically creates one and logs all hashes.

```bash
# Execution in the PRO Edition
./proedc -e -i confidential.dat -o confidential.enc -L

# Result in proedc_audit.log:
# [2026-02-26 14:30:00] OP: AUTO-KEY | Status: OK | IN: N/A | OUT: auto_generated_proedc.key
# [2026-02-26 14:30:00] OP: ENCRYPT  | Status: OK | IN: confidential.dat (Hash: A1B2...) | OUT: confidential.enc (Hash: C3D4...)

```

#### 4.2 Decryption followed by Shredding (PRO)

This command decrypts a file using the kernel bypass and irretrievably deletes the encrypted source file after success.

```bash
./proedc -d -i backup.enc -o backup.zip -k master.key -D
# [+] PRO Edition: Hardware Kernel Driver (ProKM) detected.
# [+] PRO Feature: Source file securely wiped (-D).

```

#### 4.3 String Hashing for Configurations

Direct generation of a ProHash value for a text string:

```bash
./proedc --hash -s "Master-Admin-Password-2026"
# String Hash: 7f83b165...

```

<div style="page-break-after: always;"></div>

### 5. Best Practices & Security Guidelines

* **Audit Compliance:** In automated environments, always use the `-L` flag to be able to prove the integrity of the data chain during security audits.
* **Data Hygiene:** In the PRO Edition, use the `-D` flag for temporary files to ensure that no fragments of sensitive data remain on physical storage media.
* **License Monitoring:** Regularly check exit code `-7` to react in time before TRIAL licenses or volume limits expire in production systems.
* **Kernel Privileges:** To use Ring-0 acceleration, the executable must have sufficient permissions to communicate with the `ProKM` device.

---

| © 2026 Sascha Köhne | ✉️ **Contact:** [koehne83@googlemail.com](mailto:koehne83@googlemail.com) | 🛡️ **ProTU-Verified** |
| --- | --- | --- |
| CEO / System Architect | BrainAI Inhaber : Sascha Alexander Köhne | We don't need **BRUTEFORCE**, we know **PHYSICS** |
