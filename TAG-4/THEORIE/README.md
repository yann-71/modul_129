# Theorie TAG-4: Redundanz im LAN und Spanning Tree Protocol

## Weshalb STP

Redundante Verbindungen zwischen Switches machen ein Netz ausfallsicher. Ohne Schutz entsteht dabei aber eine Schleife (Loop): Broadcasts kreisen endlos, die MAC-Tabellen werden instabil und das Netz bricht zusammen (Broadcast Storm). Auf Layer 2 gibt es kein TTL wie bei IP, das solche Pakete stoppen würde.

Das Spanning Tree Protocol (IEEE 802.1D) löst das, indem es aus dem vermaschten Netz einen schleifenfreien Baum berechnet und die überzähligen Verbindungen blockiert. Fällt eine aktive Leitung aus, wird eine blockierte Leitung automatisch wieder aktiviert.

## Root Bridge

Die Root Bridge ist die Wurzel des Baums. Gewählt wird der Switch mit der **tiefsten Bridge-ID**.

```
Bridge-ID = Priorität (16 Bit) + MAC-Adresse
```

- Die Priorität ist der Standardwert 32768 und wird in Schritten von 4096 gesetzt.
- Erst bei gleicher Priorität entscheidet die tiefere MAC-Adresse.
- Befehl: `spanning-tree vlan 1 priority 4096`

## Portrollen

| Rolle | Bedeutung |
|---|---|
| Root Port | Port mit dem günstigsten Weg zur Root Bridge, auf jedem Nicht-Root-Switch genau einer |
| Designated Port | pro Segment der Port, der die Root Bridge am günstigsten erreicht, leitet weiter |
| Blocked Port | nimmt keine Daten entgegen, hält die Leitung nur in Reserve |

Alle Ports der Root Bridge sind Designated Ports.

## Pfadkosten

| Bandbreite | Kosten (802.1D) |
|---|---|
| 10 Mbit/s | 100 |
| 100 Mbit/s | 19 |
| 1 Gbit/s | 4 |

Der Root Path Cost ist die Summe der Kosten bis zur Root Bridge. Bei Gleichstand entscheidet die tiefere Bridge-ID des Nachbarn, danach die tiefere Portnummer.

## Portzustände und Konvergenz

Klassisches STP durchläuft Blocking, Listening, Learning und Forwarding, was bis rund 50 Sekunden dauert. RSTP (802.1w) konvergiert deutlich schneller. Eine Topologieänderung (zum Beispiel ein ausgefallener Link oder eine neue Priorität) löst eine Neuberechnung aus.

## Wichtig zum Verständnis

- Ein blockierter Port bringt **keine** zusätzliche Bandbreite, er dient nur der Redundanz.
- Alle Geräte bleiben trotz blockiertem Port erreichbar, der Weg geht einfach über einen Umweg.
- Will man wirklich mehr Bandbreite zwischen zwei Switches, braucht es EtherChannel bzw. Link Aggregation. Dabei werden mehrere Leitungen zu einer logischen Verbindung gebündelt, die STP als einen Link sieht.
- Kontrolle auf dem Switch mit `show spanning-tree`.
