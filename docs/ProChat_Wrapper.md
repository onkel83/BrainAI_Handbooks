## Revisionshistorie / Revision History

| Version | Datum | Autor | Status | Beschreibung |
| --- | --- | --- | --- | --- |
| 1.0.0 | 22.02.2026 | S. Köhne | Stable | Erweiterung um Error-Handling & Multi-Language Deep-Dive |

<div style="page-break-after: always;"></div>

### 1 Produktübersicht

Die ProChat Core Engine ist ein hochsicheres Kommunikationsmodul, das für den Einsatz in Hochsicherheitsumgebungen entwickelt wurde. Sie stellt die Vertraulichkeit, Integrität und Authentizität von Datenströmen sicher und schützt gegen moderne Angriffsszenarien wie Replay-Attacken und Man-in-the-Middle-Angriffe.

Kernmerkmale

* **Kryptografische Isolation:** Jede Session wird durch einen eigenen Ratchet-State isoliert.

* **Hardware-nahe Performance:** Geschrieben in hochoptimiertem C99 für minimalen Overhead.

* **Zero-Knowledge Architektur:** Die Engine verarbeitet Daten, ohne den Inhalt zu kennen.

* **RAM-Vault Technologie:** Interner Schutz der Sitzungsschlüssel gegen Memory-Dumping.

### 2. Technische Spezifikationen

#### 2.1 Globale Konstanten

Diese Werte sind für alle Implementierungen verbindlich und dürfen nicht modifiziert werden:

* **PC_PKT_SIZE:** _1024 Bytes_ (Feste Übertragungsgröße zur Verschleierung von Metadaten).

* **PC_MAX_PAYLOAD:** _988_ Bytes (Maximal verfügbare Nutzlast pro Paket).

* **Key/Seed Size:** Exakt _32_ Bytes (_256_-Bit Entropie).

#### 2.2 Paket-Layout (Public Interface)

Ein ProChat-Paket ist ein opaker binärer Block von 1024 Bytes. Er besteht aus:

* **Routing-Tag:** _4_ Bytes (Klartext zur Identifizierung der Session).

* **Cipher-Block:** Verschlüsselte Metadaten und Nutzlast.

* **Auth-Tag (MAC):** _16_ Bytes (Integritätssicherung via ProHash HMAC).

<div style="page-break-after: always;"></div>

### 3. Globales Error-Management

Das SDK verwendet ein hybrides Fehlermodell. Während die native Engine numerische Fehlercodes zurückgibt, transformieren die Wrapper diese in sprachspezifische Paradigmen (Exceptions oder Status-Objekte).

| **_Code_** | **_Konstante_** | **_Beschreibung:_** | **_Kritikalität_** |
|:--- | ---: | ---: | ---:|
| **0** | PC_OK | Operation erfolgreich abgeschlossen.| _Keine_ |
| **-1** | PC_ERR_PARAM | Ungültige Pufferlängen oder Null-Referenzen.| _Mittel (Logikfehler)_ |
| **-2** | PC_ERR_AUTH | MAC-Vergleich fehlgeschlagen. Paket manipuliert.| _Hoch (Angriff!)_ |
| **-3** | PC_ERR_REPLAY | Paket-Counter ungültig oder bereits verarbeitet.| _Mittel (Netzwerkfehler)_ |
| **-4** | PC_ERR_DESYNC | Ratchet-Synchronisation verloren.| _Fatal (Reset nötig)_ |

<div style="page-break-after: always;"></div>

### 4. C# (.NET) Integration & Exception Handling

Der _C#-Wrapper_ nutzt ein objektorientiertes Modell mit einer zentralen Client-Klasse. Er ist für .NET 6+, .NET Core und .NET Framework optimiert.

#### 4.1 Implementierungsbeispiel

---

| © 2026 Sascha Köhne | ✉️ **Contact:** [koehne83@googlemail.com](mailto:koehne83@googlemail.com) | 🛡️ **ProTU-Verified** |
| --- | --- | --- |
| CEO / System Architect | BrainAI UG (haftungsbeschränkt) | We don't need **BRUTEFORCE**, we knows **PHYSICS** |
