# Challenge 4 – Spanning-Tree-Protokoll: Wer wird Root Bridge?

**Modul:** M129, Unterrichtsblock 4
**Datum:** 07.09.2026
**Tool:** Cisco Packet Tracer

---

## Ausgangslage

Eine Firma möchte ihr Netzwerk ausfallsicher machen und verbindet dazu drei Switches redundant im Dreieck: SW1 → SW2, SW2 → SW3, SW3 → SW1. Zusätzlich hängt an jedem Switch ein PC (PC1 an SW1, PC2 an SW2, PC3 an SW3). Ein Techniker stellt fest, dass trotz drei Kabelverbindungen nicht alle Ports Daten übertragen, das ist genau das Verhalten, das STP (Spanning Tree Protocol) verursacht, weil es eine der drei Verbindungen blockieren muss, um eine Schleife (Loop) zu verhindern.

---

## Individualisierte Prioritäten

Mit dem vorgegebenen KI-Prompt wurden mir folgende drei zufälligen, eindeutigen Prioritäten für meine drei Switches zugewiesen:

| Switch | Priorität |
|---|---|
| SW1 | 16384 |
| SW2 | 32768 |
| SW3 | 12288 |

Diese Werte verwende ich für die ganze Challenge.

---

## Auftrag 1: Prioritäten konfigurieren

Auf jedem Switch wird die Priorität für VLAN 1 gesetzt:

**SW1:**
```
enable
configure terminal
spanning-tree vlan 1 priority 16384
```

**SW2:**
```
enable
configure terminal
spanning-tree vlan 1 priority 32768
```

**SW3:**
```
enable
configure terminal
spanning-tree vlan 1 priority 12288
```

**Noch zu ergänzen:** Screenshot der Topologie in Packet Tracer (3 Switches im Dreieck + PC1/PC2/PC3), siehe Hinweis am Ende des Dokuments.

---

## Auftrag 2: Root Bridge bestimmen

Die Root Bridge ist immer der Switch mit der niedrigsten Bridge-ID. Die Bridge-ID setzt sich aus Priorität und MAC-Adresse zusammen, wobei die Priorität das Hauptkriterium ist. Da alle drei Prioritäten unterschiedlich sind, entscheidet hier die Priorität alleine, die MAC-Adresse spielt keine Rolle.

Vergleich: SW1 = 16384, SW2 = 32768, SW3 = 12288 → **SW3 hat die niedrigste Priorität und wird damit die Root Bridge.**

Alle Ports von SW3 bleiben im Zustand "Designated" (Forwarding), da die Root Bridge selber nie einen Root Port oder blockierten Port hat.

---

## Auftrag 3: Analyse (show spanning-tree)

Für die Berechnung gehe ich von der Standard-Kosten von 19 pro Link aus (Fast-Ethernet-Verbindung, Cisco-Default in Packet Tracer). Bei einem Dreieck aus drei gleich langen Strecken kostet der direkte Weg zur Root Bridge immer 19, der Umweg über den dritten Switch immer 38 (2 × 19).

**SW1** (Priorität 16384, nicht Root):
- Root Port: Port Richtung SW3 (direkter Weg, Kosten 19, günstiger als der Umweg über SW2 mit Kosten 38)

**SW2** (Priorität 32768, nicht Root):
- Root Port: Port Richtung SW3 (direkter Weg, Kosten 19, günstiger als der Umweg über SW1 mit Kosten 38)

**Verbleibende Verbindung SW1 – SW2:**
Beide Switches haben denselben Root-Path-Cost (19), es kommt also zum Gleichstand. Der Tie-Breaker ist dann die niedrigere Priorität: SW1 (16384) ist niedriger als SW2 (32768), darum gewinnt SW1 die Designated-Port-Wahl auf dieser Strecke.

- Designated Port auf dieser Strecke: SW1 (Port Richtung SW2)
- **Blocked Port: SW2 (Port Richtung SW1)**

Zusammengefasst:

| Switch | Root Port | Designated Ports | Blocked Port |
|---|---|---|---|
| SW3 (Root) | – | Richtung SW1, Richtung SW2 | – |
| SW1 | Richtung SW3 | Richtung SW2 | – |
| SW2 | Richtung SW3 | – | Richtung SW1 |

**Noch zu ergänzen:** Das sind meine Vorhersagen aufgrund von Priorität und Standard-Kosten. Muss ich noch mit dem echten `show spanning-tree` Befehl auf jedem Switch in Packet Tracer verifizieren und die Ausgabe hier einfügen (Text oder Screenshot, siehe Hinweis am Ende).

---

## Auftrag 4: Fehlersuche (Ping-Tests)

STP blockiert nur die eine redundante Verbindung, damit keine Schleife entsteht, es sorgt aber weiterhin dafür, dass alle Switches über den verbleibenden "Baum" erreichbar bleiben. Darum sollten alle drei Pings funktionieren, einer davon einfach über einen Umweg:

- **PC1 → PC2:** Der direkte Weg SW1–SW2 ist blockiert, der Ping läuft also über PC1 → SW1 → SW3 → SW2 → PC2. Sollte trotzdem funktionieren, nur mit einem Hop mehr.
- **PC2 → PC3:** Läuft direkt über SW2 → SW3 (Root Port von SW2), sollte problemlos funktionieren.
- **PC1 → PC3:** Läuft direkt über SW1 → SW3 (Root Port von SW1), sollte problemlos funktionieren.

