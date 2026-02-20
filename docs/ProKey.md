## Revisionshistorie / Revision History

| Version | Datum | Autor | Status | Beschreibung |
| :--- | :--- | :--- | :--- | :--- |
| 1.0.0 | 01.12.2025 | S. Köhne | Release | Initialer SDK Entwurf |
| 1.1.0 | 20.02.2026 | S. Köhne | Update | Implementierung RTR (Rolling the Random) & ProTU-Validierung |

<div style="page-break-after: always;"></div>

## 1. Einführung

**ProKey** ist der Industriestandard für die Erzeugung kryptografisch sicherer Zufallszahlen (CSPRNG) auf Embedded-Systemen und Hochsicherheits-Plattformen.

Im Gegensatz zu herkömmlichen Pseudo-Zufallsgeneratoren (PRNGs), die deterministische Algorithmen verwenden, ist ProKey ein **True Random Number Generator (TRNG) Hybrid**. Er fusioniert physikalische Hardware-Effekte mit hochpräzisen Zeitmessungen und veredelt das Ergebnis mit der proprietären **RTR-Technologie** (Rolling the Random).

### Leistungsmerkmale
* **Höchste Entropie:** Fusion aus Hardware-RNG, CPU-Jitter und Timing-Rauschen.
* **RTR Whitening:** Entfernt statistische Auffälligkeiten und Bias aus der Hardware-Quelle.
* **Variable Schlüssellängen:** Native Unterstützung für 128, 256, 512 und 1024 Bit Schlüssel.
* **Zero-Allocation:** Arbeitet vollständig auf dem Stack (kein `malloc`/`free`).

## 2. API-Referenz (C99)

### ProKey_Generate
Hauptfunktion zur Erzeugung von Entropie-Daten.

```c
/**
 * @brief Generiert kryptografisch sichere Zufallsbits
 * @param buffer Zielpuffer für die Zufallsdaten
 * @param bits Gewünschte Länge (128, 256, 512, 1024)
 */
void ProKey_Generate(uint8_t* buffer, ProKey_Bits bits);

```

## 3. Implementierungsbeispiele

### Szenario A: Session-Key Generierung (256 Bit)

```c
#include "ProKey.h"

void create_secure_session() {
    uint8_t session_key[32]; // 256 Bit
    ProKey_Generate(session_key, KEY_256_BIT);
    // Schlüssel für ProED Verschlüsselung nutzen...
}

```

### Szenario B: Langzeit-Seed (1024 Bit)

```c
#include "ProKey.h"

void generate_master_seed() {
    uint8_t master_seed[128];
    ProKey_Generate(master_seed, KEY_1024_BIT);
    // Übergabe an asymmetrische Key-Gen (RSA/ECC)...
}

```

<div style="page-break-after: always;"></div>

## 4. Sicherheitshinweise

1. **Speicherbereinigung:** ProKey liefert "Geheimnisse". Löschen Sie den Puffer nach der Verwendung (`memset` oder `SecureZeroMemory`), um RAM-Dumps zu verhindern.
2. **Puffer-Größe:** ProKey prüft nicht die Länge des übergebenen Arrays. Stellen Sie sicher, dass der Puffer groß genug für die angeforderte Bit-Länge ist.
3. **Boot-Time:** Auf Systemen ohne dedizierten Hardware-RNG benötigt ProKey eine kurze Sammelphase für Jitter-Entropie beim Systemstart.

---

| © 2026 Sascha Köhne | ✉️ **Contact for KYC & Early Access:** [koehne83@googlemail.com](mailto:koehne83@googlemail.com) | 🛡️ **ProHash-Verified** |
| --- | --- | --- |
| System Architect | BrainAI UG (haftungsbeschränkt) | Deterministische Integrität |
