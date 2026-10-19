# Auftrag 1 - Fehlersuche in Packet Tracer (A1 bis A10)

**Modul:** M129, Unterrichtsblock 8
**Datum:** 26.10.2026

Die zehn Topologien A1.pkt bis A10.pkt liegen im Ordner [packettracer/](packettracer/). Die Printscreens zu den einzelnen Topologien kommen in den Ordner [screenshots/](screenshots/).

## Mein Vorgehen bei jeder Topologie

Ich bin bei allen zehn Topologien nach dem gleichen Schema vorgegangen (Musterlösung: "dynamisch" und "statisch" eingrenzen):

1. **Überblick:** Topologie ansehen, Link-LEDs prüfen (rot = keine Verbindung, orange = Spanning Tree blockiert).
2. **Statisch prüfen:** bei jedem Endgerät `ipconfig /all` (IP, Maske, Gateway, DNS, MAC), bei Routern und Switches `show running-config`, `show ip interface brief`, `show ip route`, `show mac-address-table`. Die Werte habe ich jeweils in eine Tabelle übertragen und verglichen: Passen IP und Maske zum Netz? Ist das Gateway im eigenen Netz? Gibt es Duplikate?
3. **Dynamisch prüfen:** `ping` zwischen allen Gruppen, bei Routern zusätzlich `tracert`. Wo es unklar war, im Simulationsmodus das Paket verfolgt.
4. **Beheben:** jeweils nur einen Fehler aufs Mal korrigieren.
5. **Verifizieren:** alle Pings erneut durchführen.

Da in einigen Topologien mehrere Fehler stecken, habe ich nach dem ersten gefundenen Fehler immer weitergesucht, bis alle Geräte vollständig geprüft waren.

## Übersicht der Fehler

| Topologie | Fehler | OSI-Schicht |
|---|---|---|
| A1 | PC-A2 im falschen Netz, Drucker LPR-A3 mit falscher Subnetzmaske | 3 |
| A2 | uneinheitliche Subnetzmasken (/16 bei Gruppe A, /24 bei Gruppe B) | 3 |
| A3 | PC-B2 und PC-B3 mit derselben MAC-Adresse, PC-B3 zusätzlich im falschen Netz | 2 und 3 |
| A4 | PC13 mit statischer IP im falschen Netz, Netzwerkkarte von PC26 ausgeschaltet, Loop Switch3 - Switch7, DHCP-Pool bis .255 | 1, 2, 3 |
| A5 | PC-B1 ohne Standardgateway | 3 |
| A6 | IP-Adressen der Router-Interfaces vertauscht | 3 |
| A7 | DHCP-Server im anderen Subnetz, kein DHCP-Relay | 3 / 7 |
| A8 | Switch2 Port Fa0/1 administrativ abgeschaltet | 1 / 2 |
| A9 | statische Routen fehlen, PC-C2 mit falschem Gateway | 3 |
| A10 | falscher DNS-Server bei PC-A2, PC-B1 und Web-Server | 7 |

---

## A1

**Topologie:** ein Switch (Switch-A) mit PC-A1, PC-A2, dem Server SRV-A4 und dem Drucker LPR-A3. Ein einziges Netz, kein Router.

**Untersuchung:**

| Gerät | IP | Maske | Bemerkung |
|---|---|---|---|
| PC-A1 | 10.0.1.10 | 255.255.255.0 | ok |
| PC-A2 | 10.0.0.11 | 255.255.255.0 | **falsches Netz** (10.0.0.0/24 statt 10.0.1.0/24) |
| SRV-A4 | 10.0.1.100 | 255.255.255.0 | ok |
| LPR-A3 | 10.0.1.101 | 255.255.0.0 | **falsche Maske** (/16 statt /24) |

- `ping 10.0.1.100` von PC-A2: `Request timed out`. PC-A2 hält das Ziel für ein fremdes Netz und hat kein Gateway.
- `ping 10.0.1.101` von PC-A1: funktioniert trotz falscher Maske, weil 10.0.1.10 auch im /16-Netz des Druckers liegt. Der Fehler ist also nur durch die statische Prüfung mit `ipconfig` zu finden, nicht mit `ping`.

**Behebung:**

- PC-A2: IP auf `10.0.1.11 / 255.255.255.0` ändern.
- LPR-A3: Maske auf `255.255.255.0` ändern.

**Verifikation:** Pings zwischen allen vier Geräten erfolgreich.

---

## A2

**Topologie:** Switch-A mit PC-A1 bis PC-A3, Switch-B mit PC-B1 bis PC-B3, die Switches mit einem Crossover-Kabel verbunden.

