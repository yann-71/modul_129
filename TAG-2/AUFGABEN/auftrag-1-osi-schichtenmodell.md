# Auftrag 1: Das ISO/OSI-Schichtenmodell verstehen

## Weshalb wurde das ISO/OSI-Modell entwickelt?

In den 1970er/80er Jahren hatten viele Hersteller ihre eigenen, proprietären Netzwerkprotokolle, die nicht miteinander kompatibel waren. Die ISO hat das OSI-Modell entwickelt, um einen einheitlichen Standard zu schaffen, damit Geräte und Software von verschiedenen Herstellern miteinander kommunizieren können. Ausserdem hilft die Aufteilung in Schichten dabei, die Komplexität der Netzwerkkommunikation zu strukturieren, jede Schicht hat eine klar definierte Aufgabe und kann unabhängig von den anderen weiterentwickelt werden.

## Die 7 Schichten und ihre Aufgaben

| Schicht | Name | Aufgabe |
|---|---|---|
| 7 | Anwendung (Application) | Schnittstelle zu Anwendungen (z.B. Browser, E-Mail-Client) |
| 6 | Darstellung (Presentation) | Datenformat, Verschlüsselung, Kompression |
| 5 | Sitzung (Session) | Auf- und Abbau von Sitzungen zwischen Systemen |
| 4 | Transport | Zuverlässige Übertragung, Segmentierung, Ports (TCP/UDP) |
| 3 | Vermittlung (Network) | Logische Adressierung, Routing (IP) |
| 2 | Sicherung (Data Link) | Physische Adressierung (MAC), Fehlererkennung |
| 1 | Bitübertragung (Physical) | Übertragung von Bits über das physische Medium |

## Netzwerkgeräte und ihre Schichten

- **Switch (Layer 2):** arbeitet mit MAC-Adressen, leitet Frames innerhalb eines Netzwerks weiter
- **Router (Layer 3):** arbeitet mit IP-Adressen, leitet Pakete zwischen verschiedenen Netzwerken weiter
- **Access Point (Layer 1/2):** überträgt Signale drahtlos, arbeitet ähnlich wie ein Switch/Hub auf den unteren Schichten

## Protokolle und ihre Schichten

| Protokoll | Schicht |
|---|---|
| Ethernet | 2 (Sicherung) |
| IP | 3 (Vermittlung) |
| TCP / UDP | 4 (Transport) |
| HTTP | 7 (Anwendung) |
| DNS | 7 (Anwendung) |

## Unterschied ISO/OSI vs. TCP/IP-Modell

Das TCP/IP-Modell ist einfacher aufgebaut und hat nur 4 Schichten statt 7. Es fasst mehrere OSI-Schichten zusammen:

| TCP/IP-Modell | entspricht OSI-Schichten |
|---|---|
| Anwendung | 5, 6, 7 |
| Transport | 4 |
| Internet | 3 |
| Netzzugang | 1, 2 |

Das TCP/IP-Modell ist praxisorientierter und wurde tatsächlich für das echte Internet verwendet, während OSI eher als theoretisches Referenzmodell dient.

---

## Aufgabe: Vorteile eines Schichtenmodells

Ein Schichtenmodell wie OSI hat mehrere Vorteile für die Datenkommunikation:

- **Modularität:** Jede Schicht kann unabhängig entwickelt und verändert werden, ohne dass die anderen Schichten davon betroffen sind
- **Standardisierung:** Hersteller können Geräte und Software bauen, die miteinander kompatibel sind
- **Fehlersuche:** Probleme lassen sich gezielt einer Schicht zuordnen, was die Fehlerdiagnose erleichtert
- **Wiederverwendbarkeit:** Protokolle einer Schicht können mit verschiedenen Protokollen anderer Schichten kombiniert werden (z.B. HTTP über TCP über IP über Ethernet)

---

## Aufgabe: Portnummern

**a) Gesamter Portnummernbereich:**
Portnummern werden mit 16 Bit dargestellt. Der Bereich geht von **0 bis 65535** (2^16 = 65536 mögliche Werte).

**b) Well-Known Ports (System Ports):**
**0 bis 1023**

**c) Registered Ports (User Ports):**
**1024 bis 49151**

