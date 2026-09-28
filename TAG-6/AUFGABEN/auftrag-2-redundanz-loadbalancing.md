# Auftrag 2: Redundanz und Load Balancing

## Frage 1: Die Internetverbindung ist zu Spitzenzeiten überlastet. Wie hilft Load Balancing?

Load Balancing verteilt den Verkehr auf mehrere Leitungen, statt alles über eine einzige zu schicken. Nimmt die Firma einen zweiten Internetanschluss dazu und verteilt der Router den Verkehr auf beide, steht in der Summe mehr Bandbreite zur Verfügung und die Spitzen werden abgefedert.

Konkret gibt es mehrere Möglichkeiten:

- **Gleichmässige Verteilung (ECMP):** Der Router hat zwei Defaultrouten mit gleicher Metrik und verteilt die Verbindungen auf beide Leitungen. Die Verteilung geschieht pro Verbindung (per Flow), damit die Pakete einer Sitzung in der richtigen Reihenfolge ankommen.
- **Gezielte Verteilung (Policy Based Routing):** Man legt fest, welcher Verkehr über welche Leitung geht, zum Beispiel Backups und Updates über die günstige Leitung und Videokonferenzen über die schnelle, stabile Leitung.
- **Priorisierung (QoS):** Zusätzlich kann man wichtigen Verkehr wie Telefonie bevorzugt behandeln, damit er auch bei voller Leitung nicht leidet.

Wichtig ist die Erwartungshaltung: Load Balancing macht eine einzelne Übertragung nicht schneller, sondern verteilt viele gleichzeitige Verbindungen. Ein einzelner Download läuft weiterhin über eine Leitung. Und als Nebeneffekt bringt die zweite Leitung auch Redundanz.

## Frage 2: Wie erhöht Redundanz die Verfügbarkeit, und welche Risiken werden reduziert?

Redundanz bedeutet, dass eine kritische Komponente mehrfach vorhanden ist. Fällt eine aus, übernimmt die andere, idealerweise automatisch und ohne dass die Benutzer etwas merken. Dadurch sinkt die Ausfallzeit pro Jahr und die Verfügbarkeit steigt.

Reduzierte Risiken:

| Risiko | Redundante Auslegung |
|---|---|
| Defekter Router oder defekte Firewall | zweites Gerät mit HSRP oder VRRP, beide teilen sich eine virtuelle Gateway-IP |
| Ausfall des Providers oder Leitungsschaden (Bagger) | zweiter Anschluss bei einem anderen Provider und auf einem anderen Weg |
| Defektes Kabel oder defekter Port zwischen Switches | zweite Verbindung, abgesichert über STP oder gebündelt mit EtherChannel |
| Ausfall eines Servers | Cluster oder zweiter Server mit Replikation |
| Stromausfall | USV und im Rechenzentrum Notstrom |

Damit das wirklich funktioniert, muss die Umschaltung getestet werden. Ausserdem bringt eine zweite Leitung wenig, wenn beide Leitungen beim gleichen Provider im gleichen Kabelkanal liegen, dann trifft ein Bagger beide gleichzeitig. Redundanz kostet zusätzlich Geld und macht die Konfiguration komplexer, darum lohnt sie sich vor allem dort, wo ein Ausfall den Betrieb wirklich stoppt.
