# Theorie TAG-8: Netzwerkanalyse und Fehlersuche

## Warum systematisch?

Wer bei einer Störung wahllos Einstellungen ändert, findet den Fehler nur zufällig und baut oft neue Fehler ein. Systematische Fehlersuche heisst: zuerst das Symptom genau beschreiben, dann den Fehler Schritt für Schritt eingrenzen und erst ändern, wenn die Ursache klar ist.

### Ablauf

1. **Symptom erfassen:** Was genau geht nicht? Seit wann? Wer ist betroffen (ein Gerät, ein Netz, alle)? Was wurde zuletzt geändert?
2. **Soll-Zustand klären:** Netzwerkdokumentation, IP-Plan, Schema (siehe TAG-7).
3. **Eingrenzen:** mit Tests den Fehler auf ein Gerät, eine Verbindung und eine OSI-Schicht eingrenzen.
4. **Ursache bestimmen:** Ist-Zustand mit Soll-Zustand vergleichen.
5. **Beheben:** immer nur eine Änderung aufs Mal.
6. **Verifizieren:** dieselben Tests erneut durchführen.
7. **Dokumentieren:** Symptom, Ursache, Behebung festhalten.

## Das OSI-Modell als Leitfaden

| Methode | Vorgehen | Geeignet wenn |
|---|---|---|
| **Bottom-up** | bei Schicht 1 beginnen (Kabel, Link-LED), dann 2, 3 ... | Hardware- oder Verkabelungsproblem vermutet, ein einzelnes Gerät betroffen |
| **Top-down** | bei der Anwendung beginnen (Browser, DNS), dann nach unten | nur eine Anwendung betroffen, Netz grundsätzlich ok |
| **Divide and Conquer** | in der Mitte beginnen, meist mit `ping` (Schicht 3). Geht der Ping, liegt der Fehler oben, sonst unten | Standardfall, am schnellsten |
| **Ausschlussverfahren** | funktionierende Teile ausschliessen (geht es von einem anderen PC? im selben Netz?) | mehrere Geräte betroffen |

### Fehler nach Schicht

| Schicht | Typische Fehler | Prüfung |
|---|---|---|
| 1 Bitübertragung | Kabel defekt oder nicht gesteckt, Netzwerkkarte ausgeschaltet, falscher Kabeltyp | Link-LED (rot/grün), `show interfaces status`, Port-Status in Packet Tracer |
| 2 Sicherung | Port administrativ abgeschaltet, falsches VLAN, doppelte MAC-Adresse, Layer-2-Loop, falscher SAT-Eintrag | `show mac-address-table`, `show vlan brief`, `show spanning-tree`, `arp -a` |
| 3 Vermittlung | IP im falschen Netz, falsche Subnetzmaske, doppelte IP, Gateway fehlt/falsch, Route fehlt, Router-Interface falsch | `ipconfig`, `ping`, `tracert`, `show ip interface brief`, `show ip route` |
| 4-7 Anwendung | DNS falsch eingetragen, Dienst nicht gestartet, DHCP nicht erreichbar | `nslookup`, Browser mit IP statt Name, `ipconfig /renew` |

## Diagnosebefehle

### Am PC (Windows / Packet Tracer Command Prompt)

| Befehl | Zweck |
|---|---|
| `ipconfig` / `ipconfig /all` | IP, Maske, Gateway, DNS, MAC, DHCP-Server anzeigen |
| `ipconfig /release` / `ipconfig /renew` | DHCP-Adresse freigeben und neu anfordern |
| `ping <IP>` | Erreichbarkeit auf Schicht 3 prüfen |
| `tracert <IP>` | Weg über die Router verfolgen, zeigt bei welchem Hop es abbricht |
| `arp -a` | ARP-Cache: welche MAC gehört zu welcher IP |
| `nslookup <Name>` | Namensauflösung prüfen und welcher DNS-Server antwortet |

### Auf Cisco-Switches und -Routern