**Untersuchung:**

| Gerät | IP | Maske |
|---|---|---|
| PC-A1 / A2 / A3 | 172.16.1.11 / .12 / .13 | **255.255.0.0** |
| PC-B1 / B2 / B3 | 172.16.1.21 / .22 / .23 | 255.255.255.0 |

- Alle Pings funktionieren. Die A-Gruppe sieht alle Adressen 172.16.x.x als lokal, die B-Gruppe alle 172.16.1.x. Da alle sechs PCs in 172.16.1.x liegen, funktioniert es in beide Richtungen.

**Fehler:** Die Subnetzmasken sind uneinheitlich. Das widerspricht dem Subnetting-Konzept: Ein Netz muss überall dieselbe Maske haben. Spätestens wenn ein Gerät aus 172.16.2.x dazukommt, kommunizieren die A-PCs direkt statt über ein Gateway und es kommt zu schwer erklärbaren Fehlern.

**Behebung:** Bei PC-A1 bis PC-A3 die Maske auf `255.255.255.0` setzen, damit alle sechs PCs einheitlich im Netz 172.16.1.0/24 sind.

**Verifikation:** `ipconfig` auf allen PCs zeigt /24, alle Pings funktionieren weiterhin.

---

## A3

**Topologie:** wie A2, Netz 192.168.1.0/24.

**Untersuchung:**

| Gerät | IP | MAC |
|---|---|---|
| PC-A1 / A2 / A3 | 192.168.1.11 / .12 / .13 | verschieden |
| PC-B1 | 192.168.1.21 | 0006.2A48.E8D5 |
| PC-B2 | 192.168.1.22 | **0009.7CC1.0A70** |
| PC-B3 | **192.168.2.23** | **0009.7CC1.0A70** |

- `ipconfig /all` auf allen PCs: PC-B2 und PC-B3 haben dieselbe MAC-Adresse.
- `ping 192.168.1.22` von PC-A1: Antworten unzuverlässig. Auf Switch-B zeigt `show mac-address-table` die MAC 0009.7CC1.0A70 abwechselnd an Fa0/3 und Fa0/4, je nachdem, welcher PC zuletzt gesendet hat (MAC-Flapping). Frames für PC-B2 landen dadurch zeitweise bei PC-B3.
- PC-B3 ist zusätzlich nicht erreichbar, weil er im Netz 192.168.2.0/24 ist. Dieser zweite Fehler steht nicht in der Musterlösung, ich habe ihn bei der statischen Prüfung gefunden.

**Behebung:**

- PC-B3: Config > FastEthernet0 > MAC-Adresse ändern, z. B. auf `0009.7CC1.0A71`.
- PC-B3: IP auf `192.168.1.23` ändern.
- Auf Switch-B `clear mac-address-table dynamic`, damit die falschen Einträge verschwinden.

**Verifikation:** `show mac-address-table` zeigt jede MAC nur noch an einem Port, alle Pings funktionieren zuverlässig.

---

## A4

**Topologie:** grösseres Netz mit 16 Switches (Switch0 bis Switch15), 27 PCs (PC0 bis PC26), PC-Admin und einem DHCP-Server (10.0.0.3). Die PCs beziehen ihre Adresse per DHCP.

**Untersuchung:**

1. **Alle PCs mit `ipconfig` geprüft.** Fast alle erhalten eine Adresse aus 10.0.0.100 bis 10.0.0.255. Zwei Ausreisser:
   - **PC13:** statische IP `172.22.43.123 / 255.255.0.0`, kein Gateway, kein DHCP. Er kann niemanden erreichen. (In der Musterlösung heisst dieser PC "PC12", in meiner Datei ist es PC13 an Switch7 Fa0/5.)
   - **PC26:** zeigt `169.254.23.81` (APIPA). In Config > FastEthernet0 ist der Port Status **Off**, die Netzwerkkarte ist ausgeschaltet. Die Verbindung zu Switch15 ist rot.
2. **Link-LEDs geprüft:** Auf einer Leitung ist ein Port orange (blockiert). Ich habe die Verbindungen der Switches aufgezeichnet:
   `Switch0 - Switch5 - Switch4 - Switch12 - Switch7 - Switch3 - Switch2 - Switch0`
   Das ist ein Ring, also ein **Layer-2-Loop**. Die Verbindung Switch3 Fa0/2 - Switch7 Fa0/1 ist redundant. Spanning Tree verhindert zwar einen Broadcast-Sturm, indem ein Port blockiert wird (`show spanning-tree` zeigt einen Port mit Rolle Altn und Status BLK), aber die Verbindung ist laut Topologie nicht vorgesehen.
