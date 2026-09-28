# Challenge 6 - Netzwerkfall: Redundanz und Load Balancing

**Modul:** M129, Unterrichtsblock 6
**Datum:** 28.09.2026

Die Fallbeschreibung wurde mit dem im Auftrag vorgegebenen Prompt individuell generiert. Zuerst der Fall, danach meine Analyse.

```text
Du bist ein IT-Kunde und beschreibst mir eine realistische Unternehmenssituation.
Erstelle einen individuellen Netzwerkfall mit folgenden Eigenschaften:
- Unternehmensgrösse zwischen 10 und 500 Mitarbeitenden
- Branche zufällig wählen
- Anzahl Standorte zufällig wählen
- Internetanbindungen zufällig wählen
- Mögliche Risiken oder Schwachstellen einbauen
- Auslastungsprobleme oder Verfügbarkeitsanforderungen einbauen
Gib mir nur die Fallbeschreibung.
Gib mir keine Lösung.
```

---

# Fallbeschreibung

**Alpina Medtech AG**, Hersteller von Medizintechnik, 180 Mitarbeitende, drei Standorte:

| Standort | Mitarbeitende | Funktion | Internetanbindung |
|---|---|---|---|
| Zürich (Hauptsitz) | 95 | Verwaltung, Entwicklung, Rechenzentrum | 1 Gbit/s Glasfaser, Provider A |
| Winterthur | 60 | Produktion und Lager | 200/100 Mbit/s Kabelanschluss, Provider B |
| Lausanne | 25 | Vertrieb und Support | 100/40 Mbit/s DSL, Provider B |

**Aufbau:** Im Hauptsitz stehen alle Server: ERP, Fileserver mit den CAD-Daten, Datenbank und der Terminalserver. Winterthur und Lausanne sind über je einen IPsec-Tunnel über das Internet mit Zürich verbunden. Beide Aussenstandorte greifen für sämtliche Anwendungen auf Zürich zu, auch der Internetverkehr der Aussenstandorte wird über Zürich geführt (zentrale Firewall).

In Zürich stehen ein Edge-Router und eine Firewall, beide einfach vorhanden. Der Core-Switch im Serverraum ist ein einzelnes Gerät, die Verteil-Switches der Stockwerke sind je mit einer Leitung daran angeschlossen. Eine USV deckt die Server ab, die Netzwerkkomponenten der Stockwerke hängen direkt am Strom.

**Betrieb und Anforderungen:**

- In der Produktion in Winterthur laufen die Fertigungsaufträge über das ERP in Zürich. Steht die Verbindung, steht die Produktion. Gefordert sind 99,9 Prozent Verfügbarkeit während der Betriebszeiten von 05:00 bis 22:00 Uhr.
- Die Entwicklung in Zürich lädt täglich grosse CAD- und Bilddatensätze zu einem Cloud-Dienst hoch. Diese Uploads laufen tagsüber und lasten die Glasfaser regelmässig aus.
- Das nächtliche Cloud-Backup startet um 22:00 Uhr und dauert oft bis 07:00 Uhr, also in die Betriebszeit hinein.
- Lausanne meldet, dass Videokonferenzen mit Kunden regelmässig stocken, vor allem am Nachmittag.
- Der Edge-Router in Zürich ist sieben Jahre alt, ein Ersatzgerät ist nicht vorhanden. Die Lieferzeit für ein Ersatzgerät beträgt laut Lieferant zwei bis drei Wochen.
- Ein Ausfalltest der Infrastruktur wurde laut IT-Verantwortlichem noch nie durchgeführt.

---

# Meine Analyse

## 1. Ausgangslage

Die Firma betreibt ein klassisches Hub-and-Spoke-Netz: Alle Daten und alle Dienste liegen in Zürich, die beiden Aussenstandorte hängen über VPN-Tunnel daran, und sogar ihr Internetverkehr wird über Zürich geführt. Damit ist der Hauptsitz für alle drei Standorte der zentrale Punkt. Gleichzeitig sind dort alle wichtigen Komponenten nur einfach vorhanden, und die vorhandene Bandbreite wird durch Uploads und Backups zusätzlich belastet.

## 2. Risiken

