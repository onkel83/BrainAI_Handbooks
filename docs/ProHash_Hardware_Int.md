## Revision History

| Version | Date | Author | Status | Description |
| --- | --- | --- | --- | --- |
| 1.0.0 | 2025-01-01 | S. Köhne | Stable | Initial Hardware & CLI Documentation |
| 2.0.0 | 2026-02-27 | S. Köhne | Stable | Public Release incl. Internal Deep Dive & Register Maps |

<div style="page-break-after: always;"></div>

### 1 Product Overview & Philosophy

The **ProHash-256 Hardware Security Module (HSM)** physically offloads the computation of cryptographically secure checksums to a Field Programmable Gate Array (FPGA) chip.

**Sovereignty & Zero-Trust:**
The computation takes place completely isolated from the operating system. Malware on the host PC (Windows/Linux) can disrupt the process through Denial-of-Service, but can never read the internal cryptographic state of the chip or manipulate the algorithm.

### 2 Hardware Architecture (The Deep Dive)

The FPGA implementation is structured like a factory, divided into three isolated modules:

#### 2.1 The Components

1. **`prohash_top.v` (Receiving & Shipping):** The top-level module. It manages the external pins, receives UART/USB packets from the PC, and sends back the finished hash values.
2. **`prohash_soc_wrapper.v` (Storage & Registers):** The bridge. It decodes incoming commands and sorts the 64-byte data blocks into the corresponding input registers.
3. **`prohash_core.v` (The "Grinder"):** The mathematical ALU. This is where the sponge construct (`S_GRIND`) is located, performing the physical bit-mixing.

#### 2.2 Customizations (Safe Zone)

* **Baud Rate:** Configured to `115200` baud by default. With high-quality cables, this can be increased to `921600` in `prohash_top.v` and `uart_rx.v`. Both the hardware and the C driver must always use the same value.
* **LED Status:** The pin assignment (`assign leds[...]`) can be safely adapted to the specific FPGA board used (e.g., as a `tx_busy` indicator).

#### 2.3 Critical Trade Secrets (Danger Zone)

Modifications in the following areas will destroy mathematical integrity:

* **Constants:** Initial vectors like `C_MUL1 = 64'hff51afd7ed558ccd` must never be modified. Changing them results in incompatibility with the software suite.
* **The `S_GRIND` Loop:** Modifications to the logic gates (e.g., replacing XOR `^` with ADD `+`) will irreversibly corrupt the sponge mechanism.

<div style="page-break-after: always;"></div>

### 3 Physical Layer & Memory Map

Communication between the host PC and the FPGA utilizes a binary Memory-Mapped I/O protocol over an 8N1 UART connection (Little Endian).

#### 3.1 The UART Protocol

* **Write (CMD `0x01`):** `[0xA5] [0x01] [ADDR] [LSB] [..] [..] [MSB]`. Writes 4 bytes into a 32-bit register.
* **Read (CMD `0x02`):** `[0xA5] [0x02] [ADDR]`. The FPGA responds immediately with 4 bytes (LSB first).

#### 3.2 The Register Map

The FPGA provides the following memory addresses for access:

| Address | Name | R/W | Description |
| --- | --- | --- | --- |
| `0x00` | **PH_REG_CTRL** | W | Bit 0: INIT (Reset Core). Bit 1: ABSORB (Start Grind). |
| `0x01` | **PH_REG_STATUS** | R | Bit 0: READY. Bit 1: BUSY (Computing). |
| `0x10` - `0x1F` | **INPUT BUFFER** | W | 16 words (64 bytes) for the current data block. |
| `0x20` - `0x27` | **OUTPUT HASH** | R | 8 words (32 bytes) for the final 256-bit result. |

<div style="page-break-after: always;"></div>

### 4 CLI User Guide (prohash_cli)

The Command-Line Interface orchestrates the low-level register commands into a usable end-user tool for Windows and Linux.

#### 4.1 System Requirements & Identification

* **Windows:** The FPGA appears as a "USB Serial Port" or "Silicon Labs CP210x" in the Device Manager (e.g., `COM3`).
* **Linux:** Can be identified in the terminal via `dmesg | grep tty` (e.g., `/dev/ttyUSB0`). The Linux user must be in the `dialout` group for access (`sudo usermod -a -G dialout $USER`).

#### 4.2 Standard Usage

The syntax is: `prohash_cli [PORT] [FILE_PATH]`

**Example for Linux:**

```bash
./prohash_cli /dev/ttyUSB0 /tmp/system_image.iso
# [*] Opening Hardware Link on /dev/ttyUSB0... OK.
# [*] Initializing FPGA Core... READY.
# [*] Grinding file...
# --------------------------------------------------
# ProHash-256: 8f4b2e1a9c... 

```

<div style="page-break-after: always;"></div>

### 5 Troubleshooting & Hardware Diagnostics

If communication fails, the CLI provides direct indicators:

* **"Could not open serial port":** The specified COM port is incorrect, or it is exclusively blocked by other software (e.g., Putty, Arduino IDE).
* **Hangs at "Initializing FPGA Core...":** The PC is sending the SYNC byte (`0xA5`) but receives no response from the FPGA.
* *Solution A:* Reset the FPGA using the hardware push button.
* *Solution B:* For external UART adapters, ensure **TX and RX are crossed** (TX to RX, RX to TX).


* **Inconsistent Hash Values:** If the same file generates varying hashes, the USB connection is faulty. ProHash is physically deterministic. Use a shorter, shielded cable.

---

| © 2026 Sascha Köhne | ✉️ **Contact:** [koehne83@googlemail.com](mailto:koehne83@googlemail.com) | 🛡️ **ProTU-Verified** |
| --- | --- | --- |
| CEO / System Architect | BrainAI UG (haftungsbeschränkt) | We don't need **BRUTEFORCE**, we know **PHYSICS** |
