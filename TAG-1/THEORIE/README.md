# Theorie TAG-1: Netzwerkgrundlagen und Netzwerkdatenverkehr

## Client, Server, LAN, WLAN und WAN

- **Client:** Gerät oder Programm, das einen Dienst anfordert, zum Beispiel ein PC, der eine Webseite abruft.
- **Server:** Gerät oder Programm, das Dienste bereitstellt, zum Beispiel ein Web- oder Fileserver.
- **LAN:** lokales Netzwerk, meist auf ein Gebäude oder Gelände begrenzt, kabelgebunden.
- **WLAN:** gleiches Prinzip wie ein LAN, einfach drahtlos über Funk.
- **WAN:** verbindet mehrere LANs über grosse Distanzen, das Internet ist das grösste WAN.

## Adressierung

| | Logische Adresse | Physikalische Adresse |
|---|---|---|
| Was | IP-Adresse (32 Bit bei IPv4) | MAC-Adresse (48 Bit) |
| Vergabe | per Software, DHCP oder manuell | fest in der Netzwerkkarte |
| Gültigkeit | im ganzen Netzwerk / Internet | im lokalen Netz (Layer 2) |
| OSI-Layer | 3 | 2 |

Die Subnetzmaske legt fest, welcher Teil der IP-Adresse die Netz-ID und welcher die Host-ID ist. Die Netzwerkadresse hat alle Host-Bits auf 0, die Broadcastadresse alle Host-Bits auf 1. Beide können keinem Gerät zugewiesen werden.

Private Adressbereiche (nicht im Internet routbar, dürfen mehrfach vorkommen):

| Klasse | Bereich |
|---|---|
| A | 10.0.0.0 - 10.255.255.255 |
| B | 172.16.0.0 - 172.31.255.255 |
| C | 192.168.0.0 - 192.168.255.255 |

Spezielle Adressen: 127.0.0.1 ist die Loopback-Adresse (das Gerät selbst), 169.254.x.x ist der APIPA-Bereich, den Windows vergibt, wenn kein DHCP-Server antwortet.

## Aufgaben von Switch und Router

- **Switch (Layer 2):** verbindet Geräte innerhalb eines Subnetzes und leitet Frames anhand der Ziel-MAC gezielt an den richtigen Port weiter. Jeder Port ist eine eigene Kollisionsdomäne, Broadcasts werden aber weitergeleitet.
- **Router (Layer 3):** verbindet Subnetze und entscheidet anhand der Ziel-IP-Adresse, wohin ein Paket weitergeleitet wird. Ein Router trennt Broadcastdomänen.
- **Hub:** veraltet, gibt alles an allen Ports aus und erzeugt eine einzige grosse Kollisionsdomäne.

## Wichtige Windows-Befehle

| Befehl | Zweck |
|---|---|
| `ipconfig /all` | alle Netzwerkeinstellungen inkl. MAC, DHCP, Gateway, DNS |
| `ipconfig /release` + `/renew` | DHCP-Adresse freigeben und neu beziehen |
| `arp -a` | ARP-Cache anzeigen (IP zu MAC) |
| `ping` | Erreichbarkeit prüfen |
| `hostname` | Rechnername anzeigen |
| `ncpa.cpl` | Netzwerkverbindungen öffnen |

## Netzwerkdatenverkehr abschätzen

Beim Abschätzen geht es darum, den theoretischen Bedarf mit der vorhandenen Bandbreite zu vergleichen und so Engstellen (Flaschenhälse) zu finden.

- Bedarf einer Gruppe = Anzahl Geräte × Bandbreite pro Gerät, wobei immer die tatsächliche Engstelle zählt.
- Ein Uplink ist überlastet, wenn die Summe der dahinterliegenden Geräte grösser ist als seine Bandbreite.
- Umrechnung: 1 Byte = 8 Bit, also 2,5 GByte = 20 Gbit.
- Übertragungszeit = Datenmenge ÷ Bandbreite der engsten gemeinsam genutzten Leitung.

Solche Rechnungen sind Worst-Case-Betrachtungen. In der Praxis senden selten alle Geräte gleichzeitig mit voller Geschwindigkeit, trotzdem zeigt die Rechnung, wo eine Aufrüstung am meisten bringt (zum Beispiel Link Aggregation oder ein schnellerer Uplink).
