## Revisionshistorie / Revision History

| Version | Datum | Autor | Status | Beschreibung |
| --- | --- | --- | --- | --- |
| 1.0.0 | 22.02.2026 | S. Köhne | Stable | Forward the Secret |

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

```csharp
using ProChat;

public void SecureCommunicationExample()
{
    try 
    {
        // 1. Initialisierung mit 32-Byte Seed
        byte[] ramSeed = GetSecureEntropy(32);
        var client = new ProChatClient(uid: 1001, seed: ramSeed);

        // 2. Peer hinzufügen
        byte[] sharedKey = GetSharedKeyFromHandshake();
        if (!client.AddPeer(2002, sharedKey))
        {
            throw new InvalidOperationException("Peer-Initialisierung fehlgeschlagen.");
        }

        // 3. Senden mit Exception-Handling
        byte[] payload = System.Text.Encoding.UTF8.GetBytes("BrainAI Industry Standard");
        byte[] encryptedPacket = client.EncryptMessage(2002, type: 1, message: payload);
        
        if (encryptedPacket == null)
        {
            // Interner Engine-Fehler (z.B. Memory)
            LogError("Verschlüsselungsfehler in der Core Engine.");
        }

        // 4. Empfang und Validierung
        uint senderId;
        byte[] decrypted = client.DecryptPacket(receivedBytes, out senderId);
        
        if (decrypted == null)
        {
            // Hier greift der Schutz gegen Manipulation (PC_ERR_AUTH)
            throw new SecurityException("Integritätsprüfung fehlgeschlagen! Paket wurde manipuliert.");
        }
    }
    catch (ArgumentException ex)
    {
        LogError($"Parameterfehler: {ex.Message}");
    }
}
```

<div style="page-break-after: always;"></div>

### 5. Python (High-Performance Scripting)

Der _Python-Wrapper_ ist für maximale Flexibilität ausgelegt. Er konvertiert C-Fehlercodes in Python-Rückgabewerte, die einfach validiert werden können.

#### 5.1 Implementierungsbeispiel

```Python
from prochat import ProChat, ProChatStatus

def run_chat_service():
    # Setup Engine
    seed = b'\x12' * 32
    client = ProChat(uid=1001, seed=seed)
    
    # Peer Setup
    key = b'\xAB' * 32
    if not client.add_peer(peer_uid=2002, key=key):
        print("Fehler: Peer konnte nicht registriert werden.")
        return

    # Sende-Loop
    msg = b"System-Health: OK"
    packet = client.encrypt(target_uid=2002, msg_type=1, message=msg)
    
    if packet:
        send_to_socket(packet)

    # Empfangs-Logik mit Validierung
    raw_data = socket.receive(1024)
    result = client.decrypt(raw_data)
    
    if result is None:
        # Hier erkennt Python PC_ERR_AUTH oder PC_ERR_DESYNC
        handle_security_incident("Manipulation erkannt!")
    else:
        sender_id, payload = result
        print(f"Sichere Nachricht von {sender_id}: {payload.decode()}")
```

<div style="page-break-after: always;"></div>

### 6. Java (JNA / Enterprise Integration)
Die _Java_-Implementierung nutzt eine statische IProChat-Library Instanz und bietet durch die DecryptedMessage-Klasse ein sauberes Datenhandling.

#### 6.1 Implementierungsbeispiel

```Java
import com.brainai.prochat.ProChat;

public class SecureGateway {
    public void processIncoming(byte[] packet) {
        ProChat.Client client = new ProChat.Client(1001, mySeed);
        
        // Entschlüsselungs-Versuch
        ProChat.DecryptedMessage msg = client.decryptPacket(packet);
        
        if (msg == null) {
            // Behandlung von Engine-Fehlern
            System.err.println("Kritischer Fehler: Authentifizierung fehlgeschlagen (MAC-Error).");
            auditLog.reportAttack(System.currentTimeMillis(), "MAC_FAIL");
            return;
        }
        
        System.out.println("Paket authentifiziert von UID: " + msg.senderUid);
        processPayload(msg.data);
    }
}
```

<div style="page-break-after: always;"></div>

### 7. JavaScript (Node.js / Web-Services)
Der _Node.js_ Wrapper nutzt Buffer für Zero-Copy Operationen und ist ideal für skalierbare Backend-Systeme geeignet.

#### 7.1 Implementierungsbeispiel

```JavaScript
const ProChat = require('./ProChat');

const chat = new ProChat(1001, seedBuffer);

// Senden
const packet = chat.encrypt(2002, 1, "Cloud-Status: Green");
if (!packet) {
    console.error("Verschlüsselungsfehler - Engine-Parameter prüfen.");
}

// Empfangen
const result = chat.decrypt(incomingBuffer);
if (!result) {
    // Deckt PC_ERR_AUTH (-2) ab
    console.error("Sicherheitswarnung: Paket-Integrität verletzt!");
} else {
    const { senderUid, payload } = result;
    console.log(`Verifizierte Daten von ${senderUid}:`, payload.toString());
}
```

<div style="page-break-after: always;"></div>

### 8. VB.NET (Industrial Systems)
Speziell für industrielle Automatisierungssysteme bietet der VB.NET-Wrapper volle Kompatibilität und striktes Error-Handling via IsNot Nothing Prüfungen.

#### 8.1 Implementierungsbeispiel

```VB.Net
Public Sub HandleIndustrialMessage(packet As Byte())
    Dim client As New ProChatClient(1001, globalSeed)
    Dim senderUid As UInteger = 0
    
    ' Entschlüsseln und Validieren
    Dim payload = client.DecryptPacket(packet, senderUid)
    
    If payload Is Nothing Then
        ' Fehlerbehandlung für PC_ERR_AUTH oder PC_ERR_REPLAY
        RaiseSecurityEvent("Integritätsfehler bei Sender " & senderUid)
        Exit Sub
    End If
    
    ' Verarbeitung bei Erfolg
    LogMessage("Sicheres Datenpaket von " & senderUid & " empfangen.")
    UpdateMachineState(payload)
End Sub
```

<div style="page-break-after: always;"></div>

### 9. Best Practices & SicherheitsrichtlinienSitzungs-Synchronität: 
* ProChat ist ein zustandsbehaftetes Protokoll. Pakete müssen in der exakten Reihenfolge des Versands empfangen werden. Bei UDP-Übertragung sollte eine Sequenz-Prüfung vorgeschaltet werden, um PC_ERR_DESYNC zu vermeiden.
* Entropie-Quelle: Verwenden Sie für die Generierung der Seeds und Keys ausschließlich kryptografisch sichere Zufallsgeneratoren (z.B. die ProKey-Engine).
* Key-Rotation: Auch wenn der Ratchet-Mechanismus Forward Secrecy bietet, wird empfohlen, die Master-Keys in regelmäßigen Abständen (z.B. alle 24h oder nach 1 GB Datendurchsatz) zu rotieren.
* Speicher-Bereinigung: In Sprachen mit Garbage Collection (Java, C#, JS) sollten sensitive Buffer nach der Nutzung explizit mit Nullen überschrieben werden, bevor sie für den GC freigegeben werden.

---

| © 2026 Sascha Köhne | ✉️ **Contact:** [koehne83@googlemail.com](mailto:koehne83@googlemail.com) | 🛡️ **ProTU-Verified** |
| --- | --- | --- |
| CEO / System Architect | BrainAI UG (haftungsbeschränkt) | We don't need **BRUTEFORCE**, we knows **PHYSICS** |
