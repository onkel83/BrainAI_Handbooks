## Editions-Matrix & Wettbewerbsvorteile / Edition Matrix & Competitive Advantages

| Version | Status | Zielgruppe | Fokus |
| --- | --- | --- | --- |
| 1.0.0 | Stable | Enterprise & OT-Security | Compliance & Performance |

<div style="page-break-after: always;"></div>

### 1. Technische Editions-Beschränkungen

Die Differenzierung der Versionen erfolgt hartcodiert über die `ProEDC_Status`-Struktur und die Editions-Flags im Build-Prozess. Folgende Grenzwerte sind für den Verschlüsselungsprozess aktiv:

| Feature | **TRIAL** | **INDUSTRIAL** | **PRO** |
| --- | --- | --- | --- |
| **Max. Datenvolumen** | **100 MB** Gesamtdatendurchsatz | **10 GB** Gesamtdatendurchsatz | **Unlimitiert** |
| **Kernel-Bypass (Ring-0)** | Deaktiviert (Nur Software) | Deaktiviert (Nur Software) | **Aktiv via ProKM** |
| **Audit-Logging (Pairing)** | Basis-Log (nur Name) | Basis-Log (nur Name) | **Vollständig (In/Out Hashes)** |
| **Sicheres Wiping (-D)** | Nicht unterstützt | Nicht unterstützt | **Aktiv (7-Pass Shredding)** |
| **Support-Schnittstelle** | Community | Business | **Priority (Real-time)** |

*Hinweis: Beim Erreichen der Volumen-Grenzwerte gibt die API den Status `PEDC_ERR_LIMIT_REACHED` (-7) zurück, und weitere Verschlüsselungsvorgänge werden blockiert.*

<div style="page-break-after: always;"></div>

### 2. Strategische Vorteile (The ProSuite Advantage)

ProEDC unterscheidet sich fundamental von Standardlösungen durch die konsequente Umsetzung der Zero-Trust-Philosophie auf Code-Ebene:

#### 2.1 Mathematisch beweisbare Integrität

* **Hash-Pairing:** Im Gegensatz zu Tools, die nur die erfolgreiche Operation melden, speichert ProEDC (PRO) das Paar aus Original-Hash und Chiffrat-Hash. Dies ist der einzige Weg, um in einem Audit (z. B. nach DSGVO oder TISAX) die Unverfäschtheit der Datenkette zweifelsfrei nachzuweisen.
* **ProHash-Standard:** Wir nutzen keine generischen Hash-Verfahren, sondern den optimierten ProHash-Algorithmus, der speziell auf Side-Channel-Resistenz geprüft wurde.

#### 2.2 Physische Souveränität

* **Kernel-Direct I/O:** Durch die direkte Kommunikation mit dem `ProKM`-Treiber umgeht die PRO-Edition die Dateisystem-Latenzen des Betriebssystems. Daten werden für den Verschlüsselungsprozess direkt an den Kernel übergeben, was die Angriffsfläche im Arbeitsspeicher (RAM) minimiert.
* **Autarke Entropie:** Die Suite ist nicht auf den Zufallsgenerator des Betriebssystems angewiesen. Durch die Integration von **ProKey** wird hardwarenaher Jitter zur Schlüsselerzeugung genutzt, was Schutz gegen staatliche Backdoors in Standard-APIs bietet.

#### 2.3 Compliance & Anti-Forensik

* **Secure Wipe:** Während Standard-Löschbefehle nur den Dateizeiger entfernen, überschreibt ProEDC (PRO) die Sektoren der Quelldatei aktiv mit Nullen. Selbst mit forensischen Methoden ist eine Wiederherstellung der Originaldaten nach dem `-D` Befehl ausgeschlossen.

<div style="page-break-after: always;"></div>

### 3. Vergleich am Markt

| Merkmal | Standard (OpenSSL / GPG) | Cloud-Provider Tools | **ProEDC Suite** |
| --- | --- | --- | --- |
| **Architektur** | Ring-3 (User) | API-Basiert | **Hybrid (Ring-0 & 3)** |
| **Datenkontrolle** | Lokal (Software) | Extern (Provider) | **Hardware-Souverän** |
| **Lizenzstopp** | Nein | Abo-Modell | **Volumen-basiert (-7)** |
| **Audit-Trail** | Extern / Manuell | Proprietär | **Native In/Out Hashes** |

---

| © 2026 Sascha Köhne | ✉️ **Contact:** [koehne83@googlemail.com](mailto:koehne83@googlemail.com) | 🛡️ **ProTU-Verified** |
| --- | --- | --- |
| CEO / System Architect | BrainAI Inhaber : Sascha Alexander Köhne | We don't need **BRUTEFORCE**, we know **PHYSICS** |
