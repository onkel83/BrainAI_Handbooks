## Revisionshistorie

| Version | Datum | Autor | Status | Beschreibung |
| --- | --- | --- | --- | --- |
| 1.0.0 | 01.03.2026 | S. Köhne | Stable | Initial Release: ProTU Enterprise Compliance Guide |

<div style="page-break-after: always;"></div>

### 1. Produktübersicht: Process Trust Unit (ProTU)

Die **ProTU (Process Trust Unit)** ist das offizielle Zertifizierungs- und Audit-Werkzeug der BrainAI UG für die ProSuite Industrial.

In Hochsicherheitsumgebungen reicht es nicht aus, darauf zu vertrauen, dass eine Kryptografie-Software "funktioniert". Hardware-Abweichungen, fehlerhafte Betriebssystem-Updates oder stille RAM-Korruption können die Integrität von Schlüsseln gefährden. ProTU löst dieses Problem: Es führt auf dem Zielsystem des Kunden tiefe kryptografische Penetrations-, Entropie- und Speichertests aus und beweist die korrekte Funktion der gesamten Suite.

**Einsatzszenarien für den Kunden:**

* **Inbetriebnahme:** Einmalige Ausführung nach der Installation der ProSuite auf neuen Servern.
* **Continuous Compliance:** Automatisierte Ausführung (z. B. via Cronjob) zur fortlaufenden Überwachung für ISO 27001 / TISAX Audits.
* **Forensik:** Überprüfung auf "Stuck-At"-Fehler oder kompromittierte Entropie-Quellen.

### 2. Bedienung und Ausführung (CLI)

Die ProTU wird als alleinstehende, portierbare Executable ausgeliefert (`protu_industrial.exe` für Windows, `protu_industrial` für Linux). Sie erfordert keine Installation und läuft autark im User-Space.

#### 2.1 Standard-Audit (Gesamtsystem)

Um das gesamte System zu auditieren, rufen Sie die Anwendung ohne Parameter oder mit dem Parameter `ALL` auf.

**Windows:**

```cmd
> protu_industrial.exe ALL

```

**Linux:**

```bash
$ ./protu_industrial ALL

```

*Hinweis: Dieser Befehl testet alle lizenzierten User-Space Module (ProKey, ProHash, ProED, ProEDC, ProChat) sowie das Framework selbst.*

#### 2.2 Gezielte Modul-Audits

Für isolierte Prüfungen oder gezieltes CI/CD-Testing kann ein spezifisches Modul als Parameter übergeben werden:

| Befehl | Ziel des Audits |
| --- | --- |
| `protu_industrial ProKey` | **Hardware Entropie:** Prüft den Zufallsgenerator auf Monobit-Bias, Repetitionen und physikalische Starvation. |
| `protu_industrial ProHash` | **Integrität:** Prüft die Sponge-Engine, Avalanche-Effekt und Kollisionsresistenz. |
| `protu_industrial ProEDC` | **Verschlüsselung:** Prüft die AEAD-Kapselung, Memory-Wiping und Boundary-Checks. |
| `protu_industrial WASM` | **Web-Tresor:** Verifiziert die Speichersicherheit des WebAssembly-Heaps für Node.js Backends. |
| `protu_industrial ProKM` | **Kernel (Ring-0):** *Optional.* Prüft den Windows/Linux-Treiber auf Memory-Leaks und I/O Sicherheit. |

<div style="page-break-after: always;"></div>

### 3. Der Audit-Report (.brep) & Dual-Seal Technologie

Nach jedem Durchlauf generiert ProTU eine Protokolldatei im Ausführungsverzeichnis. Diese Datei nutzt die Namenskonvention `ProTU_YYYYMMDD_MODUL.brep` (z.B. `ProTU_20260301_ALL.brep`).

#### 3.1 Aufbau des Reports

Der Report listet chronologisch jeden ausgeführten Test, den injizierten Payload und das kryptografische Resultat auf.
Die Ergebnisse werden in drei Stufen klassifiziert:

* **[INFO]**: Statusmeldung oder Start einer Test-Routine.
* **[PASS]**: Der Test wurde bestanden. Das Modul arbeitet mathematisch und physikalisch korrekt.
* **[FAIL] / [CRITICAL]**: Ein Sicherheitstest ist fehlgeschlagen. *Das System muss sofort isoliert werden.*

#### 3.2 Dual-Seal (Integrität des Reports)

Ein Audit-Report ist nur dann belastbar, wenn er nicht nachträglich manipuliert werden kann. Daher nutzt ProTU die **Dual-Seal Technologie**.

Bevor die `.brep`-Datei geschlossen wird, ruft ProTU die intern statisch gelinkte `ProHash`-Engine auf. Es wird ein deterministischer SHA-256 (ProHash) Fingerabdruck über den gesamten Report-Text erstellt und als kryptografisches Siegel am Ende der Datei angehängt.

* **Nutzen für den Auditor:** Wenn auch nur ein einziges Zeichen im Report nachträglich verändert wird (z. B. um ein `FAIL` in ein `PASS` zu ändern), bricht das Siegel und der Report ist vor Auditoren ungültig.

### 4. Interpretation der Exit-Codes (Für Automatisierung)

Für die nahtlose Integration in Management-Systeme (z. B. Nagios, PRTG, Jenkins) liefert die ProTU klare System-Exit-Codes zurück:

* **Exit Code 0 (Success):** Alle durchgeführten Tests wurden bestanden (`g_pt_failed == 0`). Das System ist einsatzbereit und "Gold-Standard" zertifiziert.
* **Exit Code 1 (Failure):** Mindestens ein sicherheitskritischer Test ist fehlgeschlagen. Der Vorgang wurde abgebrochen, der Fehler im `.brep` Report detailliert protokolliert.

---

| © 2026 Sascha Köhne | ✉️ **Contact:** [koehne83@googlemail.com](mailto:koehne83@googlemail.com) | 🛡️ **ProTU-Verified** |
| --- | --- | --- |
| System Architect | BrainAI Inhaber : Sascha Alexander Köhne | We don't need **BRUTEFORCE**, we know **PHYSICS** |