**Wo erkenne ich einen Single Point of Failure?**

| Nr. | Single Point of Failure | Auswirkung bei Ausfall |
|---|---|---|
| 1 | Edge-Router Zürich (7 Jahre alt, kein Ersatz, 2-3 Wochen Lieferzeit) | alle drei Standorte ohne Internet und ohne VPN, Produktion in Winterthur steht |
| 2 | Firewall Zürich (einfach vorhanden) | gleich wie oben, da der ganze Verkehr über sie läuft |
| 3 | Internetanschluss Zürich (ein Anschluss, ein Provider) | beide VPN-Tunnel weg, alle Aussenstandorte ohne Zugriff auf ERP und Daten |
| 4 | Core-Switch im Serverraum | alle Server nicht mehr erreichbar, auch intern in Zürich |
| 5 | Je eine einzelne Leitung zu den Verteil-Switches | ein Stockwerk fällt aus |
| 6 | Standort Zürich als Ganzes (alle Server an einem Ort) | kompletter Betriebsausfall, auch das Backup liegt zwar in der Cloud, die Dienste aber nicht |
| 7 | Keine USV für die Stockwerk-Switches | bei Stromunterbruch stehen die Arbeitsplätze, obwohl die Server laufen |

**Weitere Schwachstellen, die keine SPOF sind, aber Probleme machen:**

- Die Glasfaser in Zürich wird tagsüber durch die CAD-Uploads ausgelastet, das trifft gleichzeitig die VPN-Tunnel der Aussenstandorte.
- Das Backup läuft bis 07:00 Uhr in die Betriebszeit hinein, die ab 05:00 Uhr beginnt.
- Der Internetverkehr von Lausanne macht einen Umweg über Zürich (Hairpin). Videokonferenzen leiden dadurch doppelt, einmal an der DSL-Leitung und einmal an der ausgelasteten Leitung in Zürich.
- Winterthur und Lausanne hängen beide beim gleichen Provider B, ein Ausfall bei diesem Provider trifft beide Aussenstandorte gleichzeitig.
- Die Anforderung von 99,9 Prozent erlaubt rund 8 Stunden 45 Minuten Ausfall pro Jahr. Mit einer Lieferzeit von zwei bis drei Wochen für einen Ersatzrouter ist diese Anforderung heute klar nicht erfüllbar.
- Es wurde nie ein Ausfalltest gemacht, es ist also unbekannt, ob im Ernstfall überhaupt etwas greift.

## 3. Vorschlag Redundanz

**Welche Komponenten sollten redundant ausgelegt werden?**

| Priorität | Massnahme | Begründung |
|---|---|---|
| 1 | Zweiter Internetanschluss in Zürich bei einem anderen Provider und mit anderer Technologie (z.B. Kabel oder 5G als Backup) | trifft gleich mehrere Risiken: Provider-Ausfall, Leitungsschaden, Überlast |
| 2 | Zweiter Edge-Router / zweite Firewall als HA-Paar mit HSRP oder VRRP | das älteste Gerät ist heute der grösste Einzelpunkt, ein Ersatz dauert Wochen |
| 3 | Zweiter Core-Switch im Serverraum, Verteil-Switches mit je zwei Uplinks (EtherChannel oder STP) | verhindert, dass ein Switch das ganze Rechenzentrum lahmlegt |
| 4 | Backup-VPN von Winterthur direkt zu einem zweiten Zugang, plus Mobilfunk-Backup für die Produktion | Produktion ist der kritischste Prozess |
| 5 | USV auch für die Stockwerkverteiler | Server laufen sonst weiter, ohne dass jemand sie erreicht |

Technisch im Routing umgesetzt wird das über eine **Floating Static Route**: Die Defaultroute über den Hauptanschluss bekommt die normale Distanz, die Route über den Backup-Anschluss eine schlechtere (zum Beispiel `ip route 0.0.0.0 0.0.0.0 <Backup-Gateway> 10`). Fällt der Hauptweg aus, wird automatisch die Ersatzroute aktiv.

## 4. Vorschlag Load Balancing

**Wo bringt Verteilung etwas?**

