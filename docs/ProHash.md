## Revisionshistorie / Revision History

| Version | Datum | Autor | Status | Beschreibung |
| :--- | :--- | :--- | :--- | :--- |
| 1.0.0 | 01.12.2025 | S. Köhne | Release | Initialer Entwurf der Software API |
| 1.0.1 | 20.02.2026 | S. Köhne | Stable | Anpassung für ProEDC Industrial Pipeline |

<div style="page-break-after: always;"></div>

## 1. Produktübersicht

Die **ProHash-256 Software Bibliothek** bietet eine deterministische und hochperformante Schnittstelle zur Berechnung von kryptografischen 256-Bit-Hashes. Sie ist für den Einsatz auf Windows, Linux und Embedded-Plattformen konzipiert und vollständig C99-kompatibel.

Die interne Architektur nutzt ein sicheres **Sponge-Konstrukt**, das darauf optimiert ist, Datenströme beliebiger Größe effizient zu verarbeiten.

### Leistungsmerkmale
* **Plattformunabhängige Deterministik:** Ein identischer Input generiert über alle unterstützten Plattformen (x86, x64, ARM) hinweg garantiert den exakt gleichen Hash-Wert.
* **Streaming-Support:** Sequentielle Verarbeitung von Daten ohne die Notwendigkeit, den gesamten Datensatz im Speicher zu halten.
* **Thread-Sicherheit:** Vollständig reentrant; parallele Hash-Berechnungen sind durch unabhängige Kontext-Strukturen problemlos möglich.

## 2. Datenstrukturen

### `ProHash_Ctx`
Dieser Kontext hält den gesamten Lebenszyklus einer Hash-Berechnung. Er ist als „Black Box“ zu betrachten und sollte vom Nutzer niemals manuell manipuliert werden.

```c
typedef struct {
    uint32_t belt[16];   // Interner State-Vektor
    uint64_t count;      // Akkumulierte Byte-Anzahl
    uint8_t  buffer[64]; // Block-Puffer
    uint32_t buf_idx;    // Aktuelle Puffer-Position
} ProHash_Ctx;

```

<div style="page-break-after: always;"></div>

## 3. API-Referenz (C99)

Die Hash-Generierung erfolgt strikt in drei Phasen: Initialisierung, Update(s) und Finalisierung.

### 3.1 Initialisierung

Bereitet den Kontext für eine neue Berechnung vor.

```c
/**
 * @param ctx Zeiger auf den zu initialisierenden Kontext.
 */
PROHASH_API void prohash_init(ProHash_Ctx* ctx);

```

### 3.2 Daten-Injektion (Update)

Füttert die Sponge-Funktion mit Daten. Kann in einer Schleife beliebig oft für Chunks aufgerufen werden.

```c
/**
 * @param ctx Zeiger auf den initialisierten Kontext.
 * @param data Eingabe-Datenstrom.
 * @param len Länge der Eingabe in Bytes.
 */
PROHASH_API void prohash_update(ProHash_Ctx* ctx, const uint8_t* data, size_t len);

```

### 3.3 Finalisierung

Generiert den finalen Digest und leert den internen Block-Puffer.

```c
/**
 * @param ctx Zeiger auf den Kontext.
 * @param digest Puffer (exakt 32 Bytes), in den das Ergebnis geschrieben wird.
 */
PROHASH_API void prohash_final(ProHash_Ctx* ctx, uint8_t* digest);

```

<div style="page-break-after: always;"></div>

## 4. Integrationsbeispiel

Der folgende Code demonstriert einen speicherschonenden Workflow zur Berechnung des Hash-Wertes einer lokalen Datei in 4-Kilobyte-Blöcken.

```c
#include "ProHash.h"
#include <stdio.h>

void calculate_file_integrity(const char* filepath) {
    FILE* file = fopen(filepath, "rb");
    if(!file) return;

    ProHash_Ctx ctx;
    uint8_t chunk[4096];
    uint8_t final_hash[32];
    size_t bytes_read;

    // 1. Kontext aufsetzen
    prohash_init(&ctx);

    // 2. Datei sequentiell in den Sponge streamen
    while ((bytes_read = fread(chunk, 1, sizeof(chunk), file)) > 0) {
        prohash_update(&ctx, chunk, bytes_read);
    }

    // 3. Hash berechnen
    prohash_final(&ctx, final_hash);

    // 4. State-Sanitization (Best Practice)
    memset(&ctx, 0, sizeof(ProHash_Ctx));

    // Ergebnis protokollieren
    printf("ProHash-256: ");
    for(int i = 0; i < 32; i++) {
        printf("%02x", final_hash[i]);
    }
    printf("\n");

    fclose(file);
}

```

---

| © 2026 Sascha Köhne | ✉️ **Contact:** [koehne83@googlemail.com](mailto:koehne83@googlemail.com) | 🛡️ **ProHash-Verified** |
| --- | --- | --- |
| CEO / System Architect | BrainAI UG (haftungsbeschränkt) | We don't need **BRUTEFORCE**, we knows **PHYSICS** |
