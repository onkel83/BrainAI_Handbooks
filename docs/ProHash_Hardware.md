## Revisionshistorie / Revision History

| Version | Datum | Autor | Status | Beschreibung |
| :--- | :--- | :--- | :--- | :--- |
| 1.0.0 | 20.02.2026 | S. Köhne | Release | Initial Public Data Sheet für SoC-Integratoren |

<div style="page-break-after: always;"></div>

## 1. Übersicht: ProHash IP Core
Der **ProHash Hardware IP Core** ist eine hochgradig optimierte RTL-Implementierung (Register-Transfer-Level) zur Beschleunigung von deterministischen 256-Bit Integritätsprüfungen. 

Das Design ist primär für den Einsatz in industriellen SoC-Architekturen (System-on-Chip) konzipiert und lagert rechenintensive Sponge-Konstrukte von der Haupt-CPU in dedizierte Hardware-Gatter aus. Dies ermöglicht maximalen Durchsatz bei extrem niedriger Latenz und minimalem Energieverbrauch.

## 2. Zielarchitekturen & Kompatibilität
Der Core ist in plattformunabhängigem Verilog (IEEE 1364-2001) geschrieben und synthetisierbar für:
* **FPGAs:** Xilinx/AMD (Zynq-7000, UltraScale+), Intel/Altera (Cyclone V, Stratix).
* **ASIC:** Generisches Gatter-Design für Standard-Zellen-Bibliotheken.
* **Microcontroller-Peripherie:** Direkte Anbindung via UART (`uart_tx.v`, `uart_rx.v`) oder Integration als Memory-Mapped Device via SoC-Wrapper (`prohash_soc_wrapper.v`).

## 3. Leistungsspezifikationen (Estimates)

Die finalen Performance-Werte skalieren linear mit dem verfügbaren Hardware-Takt (Clock Frequency) der Zielplattform. Die Architektur ist darauf ausgelegt, Daten in kontinuierlichen Streams zu verarbeiten (Zero-Wait-State nach Initialisierung).

| Metrik | Spezifikation / Schätzwert |
| :--- | :--- |
| **Datendurchsatz (Takt)** | 64 Bit pro n Taktzyklen (Pipeline-abhängig) |
| **Max. Frequenz (FPGA)** | > 150 MHz (abhängig von Speedgrade & Routing) |
| **Max. Frequenz (ASIC)** | > 500 MHz (abhängig vom Node, z.B. 28nm) |
| **Latenz (Initialisierung)** | < 10 Taktzyklen |
| **Ressourcenbedarf** | Low-Footprint (Genaue LUT/FF-Werte abhängig von der Synthese-Konfiguration) |

<div style="page-break-after: always;"></div>

## 4. Hardware Security Features

Physische Hardware ist naturgemäß anderen Angriffsvektoren ausgesetzt als Software. Der ProHash Core integriert daher passive und aktive Schutzmechanismen direkt auf der RTL-Ebene, um die Halbwertszeit der kryptografischen Sicherheit gegenüber aktuellen Hardware-Angriffen maximal zu verlängern:

* **Constant-Time Execution:** Unabhängig von den Eingabedaten benötigt der Hash-Vorgang stets exakt dieselbe Anzahl an Taktzyklen. Dies schützt vor Timing-Analysen.
* **Side-Channel-Mitigation:** Interne State-Register sind so entworfen, dass die Korrelation zwischen Stromverbrauch (DPA/SPA) und den verarbeiteten Datenstrukturen mathematisch erschwert wird.
* **State Wiping:** Dedizierte Hardware-Signale ermöglichen das Löschen des gesamten internen Core-Zustands innerhalb eines einzigen Taktzyklus (Hardware-Reset).

## 5. Integrations-Schnittstelle (Black-Box)

Der Integrator kommuniziert ausschließlich über standardisierte Signale mit dem Core. Die interne Datenfaltung bleibt vollständig abstrahiert.

**Top-Level Ports (Auszug):**
* `clk`: Systemtakt
* `rst_n`: Asynchroner Reset (Active-Low)
* `data_in [63:0]`: 64-Bit Eingangs-Bus für den Datenstrom
* `data_valid`: Steuersignal für gültige Eingangsdaten
* `hash_out [255:0]`: 256-Bit Ausgangs-Bus für das Ergebnis
* `hash_ready`: Flag, sobald die Berechnung abgeschlossen ist

---
*(C) 2026 BrainAI Engineering Division. Hardware Cryptography Core.*