**d) Dynamic Ports (Private/Ephemeral Ports):**
**49152 bis 65535**

---

## Aufgabe: Probleme und Dienste zuordnen

| Problem | Dienst | Protokoll | Standard-Port(s) | Verschlüsselte Variante |
|---|---|---|---|---|
| Webseiten lassen sich nicht öffnen | HTTP | TCP | 80 | HTTPS (443) |
| E-Mails können empfangen, aber nicht versendet werden | SMTP | TCP | 25 / 587 | SMTPS (465/587 mit TLS) |
| Namensauflösung funktioniert nicht | DNS | UDP/TCP | 53 | DNS over TLS (853) |
| Netzwerkdrucker erhält keine IP-Adresse | DHCP | UDP | 67 (Server) / 68 (Client) | – (DHCP wird üblicherweise nicht verschlüsselt) |

---

## Aufgabe: Firewall-Ports zuordnen

| Port | Dienst | Warum benötigt? | Verschlüsselt? |
|---|---|---|---|
| 53 | DNS | Namensauflösung, damit z.B. Domainnamen in IP-Adressen übersetzt werden | Nein (Standard-DNS) |
| 80 | HTTP | Zugriff auf Webseiten | Nein |
| 123 | NTP | Zeitsynchronisation der Systeme im Netzwerk (wichtig für Logs, Zertifikate, Authentifizierung) | Nein |
| 443 | HTTPS | Sicherer Zugriff auf Webseiten | Ja |
| 587 | SMTP (Submission) | Versand von E-Mails durch Mailclients | Ja (mit STARTTLS) |
| 993 | IMAPS | Sicherer Abruf von E-Mails | Ja |
| 22 | SSH | Sichere Fernwartung von Servern/Geräten | Ja |

**Zusammenfassung verschlüsselt/unverschlüsselt:**
- **Unverschlüsselt:** DNS (53), HTTP (80), NTP (123)
- **Verschlüsselt:** SMTP mit STARTTLS (587), IMAPS (993), SSH (22), HTTPS (443)

---

## Praxisaufgabe Packet Tracer (Vorgehensweise)

**1. Kommunikation testen:**
Nutze den Befehl `ping <IP-Adresse>` in der Kommandozeile der PCs. Alternativ kannst du im Simulationsmodus auch ein "PDU" (Paket) manuell zwischen zwei Geräten verschicken.

**2. Simulation-Modus & Protokolle beobachten:**
Wechsle unten rechts von "Realtime" auf "Simulation". Beim Ping zwischen zwei PCs im selben Subnetz siehst du typischerweise **ARP** (zuerst, um die MAC-Adresse zu finden) und danach **ICMP** (der eigentliche Ping).

**3. ARP-Cache untersuchen:**
Reduziere die Geschwindigkeit im Simulationspanel und gib in der PC-Kommandozeile `arp -a` ein. Du siehst dann eine Tabelle mit IP-Adresse zu MAC-Adresse Zuordnungen. Die Erkenntnis: Geräte müssen erst die MAC-Adresse zur Ziel-IP herausfinden, bevor sie auf Layer 2 kommunizieren können.

**4. Ping im selben Subnetz:**
Wenn die Ziel-MAC noch nicht bekannt ist, wird zuerst ein ARP-Request/Reply durchgeführt, danach erscheint ein neuer Eintrag im ARP-Cache mit der IP und MAC des Zielgeräts.

**5. Ping in fremdes Subnetz:**
Hier läuft der ARP-Request nicht zum Zielgerät, sondern zum **Gateway (Router)**. Im ARP-Cache erscheint deshalb die MAC-Adresse des Routers, nicht die des eigentlichen Ziel-PCs, da der Router die Pakete weiterleitet.

**6. Webserver aufsetzen:**
- Webserver mit IP 192.168.2.13 an Switch21 anschliessen
- Im Webserver unter "Services" → HTTP aktivieren, HTTPS deaktivieren
- Client-Browser öffnen, URL `http://192.168.2.13/index.html` eingeben
- Im Simulationsmodus die Protokollfilter auf **ARP** und **HTTP** einschränken (Edit Filters Button)
- Du solltest sehen: zuerst ARP-Auflösung, dann HTTP-Request/Response