| Befehl | Zweck |
|---|---|
| `show running-config` | aktuelle Konfiguration |
| `show ip interface brief` | Interfaces mit IP und Status (up/down, administratively down) |
| `show interfaces status` | Ports, Status, VLAN, Duplex, Speed (Switch) |
| `show mac-address-table` | SAT: welche MAC an welchem Port gelernt wurde |
| `clear mac-address-table dynamic` | SAT leeren |
| `show vlan brief` | VLANs und zugeordnete Ports |
| `show spanning-tree` | Root Bridge, Portrollen, blockierte Ports |
| `show ip route` | Routingtabelle |
| `show cdp neighbors` | welches Nachbargerät an welchem Port hängt |
| `show ip dhcp binding` | vergebene DHCP-Adressen (Router als DHCP-Server) |

### Packet Tracer

- **Realtime- und Simulationsmodus:** Im Simulationsmodus sieht man jedes Paket einzeln und kann im PDU-Fenster nachsehen, auf welcher Schicht ein Gerät das Paket verwirft.
- **Inspect-Lupe:** zeigt ARP-Tabelle, MAC-Tabelle und Routingtabelle eines Geräts.
- **Link-LEDs:** grün = aktiv, orange = Spanning Tree blockiert / startet, rot = keine Verbindung.

## Typische Fehlerbilder

| Symptom | Wahrscheinliche Ursache |
|---|---|
| IP 169.254.x.x (APIPA) | DHCP-Server nicht erreichbar (anderes Subnetz ohne Relay, Netzwerkkarte aus, Server aus) |
| Ping im eigenen Netz geht, in fremde Netze nicht | Gateway fehlt oder falsch, Route auf dem Router fehlt |
| Ping auf das eigene Gateway geht nicht | Router-Interface falsch adressiert, abgeschaltet oder vertauscht |
| Ping geht nur in eine Richtung | Gateway oder Route beim Antwortenden fehlt (Hin- und Rückweg prüfen!) |
| Verbindung mal da, mal weg | doppelte IP- oder MAC-Adresse, Wackelkontakt |
| Ping per IP geht, per Name nicht | DNS falsch eingetragen oder DNS-Record fehlt |
| ganzes Netz extrem langsam, Switch-LEDs blinken dauernd | Layer-2-Loop ohne Spanning Tree (Broadcast-Sturm) |
| `tracert` bricht beim gleichen Router ab | auf diesem Router fehlt die Route ins Zielnetz oder zurück |

## DHCP über Subnetzgrenzen: DHCP-Relay

DHCP-Discover ist ein Broadcast und Router leiten Broadcasts nicht weiter. Liegt der DHCP-Server in einem anderen Subnetz, braucht es auf dem Router-Interface auf der Client-Seite einen **DHCP-Relay-Agent**:

```text
interface GigabitEthernet0/1
 ip helper-address 10.0.1.2
```

Der Router nimmt den Broadcast entgegen und schickt ihn als Unicast an den DHCP-Server. Im Feld `giaddr` trägt er die IP seines Client-seitigen Interfaces ein, so weiss der Server, aus welchem Pool er die Adresse vergeben muss. Der Server braucht deshalb für jedes Subnetz einen eigenen Pool mit dem richtigen Gateway.

## Layer-2-Loop erkennen

- **Spanning Tree** verhindert Loops, indem redundante Ports blockiert werden. `show spanning-tree` zeigt blockierte Ports (BLK).
- **SNMP-Monitoring** (z. B. PRTG, LibreNMS): Ein Loop zeigt sich als plötzlicher, massiver Anstieg von Broadcast- und Multicast-Zählern auf mehreren Ports gleichzeitig, Auslastung nahe 100 Prozent und hohe CPU-Last der Switches. Zudem senden Switches SNMP-Traps bei Topologieänderungen im Spanning Tree.
- **Syslog:** Meldungen über MAC-Flapping (dieselbe MAC-Adresse wird abwechselnd an zwei Ports gelernt).
- **Wireshark:** dieselben Broadcast-Frames tauchen in kurzer Zeit immer wieder auf.
- **Schutz:** RSTP, BPDU Guard und PortFast an Endgeräte-Ports, Storm Control.