**Noch zu ergänzen:** Ergebnisse der drei Pings aus Packet Tracer (Text-Output von `ping`, siehe Hinweis am Ende), inklusive IP-Adressierung der drei PCs (müssen im gleichen Subnetz liegen, damit das überhaupt geht).

---

## Auftrag 5: Root-Bridge-Wechsel

Um die Root Bridge zu wechseln, setze ich die Priorität von SW1 auf 4096 (niedrigster erlaubter Wert, tiefer als die aktuelle Root SW3 mit 12288):

```
enable
configure terminal
spanning-tree vlan 1 priority 4096
```

- **Alte Root Bridge:** SW3 (12288)
- **Neue Root Bridge:** SW1 (4096, jetzt der tiefste Wert von allen dreien)

**Auswirkungen auf STP:**
Das ganze Spanning Tree wird neu berechnet (Topology Change), das dauert bei klassischem STP typischerweise ca. 30-50 Sekunden bis zur Konvergenz (Listening/Learning-Phasen), bei RSTP deutlich schneller. Neu ergibt sich:

- SW2: Root Port neu Richtung SW1 (Kosten 19) statt Richtung SW3
- SW3: Root Port neu Richtung SW1 (Kosten 19) statt selber Root zu sein
- Verbleibende Strecke SW2–SW3: beide haben Root-Path-Cost 19, Gleichstand, Tie-Breaker Priorität: SW3 (12288) ist niedriger als SW2 (32768) → SW3 wird Designated, **SW2 wird neu auf dieser Strecke blockiert**

Der blockierte Port wandert also von "SW2 Richtung SW1" zu "SW2 Richtung SW3", SW2 hat in diesem Szenario mit der schlechtesten Priorität (32768) in beiden Fällen den Kürzeren gezogen.

---

## Auftrag 6: Vierter Switch

Zusätzlich zur bisherigen Topologie (nach dem Root-Wechsel aus Auftrag 5, SW1 ist Root) wird SW4 eingebaut: SW2 → SW4, SW4 → SW3. SW4 bekommt keine eigene Priorität zugewiesen und bleibt darum beim Cisco-Standardwert 32768.

**Vorhersage vor dem Test in Packet Tracer:**

SW4 hat zwei mögliche Wege zur Root Bridge SW1, beide mit Kosten 38 (2 Hops): über SW2 (SW4–SW2–SW1) oder über SW3 (SW4–SW3–SW1). Bei Gleichstand entscheidet die Priorität des Nachbarn: SW3 (12288) ist tiefer als SW2 (32768), darum wird der Port Richtung SW3 der Root Port von SW4.

Für die Strecke SW4–SW2 vergleicht man dann den Root-Path-Cost, den beide Seiten einbringen: SW2 hat 19 (direkt zur Root), SW4 hat 38 (über SW3). SW2 gewinnt also klar, ihr Port wird Designated, der Port von SW4 Richtung SW2 wird blockiert.

Damit ergeben sich im 4-Switch-Netz zwei blockierte Ports insgesamt:
- SW2, Port Richtung SW3 (aus Auftrag 5)
- SW4, Port Richtung SW2 (neu durch den vierten Switch)

Das passt auch rechnerisch: 4 Switches brauchen minimal 3 Verbindungen für einen schleifenfreien Baum, es sind aber 5 Verbindungen verbaut, also müssen 2 davon blockiert werden.

**Noch zu ergänzen:** Topologie-Screenshot mit SW4 und die tatsächliche Verifikation via `show spanning-tree` auf allen vier Switches (siehe Hinweis am Ende).

---

## Auftrag 7: Beurteilung der Aussage

*"Drei Kabel zwischen drei Switches sind besser als zwei, da so mehr Bandbreite zur Verfügung steht."*

Diese Aussage stimmt so nicht. Solange STP normal läuft (ohne EtherChannel/Link-Aggregation), wird eine der drei Verbindungen blockiert, um eine Schleife zu verhindern. Diese blockierte Leitung transportiert im Normalbetrieb gar keine Daten, sie bringt also keine zusätzliche Bandbreite. Ihr eigentlicher Nutzen ist Redundanz: Fällt eine der beiden aktiven Verbindungen aus, übernimmt die vorher blockierte Leitung automatisch, das Netzwerk bleibt also ausfallsicherer, aber nicht schneller. Für tatsächlich mehr Bandbreite zwischen zwei Switches bräuchte man EtherChannel, also mehrere Leitungen zwischen dem gleichen Switch-Paar, die STP zu einer einzigen logischen Verbindung bündelt.

---

## Fazit

Die Challenge zeigt gut, wie STP anhand von Priorität (Bridge-ID) und Pfadkosten automatisch entscheidet, welcher Switch Root Bridge wird und welche Verbindung blockiert werden muss, um Schleifen im redundanten Netz zu verhindern. Wichtig zu verstehen war vor allem, dass eine blockierte Leitung die Erreichbarkeit nicht einschränkt, sondern nur die Redundanz "auf Vorrat" hält, und dass bei Gleichstand bei den Pfadkosten am Schluss immer die Priorität entscheidet. Die rechnerischen Vorhersagen (Auftrag 3, 5 und 6) muss ich noch mit den echten Ausgaben aus Packet Tracer gegenprüfen, das trage ich nach, sobald ich die Topologie fertig aufgebaut habe.