3. **DHCP-Server geprüft:** Pool `serverPool` 10.0.0.0/24 von 10.0.0.100 bis **10.0.0.255**. 10.0.0.255 ist die Broadcastadresse und darf nicht vergeben werden. Gateway 10.0.0.1 und DNS 10.0.0.2 gibt es in der Topologie nicht, für dieses Netz ohne Router spielt das aber keine Rolle.

**Behebung:**

- PC13: Config > FastEthernet0 > IP Configuration auf **DHCP** umstellen.
- PC26: Config > FastEthernet0 > Port Status **On**, danach DHCP neu anfordern.
- Verbindung Switch3 Fa0/2 - Switch7 Fa0/1 entfernen.
- DHCP-Server: Ende des Pools auf `10.0.0.254` setzen (bzw. Maximum Users auf 155).

**Verifikation:** PC13 und PC26 erhalten eine Adresse aus dem Pool, alle Links sind grün, `show spanning-tree` zeigt keine blockierten Ports mehr, Pings von PC-Admin zu einzelnen PCs aus allen Ästen funktionieren.

---

## A5

**Topologie:** Router1 mit zwei Netzen. Netz A 10.0.1.0/24 (Switch-A, PC-A1), Netz B 10.0.2.0/24 (Switch-B, PC-B1).

**Untersuchung:**

| Gerät | IP | Gateway |
|---|---|---|
| Router1 Gi0/0 | 10.0.1.1/24 | - |
| Router1 Gi0/1 | 10.0.2.1/24 | - |
| PC-A1 | 10.0.1.11/24 | 10.0.1.1 |
| PC-B1 | 10.0.2.11/24 | **leer** |

- `ping 10.0.2.1` von PC-A1: ok, `ping 10.0.2.11`: `Request timed out`.
- `ping 10.0.2.1` von PC-B1: ok (eigenes Netz), `ping 10.0.1.11`: kein Erfolg.
- Im Simulationsmodus sieht man: Der Echo Request kommt bei PC-B1 an, aber PC-B1 kann die Antwort nicht senden, weil er für fremde Netze kein Gateway hat.

**Behebung:** PC-B1 Standardgateway `10.0.2.1` eintragen.

**Verifikation:** Pings in beide Richtungen erfolgreich, `tracert 10.0.2.11` von PC-A1 zeigt `10.0.1.1` und dann das Ziel.

---

## A6

**Topologie:** wie A5, mit je drei PCs pro Netz. Netz A 10.0.1.0/24 an Switch-A, Netz B 10.0.2.0/24 an Switch-B. Die PCs haben korrekte Gateways (10.0.1.1 bzw. 10.0.2.1).

**Untersuchung:**

- `ping 10.0.1.1` von PC-A1: `Request timed out`, obwohl die PCs untereinander pingen können. Also liegt das Problem beim Router.
- `show ip interface brief` auf Router1:

  ```text
  GigabitEthernet0/0   10.0.2.1   up   up
  GigabitEthernet0/1   10.0.1.1   up   up
  ```

- `show cdp neighbors` bzw. die Verkabelung zeigt: **Gi0/0 hängt an Switch-A (Netz A)**, Gi0/1 an Switch-B (Netz B). Die IP-Adressen der beiden Router-Interfaces sind also vertauscht.

**Behebung:**

```text
Router(config)# interface gi0/0
Router(config-if)# ip address 10.0.1.1 255.255.255.0
Router(config-if)# interface gi0/1
Router(config-if)# ip address 10.0.2.1 255.255.255.0
```

(Alternativ die beiden Kabel am Router tauschen.)

**Verifikation:** Alle PCs erreichen ihr Gateway und die PCs im anderen Netz.

---

## A7

**Topologie:** Router-1 mit Netz A 10.0.1.0/24 (Switch-1 und Switch-2, PC-A1 bis PC-A6, DHCP-Server 10.0.1.2, PC-ADMIN 10.0.1.3) und Netz B 10.0.2.0/24 (Switch-3, PC-B1 bis PC-B3). Alle PCs ausser PC-ADMIN beziehen ihre Adresse per DHCP.

**Untersuchung:**

