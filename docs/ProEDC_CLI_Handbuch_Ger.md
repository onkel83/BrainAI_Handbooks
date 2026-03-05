## Revisionshistorie / Revision History

| Version | Datum | Autor | Status | Beschreibung |
| --- | --- | --- | --- | --- |
| 1.0.0 | 01.01.2025 | S. Köhne | Stable | Initial Commercial Suite CLI |
| 2.0.0 | 26.02.2026 | S. Köhne | Stable | Hybrid Ring-0, Audit-Logging & Secure Wipe (PRO Edition) |

<div style="page-break-after: always;"></div>

### 1 Produktübersicht

Die **ProEDC CLI Suite** ist das zentrale Administrations-Werkzeug der kommerziellen ProED-Serie. Im Gegensatz zur Standard-Version integriert ProEDC ein vollständiges Lizenzmanagement, erweiterte kryptografische Hilfsfunktionen (KeyGen, Hashing) und einen revisionssicheren Audit-Trail.

**Kernmerkmale:**

* **Hybride Beschleunigung (PRO Only):** Automatische Erkennung und Nutzung des `ProKM` Kernel-Treibers für verlustfreie Ring-0 Performance.
* **Revisionssicheres Audit-Logging:** Optionales Logging von Vorgängen inklusive kryptografischer Ein- und Ausgangs-Hashes zur lückenlosen Nachweisbarkeit.
* **Sicheres Daten-Shredding (PRO Only):** Mehrfaches Überschreiben der Quelldateien nach erfolgreicher Verarbeitung zur Erfüllung höchster Datenschutz-Standards.
* **Auto-Keygen:** Automatische Erstellung sicherer 256-Bit Schlüssel bei fehlenden Parametern im Verschlüsselungsprozess.
* **Integrierte Suite:** Vereint Datei-Verschlüsselung, Schlüsselgenerierung und ProHash-Verifizierung in einer einzigen Binary.

### 2 Technische Spezifikationen

#### 2.1 Kommandozeilen-Parameter (CLI)

Die Suite folgt der Syntax: `proedc [MODUS] [OPTIONEN]`.

**Modi:**

* `-e, --encrypt`: Verschlüsselungsmodus.
* `-d, --decrypt`: Entschlüsselungsmodus.
* `-g, --keygen`: Generierung kryptografischer Schlüssel.
* `--hash`: Erstellung von ProHash-Werten für Dateien oder Strings.

**Optionen:**

* `-i, --input <pfad>`: Pfad zur Eingabedatei.
* `-o, --output <pfad>`: Pfad zur Ausgabedatei.
* `-k, --key <pfad>`: Pfad zur Schlüsseldatei.
* `-s, --string <text>`: Text für direktes Hashing (ohne Datei).
* `-b, --bits <n>`: Schlüssellänge in Bits (Standard: 256).
* `-L, --log`: **(PRO)** Aktiviert detailliertes Audit-Logging.
* `-D, --delete`: **(PRO)** Sicheres Löschen der Quelldatei nach Erfolg.

<div style="page-break-after: always;"></div>

### 3 Globales Error-Management

ProEDC nutzt ein erweitertes Status-Modell, das Lizenz- und Hardware-Zustände berücksichtigt.

| **Exit Code** | **Konstante** | **Beschreibung** | **Kritikalität** |
| --- | --- | --- | --- |
| **0** | PEDC_SUCCESS | Operation erfolgreich abgeschlossen. | Keine |
| **1** | PEDC_ERR_PARAM | Ungültige Parameter oder fehlende Argumente. | Mittel |
| **2** | PEDC_ERR_AUTH | MAC-Check fehlgeschlagen (Datei manipuliert). | Hoch |
| **3** | PEDC_ERR_FILE_IO | Fehler beim Lesen/Schreiben von Dateien. | Mittel |
| **-7** | PEDC_ERR_LIMIT | Lizenz-Limit erreicht (z.B. TRIAL-Ablauf). | Fatal |

<div style="page-break-after: always;"></div>

### 4 Implementierungsbeispiele

#### 4.1 Verschlüsselung mit Auto-Keygen & Audit-Log (PRO)

In diesem Beispiel wird eine Datei verschlüsselt. Da kein Schlüssel angegeben ist, erstellt ProEDC diesen automatisch und loggt alle Hashes.

```bash
# Aufruf in der PRO Edition
./proedc -e -i vertraulich.dat -o vertraulich.enc -L

# Ergebnis in proedc_audit.log:
# [2026-02-26 14:30:00] OP: AUTO-KEY | Status: OK | IN: N/A | OUT: auto_generated_proedc.key
# [2026-02-26 14:30:00] OP: ENCRYPT  | Status: OK | IN: vertraulich.dat (Hash: A1B2...) | OUT: vertraulich.enc (Hash: C3D4...)

```

#### 4.2 Entschlüsselung mit anschließendem Shredding (PRO)

Dieses Kommando entschlüsselt eine Datei unter Verwendung des Kernel-Bypasses und löscht die verschlüsselte Quelldatei unwiderruflich nach Erfolg.

```bash
./proedc -d -i backup.enc -o backup.zip -k master.key -D
# [+] PRO Edition: Hardware Kernel Driver (ProKM) detected.
# [+] PRO Feature: Source file securely wiped (-D).

```

#### 4.3 String-Hashing für Konfigurationen

Direktes Erzeugen eines ProHash-Wertes für einen Text-String:

```bash
./proedc --hash -s "Master-Admin-Passwort-2026"
# String Hash: 7f83b165...

```

<div style="page-break-after: always;"></div>

### 5 Best Practices & Sicherheitsrichtlinien

* **Audit-Konformität:** Verwenden Sie in automatisierten Umgebungen grundsätzlich das `-L` Flag, um bei Sicherheitsaudits die Integrität der Datenkette lückenlos beweisen zu können.
* **Daten-Hygiene:** Nutzen Sie in der PRO Edition das `-D` Flag für temporäre Dateien, um sicherzustellen, dass keine Fragmente sensibler Daten auf physischen Datenträgern zurückbleiben.
* **Lizenzüberwachung:** Überprüfen Sie regelmäßig den Exit-Code `-7`, um rechtzeitig vor Ablauf von TRIAL-Lizenzen oder Volumen-Limits in produktiven Systemen reagieren zu können.
* **Kernel-Privilegien:** Für die Nutzung der Ring-0 Beschleunigung muss die Executable über ausreichende Berechtigungen verfügen, um mit dem `ProKM`-Device zu kommunizieren.

---

| © 2026 Sascha Köhne | ✉️ **Contact:** [koehne83@googlemail.com](mailto:koehne83@googlemail.com) | 🛡️ **ProTU-Verified** |
| --- | --- | --- |
| CEO / System Architect | BrainAI Inhaber : Sascha Alexander Köhne | We don't need **BRUTEFORCE**, we know **PHYSICS** |
