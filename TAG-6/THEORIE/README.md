# Theorie TAG-6: Redundanz und Load Balancing

## Die zwei Begriffe

| | Redundanz | Load Balancing |
|---|---|---|
| Ziel | hohe Verfügbarkeit | hohe Leistung |
| Frage | Was passiert bei einem Ausfall? | Wie verteilen wir den Verkehr? |
| Bei Ausfall | die Alternative übernimmt | kein primäres Ziel |
| Ressourcen | zweite Leitung wartet oft im Reservebetrieb | mehrere Leitungen werden gleichzeitig genutzt |

**Merksatz:** Redundanz sorgt dafür, dass das Netzwerk bei einem Ausfall weiterläuft. Load Balancing sorgt dafür, dass die vorhandenen Verbindungen effizient genutzt werden.

Die beiden schliessen sich nicht aus. Zwei aktive Leitungen mit Load Balancing bringen automatisch auch Redundanz, weil beim Ausfall einer Leitung der Verkehr über die andere weiterläuft, einfach mit weniger Bandbreite.

## Single Point of Failure

Ein Single Point of Failure (SPOF) ist eine Komponente, bei deren Ausfall ein ganzer Dienst oder Standort steht. Typische SPOF in einem KMU-Netz:

- ein einziger Edge-Router oder eine einzige Firewall
- ein einziger Internetanschluss oder ein einziger Provider
- ein einziger Uplink zwischen Core- und Verteil-Switch
- ein einziger Server für einen kritischen Dienst
- die Stromversorgung ohne USV

Wichtig ist, dass zwei Leitungen beim gleichen Provider im selben Kabelkanal nur auf dem Papier redundant sind. Echte Redundanz braucht unterschiedliche Wege und idealerweise unterschiedliche Technologien, zum Beispiel Glasfaser plus Mobilfunk.

## Umsetzung im Routing

| Mittel | Wirkung |
|---|---|
| Zwei statische Routen mit gleicher Metrik | der Router verteilt den Verkehr auf beide Wege (Equal Cost Multipath) |
| Floating Static Route (`ip route ... 10`) | Ersatzroute mit schlechterer Distanz, wird erst aktiv, wenn die Hauptroute wegfällt |
| Dynamisches Routingprotokoll (RIP, OSPF) | erkennt den Ausfall selbst und berechnet neue Wege |
| HSRP / VRRP | zwei Router teilen sich eine virtuelle Gateway-IP, der zweite übernimmt bei Ausfall |
| EtherChannel / LACP | mehrere Leitungen zwischen zwei Geräten werden zu einer logischen gebündelt |
| Mehrere Internetanschlüsse (Multihoming) | Ausfallschutz und mehr Bandbreite, oft mit Policy Based Routing verteilt |

Bei ECMP verteilt der Router pro Verbindung (per Flow) und nicht pro Paket. Das ist gewollt, weil sonst die Pakete einer Sitzung in falscher Reihenfolge ankommen könnten.

## Verfügbarkeit

| Verfügbarkeit | erlaubte Ausfallzeit pro Jahr |
|---|---|
| 99 % | ca. 3 Tage 15 Stunden |
| 99,9 % | ca. 8 Stunden 45 Minuten |
| 99,99 % | ca. 52 Minuten |

Je höher die geforderte Verfügbarkeit, desto teurer wird es. Darum lohnt es sich, zuerst zu klären, welche Dienste wirklich kritisch sind, statt alles doppelt zu bauen.

## Kosten und Nachteile

Redundanz und Load Balancing kosten Geld (zweite Geräte, zweiter Anschluss, Wartungsverträge) und erhöhen die Komplexität. Eine komplexere Konfiguration ist fehleranfälliger und muss dokumentiert und regelmässig getestet werden. Eine Redundanz, die nie getestet wurde, funktioniert im Ernstfall oft nicht.