- `ipconfig` auf PC-A1 bis PC-A6: Adressen aus 10.0.1.100 bis 10.0.1.199, ok.
- `ipconfig` auf PC-B1 bis PC-B3: `169.254.x.x` (APIPA), sie haben **keine Adresse vom DHCP-Server erhalten**.
- `ipconfig /renew` auf PC-B1: `DHCP request failed`.
- DHCP-Server: nur ein Pool für 10.0.1.0/24.
- Router-1 `show running-config`: auf Gi0/1 kein `ip helper-address`.

**Ursache:** Der DHCP-Discover ist ein Broadcast. Router-1 leitet Broadcasts nicht weiter, deshalb erreicht die Anfrage der B-PCs den DHCP-Server nie. Gemäss Topologie soll der Server aber beide Netze bedienen. Es fehlt ein DHCP-Relay-Agent und ein Pool für Netz B.

**Behebung:**

```text
Router(config)# interface gi0/1
Router(config-if)# ip helper-address 10.0.1.2
```

Auf dem DHCP-Server einen zweiten Pool anlegen:

| Pool | Netz | Gateway | Start | Max. Users |
|---|---|---|---|---|
| poolB | 10.0.2.0/24 | 10.0.2.1 | 10.0.2.100 | 100 |

Danach auf PC-B1 bis PC-B3 DHCP neu anfordern.

**Verifikation:** PC-B1 bis PC-B3 erhalten 10.0.2.100 und folgende mit Gateway 10.0.2.1, Pings zwischen Netz A und B funktionieren.

---

## A8

**Topologie:** drei Switches in Reihe (Switch1 - Switch2 - Switch3), PC1 und PC2 an Switch1, PC3 an Switch2, PC4 an Switch3. Alle im Netz 192.168.1.0/24, MAC-Adressen manuell gesetzt (0000.0000.0001 bis 0000.0000.0004).

**Untersuchung:**

- `ping 192.168.1.2` von PC1: ok, `ping 192.168.1.3` und `.4`: `Request timed out`.
- Die Verbindung Switch1 - Switch2 hat rote LEDs.
- Switch2 `show ip interface brief`: `FastEthernet0/1   administratively down   down`. In der Running-Config steht bei `interface FastEthernet0/1` der Befehl `shutdown`.
- `show mac-address-table` auf Switch1 zeigt nur die MACs von PC1 und PC2, auf Switch2 nur PC3 und PC4. Die SAT bestätigt also, dass über die Leitung Switch1 - Switch2 nichts gelernt wird.

**Behebung:**

```text
Switch(config)# interface fa0/1
Switch(config-if)# no shutdown
```

**Verifikation:** Link grün, `show mac-address-table` auf Switch1 zeigt jetzt auch 0000.0000.0003 und 0000.0000.0004 am Port Fa0/1, alle Pings erfolgreich. Mit `clear mac-address-table dynamic` kann die SAT bei Bedarf wieder geleert werden.

---

## A9

**Topologie:** drei Netze, zwei Router. Netz A 10.0.1.0/24 (PC-A1, PC-A2), Netz B 10.0.2.0/24 (PC-B1, PC-B2) als Transfernetz zwischen Router-AB (10.0.2.1) und Router-BC (10.0.2.2), Netz C 10.0.3.0/24 (PC-C1, PC-C2).

**Untersuchung:**

- Pings A - B und B - C funktionieren, A - C nicht.
- `tracert 10.0.3.11` von PC-A1: erster Hop 10.0.1.1, dann `Destination host unreachable`.
- `show ip route` auf Router-AB: nur die direkt verbundenen Netze 10.0.1.0 und 10.0.2.0, **keine Route nach 10.0.3.0/24**.
- `show ip route` auf Router-BC: nur 10.0.2.0 und 10.0.3.0, **keine Route nach 10.0.1.0/24**.
- `ipconfig` auf PC-C2: Gateway **10.0.1.3**. Diese Adresse liegt nicht im Netz C, PC-C2 kann damit keine fremden Netze erreichen. Dieser zweite Fehler steht nicht in der Musterlösung.

**Behebung:**

```text
Router-AB(config)# ip route 10.0.3.0 255.255.255.0 10.0.2.2
Router-BC(config)# ip route 10.0.1.0 255.255.255.0 10.0.2.1
```

PC-C2: Gateway auf `10.0.3.1` ändern.

**Verifikation:** `tracert 10.0.3.11` von PC-A1 zeigt `10.0.1.1 - 10.0.2.2 - 10.0.3.11`. Alle PCs erreichen sich gegenseitig. Die PCs im Netz B verwenden Router-AB als Gateway. Pakete nach Netz C schickt Router-AB über dasselbe Interface an Router-BC weiter, das funktioniert ebenfalls.

---

## A10

