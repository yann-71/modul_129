# Theorie TAG-2: Schichtenmodelle, Protokolle und ARP

## Weshalb ein Schichtenmodell

Früher hatte jeder Hersteller eigene, proprietäre Protokolle, die nicht zusammengearbeitet haben. Das OSI-Modell der ISO schafft einen gemeinsamen Standard und teilt die Kommunikation in Schichten auf. Jede Schicht hat eine klar definierte Aufgabe und kann unabhängig weiterentwickelt werden. Das bringt Modularität, Standardisierung und eine gezieltere Fehlersuche, weil man ein Problem einer Schicht zuordnen kann.

## Die 7 Schichten

| Schicht | Name | Aufgabe | Beispiele |
|---|---|---|---|
| 7 | Anwendung | Schnittstelle zur Anwendung | HTTP, DNS, SMTP |
| 6 | Darstellung | Format, Verschlüsselung, Kompression | TLS, ASCII |
| 5 | Sitzung | Auf- und Abbau von Sitzungen | NetBIOS |
| 4 | Transport | Segmente, Ports, Zuverlässigkeit | TCP, UDP |
| 3 | Vermittlung | logische Adressierung, Routing | IP, ICMP |
| 2 | Sicherung | MAC-Adressen, Frames, Fehlererkennung | Ethernet, ARP |
| 1 | Bitübertragung | Bits auf dem Medium | Kupfer, Glasfaser, Funk |

Geräte nach Schicht: Hub und Access Point auf Layer 1/2, Switch auf Layer 2, Router auf Layer 3.

## OSI gegenüber TCP/IP

| TCP/IP-Modell | entspricht OSI |
|---|---|
| Anwendung | 5, 6, 7 |
| Transport | 4 |
| Internet | 3 |
| Netzzugang | 1, 2 |

Das TCP/IP-Modell ist praxisorientiert und wird real verwendet, OSI dient vor allem als Referenz- und Lernmodell.

## Ports

| Bereich | Nummern | Verwendung |
|---|---|---|
| Well-Known Ports | 0 - 1023 | Standarddienste wie HTTP (80), HTTPS (443), DNS (53), SSH (22) |
| Registered Ports | 1024 - 49151 | registrierte Anwendungen |
| Dynamic Ports | 49152 - 65535 | temporäre Quellports der Clients |

Portnummern sind 16 Bit gross, darum gehen sie von 0 bis 65535.

## ARP

ARP (Address Resolution Protocol) löst im lokalen Netz eine bekannte IP-Adresse in die dazugehörige MAC-Adresse auf.

1. Der Sender schaut zuerst im ARP-Cache nach (`arp -a`).
2. Ist die MAC unbekannt, schickt er einen ARP-Request als Broadcast: "Who has 10.0.0.5? Tell 10.0.0.1".
3. Das Zielgerät antwortet mit einem ARP-Reply direkt an den Absender: "10.0.0.5 is at aa:bb:cc:...".
4. Der Eintrag wird im Cache gespeichert und verfällt nach einigen Minuten wieder.

Liegt das Ziel in einem fremden Subnetz, fragt der PC nicht nach der MAC des Ziels, sondern nach der MAC seines Standardgateways. Im Cache steht dann die Adresse des Routers.

**ARP-Spoofing:** ARP kennt keine Authentifizierung. Ein Angreifer kann gefälschte Replies senden und sich so als Gateway ausgeben (Man-in-the-Middle). Schutz bieten Dynamic ARP Inspection, Port Security, statische Einträge für kritische Geräte, VLANs und verschlüsselte Protokolle.

## Wireshark

Mit Wireshark kann man den Datenverkehr auf einem Interface mitschneiden und nach Protokollen filtern (zum Beispiel `arp`, `icmp`, `http`). In der Paketdetailansicht sieht man die Kapselung: Ethernet-Header (Layer 2), IP-Header (Layer 3), TCP/UDP-Header (Layer 4) und die Nutzdaten. In Packet Tracer macht der Simulationsmodus das Gleiche in langsam und vereinfacht.
