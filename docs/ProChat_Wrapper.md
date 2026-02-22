## Revisionshistorie / Revision History

| Version | Datum | Autor | Status | Beschreibung |
| --- | --- | --- | --- | --- |
| 1.0.0 | 22.02.2026 | S. Köhne | Stable | Erweiterung um Error-Handling & Multi-Language Deep-Dive |

<div style="page-break-after: always;"></div>

## 1. Technische Architektur & Protokoll-Spezifikation

Das **ProChat SDK** implementiert ein zustandsbehaftetes, kryptografisches Protokoll auf Basis der **Pro-Suite**. Es ist darauf ausgelegt, absolute Vertraulichkeit und Integrität in unsicheren Netzwerken zu garantieren.

### 1.1 Mathematisches Fundament

* **Verschlüsselung:** Nutzt die **ProED Engine** (Symmetrische Stromverschlüsselung).
* **Integrität & Authentizität:** Ein HMAC-Konstrukt basierend auf der **ProHash-256 Sponge-Funktion** sichert jedes Paket.
* **Forward Secrecy:** Nach jeder erfolgreichen Operation (Senden/Empfangen) wird der Sitzungsschlüssel mittels einer Einweg-Funktion (Ratchet) transformiert.

### 1.2 Paket-Struktur (Binary Layout)

Jedes Paket hat eine feste Größe von exakt **1024 Bytes** (`PC_PKT_SIZE`).

1. **Tag (4 Bytes):** Identifikator zur schnellen Sitzungszuordnung.
2. **Header (12 Bytes):** Verschlüsselte Metadaten (Sender-ID, Counter, Typ).
3. **Payload (988 Bytes):** Die eigentlichen Nutzdaten, verschlüsselt und mit Padding versehen.
4. **MAC (16 Bytes):** Authentifizierungs-Tag (HMAC-ProHash).

## 2. Detailliertes Error-Handling

Das SDK verwendet strikte Rückgabewerte. Ein Wert ungleich `PC_OK` (0) muss zwingend behandelt werden, um die Sitzungssynchronität nicht zu gefährden.

| Konstante | Wert | Ursache | Empfohlene Reaktion |
| --- | --- | --- | --- |
| `PC_OK` | 0 | Operation erfolgreich abgeschlossen. | Fortfahren mit dem nächsten Paket. |
| `PC_ERR_PARAM` | -1 | Ungültige Argumente (z.B. `null`-Pointer) oder Puffergrößen unterhalb der Mindestanforderung. | Logikprüfung: Sind Keys/Seeds exakt 32 Bytes lang? |
| `PC_ERR_AUTH` | -2 | Der Integritätscheck (MAC) ist fehlgeschlagen. Das Paket wurde entweder manipuliert oder mit einem falschen Schlüssel verschlüsselt. | Paket verwerfen. Bei gehäuftem Auftreten: Verdacht auf Angriff oder Key-Missmatch. |
| `PC_ERR_REPLAY` | -3 | Ein Paket mit einem bereits verwendeten oder veralteten Counter wurde empfangen. | Paket verwerfen (Schutz gegen Replay-Attacks). |
| `PC_ERR_DESYNC` | -4 | Der interne Ratchet-State des Empfängers passt nicht mehr zum Sender. | **Fataler Fehler:** Die Session muss terminiert und mit einem neuen Key-Exchange neu initialisiert werden. |

<div style="page-break-after: always;"></div>

## 3. Multi-Language SDK Referenz

### 3.1 C# (.NET / Unity)

Das C#-Interface nutzt `P/Invoke`. Bei Fehlern sollte die `ProChatClient`-Klasse entsprechende Exceptions werfen oder `null` zurückgeben.

```csharp
try {
    var client = new ProChatClient(myUid, mySeed);
    if (!client.AddPeer(peerUid, sharedKey)) {
        throw new Exception("Fehler beim Hinzufügen des Peers.");
    }

    byte[] packet = client.EncryptMessage(peerUid, 1, data);
    if (packet == null) {
        // Fehlerbehandlung: z.B. Logik-Error oder Key fehlt
    }
} catch (ArgumentException ex) {
    // Behandlung von PC_ERR_PARAM (falsche Längen)
}

```

### 3.2 Python (3.x)

In Python werden `bytes`-Objekte verwendet. Der Wrapper validiert die Längen, bevor der C-Stack aufgerufen wird.

```python
chat = ProChat(1337, seed)
if not chat.add_peer(42, key):
    print("Kritisch: Peer konnte nicht initialisiert werden.")

# Entschlüsselung mit Error-Checking
result = chat.decrypt(incoming_pkt)
if result is None:
    print("Fehler: Authentifizierung fehlgeschlagen (PC_ERR_AUTH).")
else:
    sender_uid, payload = result
    print(f"Nachricht von {sender_uid} erhalten.")

```

### 3.3 Java (JNA)

Java-Entwickler profitieren von der `DecryptedMessage`-Hilfsklasse, die das Resultat kapselt.

```java
ProChat.Client client = new ProChat.Client(myUid, mySeed);
byte[] packet = client.encryptMessage(targetUid, (byte)1, message);

if (packet != null) {
    ProChat.DecryptedMessage result = client.decryptPacket(packet);
    if (result == null) {
        // Fehlerbehandlung für PC_ERR_AUTH oder PC_ERR_DESYNC
    }
}

```

### 3.4 JavaScript (Node.js)

Node.js nutzt Buffer. Fehler werden über Rückgabewerte oder `null`-Validierung signalisiert.

```javascript
const client = new ProChat(1337, seed);
const packet = client.encrypt(42, 1, "Geheime Nachricht");

if (!packet) {
    console.error("Verschlüsselung fehlgeschlagen - Parameter prüfen.");
}

const result = client.decrypt(packet);
if (!result) {
    console.error("Entschlüsselung fehlgeschlagen (MAC Error).");
}

```

<div style="page-break-after: always;"></div>

## 4. Sicherheitsrichtlinien & Best Practices

1. **RAM-Sanitization:** Die native Library wischt (`wipe`) sensible Daten und Zwischenschlüssel sofort nach der Verwendung aus dem Speicher. Stellen Sie sicher, dass auch Ihre Wrapper-Anwendung Byte-Arrays nach Gebrauch nullt (sofern die Sprache dies zulässt).
2. **Einmalige Seeds:** Der `pc_init`-Seed dient dem Schutz der Session-Keys im RAM gegen Memory-Dumping. Er muss kryptografisch sicher generiert werden (z.B. via `ProKey_Generate`).
3. **Sitzungs-Synchronität:** Da ProChat einen Ratchet verwendet, führt das Verlieren eines Pakets oder eine falsche Reihenfolge unweigerlich zu `PC_ERR_DESYNC`. Implementieren Sie auf Netzwerkebene (TCP/WebSockets) Mechanismen zur Sicherstellung der Paketreihenfolge.
4. **Konstante Laufzeit:** Der MAC-Vergleich erfolgt in der Core-Engine in konstanter Zeit, um Side-Channel-Angriffe (Timing Attacks) zu verhindern.

---

| © 2026 Sascha Köhne | ✉️ **Contact:** [koehne83@googlemail.com](mailto:koehne83@googlemail.com) | 🛡️ **ProChat-Verified** |
| --- | --- | --- |
| CEO / System Architect | BrainAI UG (haftungsbeschränkt) | We don't need **BRUTEFORCE**, we knows **PHYSICS** |