- **Zwei Internetanschlüsse in Zürich aktiv nutzen:** Der zweite Anschluss liegt nicht brach, sondern übernimmt gezielt Verkehr. Sinnvoll ist Policy Based Routing statt reinem ECMP, damit man steuern kann, was wohin geht:
  - CAD-Uploads und Cloud-Backup über den Zweitanschluss,
  - VPN-Tunnel der Standorte und Videokonferenzen über die Glasfaser.
- **Lokaler Internetausbruch an den Aussenstandorten:** Lausanne und Winterthur surfen direkt über ihren eigenen Anschluss, nur der Firmenverkehr geht durch den Tunnel. Das entlastet die Leitung in Zürich und behebt das Stocken der Videokonferenzen in Lausanne.
- **QoS:** Videokonferenzen und ERP-Verkehr priorisieren, Backups und Updates nachrangig behandeln.
- **Backupfenster anpassen und drosseln:** Start früher und Bandbreitenlimite, damit es nicht in die Betriebszeit ab 05:00 Uhr hineinläuft.
- **Bei den Switch-Uplinks:** EtherChannel bündelt zwei Leitungen zu einer logischen. Das bringt gleichzeitig Redundanz und mehr Bandbreite.

## 5. Empfehlung

**Redundanz, Load Balancing oder beides? Beides, aber in dieser Reihenfolge.**

**Sofort (kleiner Aufwand, grosse Wirkung):**

1. Zweiter Internetanschluss in Zürich bei einem anderen Provider, eingerichtet mit Floating Static Route als Failover und mit Policy Based Routing für Backups und Uploads.
2. Backupfenster verschieben und Bandbreite begrenzen, QoS für Videokonferenzen und ERP.
3. Lokaler Internetausbruch in Lausanne und Winterthur.

**Mittelfristig (Budget einplanen):**

4. Edge-Router und Firewall als HA-Paar mit VRRP, das alte Gerät ersetzen und als Ersatzteil behalten.
5. Zweiter Core-Switch und doppelte Uplinks zu den Verteil-Switches.
6. USV für die Stockwerkverteiler.

**Organisatorisch:**

7. Ausfalltests mindestens einmal pro Jahr, dokumentiert, inklusive Umschaltung auf den Backup-Anschluss.
8. Wartungsvertrag mit garantierter Reaktionszeit statt zwei bis drei Wochen Lieferzeit.

## 6. Begründung

**Welche Vorteile ergeben sich?**

Mit dem zweiten Anschluss und dem HA-Paar verschwinden die grössten Einzelpunkte. Die Produktion in Winterthur bleibt auch dann am ERP, wenn eine Leitung oder ein Gerät ausfällt, und die geforderten 99,9 Prozent werden realistisch erreichbar. Der lokale Internetausbruch und die Verteilung der Uploads entlasten die Glasfaser genau dann, wenn es eng wird, dadurch laufen die Videokonferenzen in Lausanne wieder stabil. Weil die zweite Leitung im Normalbetrieb aktiv genutzt wird, zahlt man sie nicht nur für den Notfall.

**Welche Kosten und Nachteile entstehen?**

Es entstehen laufende Kosten für den zweiten Anschluss und einmalige Kosten für das zweite Gerätepaar, den zweiten Core-Switch und die USV. Die Konfiguration wird komplexer: Failover, Policy Based Routing und QoS müssen sauber dokumentiert und gepflegt werden, sonst sucht man im Ernstfall lange. Ausserdem braucht der lokale Internetausbruch an den Aussenstandorten eine eigene Firewall-Policy, die Sicherheit wandert damit teilweise aus dem Hauptsitz an die Standorte. Und die beste Redundanz nützt nichts, wenn sie nicht getestet wird, darum gehören die jährlichen Ausfalltests zwingend dazu.

Nicht empfohlen habe ich einen zweiten Serverstandort mit Replikation. Das wäre die konsequenteste Lösung gegen Risiko Nummer 6, kostet aber deutlich mehr und geht über die Anforderung von 99,9 Prozent während der Betriebszeiten hinaus. Für ein Unternehmen dieser Grösse ist das Verhältnis von Aufwand und Nutzen bei den Punkten 1 bis 6 klar besser.