**Topologie:** Router1 mit Netz A 10.0.1.0/24 (PC-A1, PC-A2) und Netz B 10.0.2.0/24 (DNS-Server 10.0.2.2, Web-Server 10.0.2.3, PC-B1). Der DNS-Server hat einen A-Record `soltec.local -> 10.0.2.3`.

**Untersuchung:**

- Webbrowser auf PC-A2 mit `http://soltec.local`: `Host Name Unresolved`.
- Webbrowser mit `http://10.0.2.3`: Die Soltec-Webseite erscheint. Netz und Webserver sind also in Ordnung, das Problem liegt bei der Namensauflösung (Schicht 7).
- `ipconfig /all`:

| Gerät | DNS-Server | Bemerkung |
|---|---|---|
| PC-A1 | 10.0.2.2 | ok, `soltec.local` funktioniert |
| PC-A2 | **10.0.2.3** | falsch, das ist der Web-Server |
| PC-B1 | **10.0.2.3** | falsch |
| Web-Server | **10.0.2.3** | falsch (zeigt auf sich selbst) |

- `nslookup soltec.local` auf PC-A2: keine Antwort vom Server 10.0.2.3, da dort kein DNS-Dienst läuft.

In der Musterlösung steht "bei allen PCs". In meiner Datei war PC-A1 bereits korrekt, falsch waren PC-A2, PC-B1 und der Web-Server.

**Behebung:** Bei PC-A2, PC-B1 und dem Web-Server den DNS-Server auf `10.0.2.2` setzen.

**Verifikation:** `nslookup soltec.local` liefert 10.0.2.3, `http://soltec.local` öffnet auf allen PCs die Soltec-Webseite. Dort steht auch das Passwort für die Musterlösung.

---

## Zusatzfragen

### Gibt es eine Möglichkeit, einen Layer-2-Loop mittels Netzwerkanalyse (z. B. SNMP) festzustellen?

Ja. Ein Loop ohne Spanning Tree führt zu einem Broadcast-Sturm, weil Ethernet-Frames im Gegensatz zu IP-Paketen kein TTL haben und endlos kreisen. Das lässt sich auf mehreren Wegen erkennen:

- **SNMP-Monitoring** (z. B. mit PRTG oder LibreNMS): Die Switches liefern über SNMP ihre Interface-Zähler. Bei einem Loop steigen die Broadcast- und Multicast-Zähler auf mehreren Ports gleichzeitig massiv an, die Auslastung geht gegen 100 Prozent und die CPU-Last der Switches steigt. Ein Monitoring-System kann dafür Schwellwerte und Alarme setzen.
- **SNMP-Traps:** Switches melden Spanning-Tree-Topologieänderungen (z. B. neuer Root, Topology Change) aktiv per Trap. Häufige Topologieänderungen deuten auf ein Problem hin.
- **Syslog:** Meldungen über MAC-Flapping, also dieselbe MAC-Adresse wird abwechselnd an verschiedenen Ports gelernt.
- **Wireshark:** Man sieht dieselben Broadcast-Frames (z. B. ARP-Requests) in sehr kurzer Zeit immer wieder.
- **Packet Tracer:** Im Simulationsmodus sieht man, wie ein Broadcast im Kreis weitergeleitet wird. `show spanning-tree` zeigt, welcher Port blockiert ist.

Vorbeugend helfen Spanning Tree (RSTP), BPDU Guard an Endgeräte-Ports und Storm Control.

### Wie könnte man einen DHCP-Betrieb ermöglichen, wo DHCP-Client und DHCP-Server in verschiedenen Subnetzen liegen?

Mit einem **DHCP-Relay-Agent**, wie in A7 umgesetzt. Auf dem Router-Interface, an dem die Clients hängen, wird die Adresse des DHCP-Servers eingetragen:

```text
interface GigabitEthernet0/1
 ip helper-address 10.0.1.2
```

Der Router empfängt den DHCP-Broadcast des Clients und leitet ihn als Unicast an den DHCP-Server weiter. Dabei trägt er im Feld `giaddr` die IP seines Interfaces ein (z. B. 10.0.2.1). Daran erkennt der Server, für welches Subnetz die Anfrage ist, und vergibt eine Adresse aus dem passenden Pool. Deshalb braucht der Server für jedes Subnetz einen eigenen Pool mit dem richtigen Gateway.

Alternativen wären ein eigener DHCP-Server pro Subnetz oder der Router selbst als DHCP-Server (`ip dhcp pool`). Bei vielen Subnetzen ist ein zentraler Server mit Relay aber einfacher zu verwalten.
