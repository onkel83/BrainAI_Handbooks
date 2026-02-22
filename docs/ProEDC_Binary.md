## Revisionshistorie / Revision History

| Version | Datum | Autor | Status | Beschreibung |
| --- | --- | --- | --- | --- |
| 1.0.0 | 01.02.2026 | S. Köhne | Release | Initialer Release der ProEDC CLI-Dokumentation |

<div style="page-break-after: always;"></div>

## 1. Produktübersicht

Die **ProEDC (ProEncryption CLI)** ist ein leistungsstarkes, plattformübergreifendes Kommandozeilenwerkzeug (Windows, Linux, Android) für kryptografische Operationen. Sie bündelt die Kerntechnologien der Pro-Suite (ProED, ProHash und ProKey) in einer einfach zu integrierenden Ausführungsdatei (`proedc.exe` bzw. `proedc`).

Das Tool ist darauf ausgelegt, von Skripten, Automatisierungspipelines oder anderen Anwendungen aufgerufen zu werden. Alle Abhängigkeiten (Shared Libraries) werden dynamisch zur Laufzeit geladen.

### Leistungsmerkmale

* **Schlüsselgenerierung:** Erzeugung kryptografisch sicherer Zufallsschlüssel (Hardware- und Jitter-Entropie).
* **Dateiverschlüsselung:** Schnelle, symmetrische Verschlüsselung von Dateien beliebiger Größe ohne Speicherengpässe.
* **Integritätsprüfung:** Deterministische Generierung von 256-Bit Datei-Hashes.
* **Lizenz-Profile:** Integriertes Profilmanagement (TRIAL, INDUSTRIAL, PROFESSIONAL) zur Steuerung von Kontingenten und Audit-Logs.

---

## 2. CLI-Befehlsreferenz

Der Aufruf der ProEDC erfolgt stets nach dem Muster:
`proedc <befehl> [parameter...]`

### 2.1 Versions- und Lizenzinfo abrufen

Zeigt die aktuelle Version, das aktive Lizenzprofil (z. B. TRIAL oder PROFESSIONAL) sowie den Treiberstatus an.

* **Befehl:** `version` oder `-v`
* **Aufruf:** `proedc version`

### 2.2 Schlüssel generieren (Keygen)

Erzeugt einen neuen kryptografischen Schlüssel und speichert ihn in einer Datei.

* **Befehl:** `genkey` oder `-g`
* **Syntax:** `proedc genkey <bits> <ausgabedatei>`
* **Beispiel:** `proedc genkey 256 secret.key`

### 2.3 Datei-Hash berechnen

Berechnet den ProHash-256 Wert einer Datei und gibt ihn als Hex-String auf der Konsole aus.

* **Befehl:** `hash`
* **Syntax:** `proedc hash <datei>`
* **Beispiel:** `proedc hash dokument.pdf`

### 2.4 Datei verschlüsseln (Encrypt)

Verschlüsselt eine Eingabedatei mit dem angegebenen Schlüssel.

* **Befehl:** `enc` oder `-e`
* **Syntax:** `proedc enc <eingabedatei> <ausgabedatei> <schlüsseldatei>`
* **Beispiel:** `proedc enc daten.csv daten.csv.enc secret.key`

### 2.5 Datei entschlüsseln (Decrypt)

Entschlüsselt eine zuvor verschlüsselte Datei.

* **Befehl:** `dec` oder `-d`
* **Syntax:** `proedc dec <eingabedatei> <ausgabedatei> <schlüsseldatei>`
* **Beispiel:** `proedc dec daten.csv.enc daten_klar.csv secret.key`

<div style="page-break-after: always;"></div>

## 3. Rückgabewerte und Fehlercodes (Exit Codes)

Das CLI-Tool gibt bei Beendigung einen Exit-Code an das Betriebssystem zurück. Ein Wert von `0` bedeutet, dass die Operation erfolgreich war. Jeder Wert ungleich `0` weist auf einen Fehler hin.

Zusätzlich gibt das Tool bei Fehlern einen detaillierten Text über die Standardausgabe (stdout/stderr) aus.

| Status-Code | Interner Name | Beschreibung / Ursache |
| --- | --- | --- |
| **0** | `PEDC_SUCCESS` | Die Operation wurde erfolgreich abgeschlossen. |
| **-1** | `PEDC_ERR_PARAM` | Ungültige Parameter (z. B. fehlende Argumente beim Aufruf). |
| **-2** | `PEDC_ERR_FILE_IO` | Datei konnte nicht gelesen oder geschrieben werden (Fehlende Rechte, Datei nicht gefunden). |
| **-3** | `PEDC_ERR_CRYPTO` | Interner Fehler in der ProED Verschlüsselungs-Engine. |
| **-4** | `PEDC_ERR_HASH` | Fehler bei der Hash-Berechnung (z. B. Abbruch des Datenstroms). |
| **-5** | `PEDC_ERR_KEYGEN` | Fehler bei der Schlüsselgenerierung (Fehlende Entropie). |
| **-6** | `PEDC_ERR_MEMORY` | Unzureichender Arbeitsspeicher (Out of Memory). |
| **-7** | `PEDC_ERR_LIMIT_REACHED` | **Lizenz-Limit erreicht!** Die maximale Anzahl an Verschlüsselungen oder Schlüsselgenerierungen für das aktuelle Profil (z. B. TRIAL) wurde überschritten. Ein Upgrade ist erforderlich. |

---

## 4. Anwendungsbeispiele (Workflow)

Ein typischer Ablauf zur Absicherung eines Datenexport-Skripts sieht wie folgt aus:

**Schritt 1: Schlüssel erzeugen**

```bash
> proedc genkey 256 master.key
[*] Generating Key (256 bits) -> master.key ... Success

```

**Schritt 2: Datei verschlüsseln**

```bash
> proedc enc backup_2026.zip backup_2026.enc master.key
[*] Encrypting backup_2026.zip -> backup_2026.enc ... Success

```

**Schritt 3: Integrität der verschlüsselten Datei protokollieren**

```bash
> proedc hash backup_2026.enc
File: backup_2026.enc
Hash: a1b2c3d4e5f6... (64 Zeichen Hex-String)

```

<div style="page-break-after: always;"></div>

| © 2026 Sascha Köhne | ✉️ **Contact:** [koehne83@googlemail.com](mailto:koehne83@googlemail.com) | 🛡️ **ProEDC-Verified** |
| --- | --- | --- |
| CEO / System Architect | BrainAI UG (haftungsbeschränkt) | We don't need **BRUTEFORCE**, we knows **PHYSICS** |
