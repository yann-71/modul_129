# Reflexion TAG-8

**Datum:** 26.10.2026

## Mein Lernertrag

Ich habe gelernt, die Fehlersuche systematisch anzugehen, statt einfach auszuprobieren. Am meisten gebracht hat die Kombination aus statischer Prüfung (alle Konfigurationen mit `ipconfig /all`, `show running-config`, `show ip route` in eine Tabelle übertragen und vergleichen) und dynamischer Prüfung mit `ping` und `tracert`.

Eine wichtige Erkenntnis: Ein erfolgreicher Ping heisst nicht, dass alles richtig konfiguriert ist. In A1 und A2 funktionierten Pings trotz falscher Subnetzmasken. Solche Fehler findet man nur, wenn man die Konfiguration mit dem Soll-Zustand vergleicht. Umgekehrt muss man bei einem fehlgeschlagenen Ping immer an den Rückweg denken, wie bei A5, wo nur das Gateway beim Empfänger fehlte.

Neu war für mich der DHCP-Relay mit `ip helper-address` und warum der Server für jedes Subnetz einen eigenen Pool braucht. Auch die Zusatzfrage zum Layer-2-Loop hat mir gezeigt, wie man mit SNMP und Monitoring Probleme erkennt, bevor sich die Benutzer melden.

## Meine Herausforderungen

Schwierig war A4 mit 16 Switches und 27 PCs. Den Loop habe ich erst gefunden, als ich die Verbindungen der Switches aufgezeichnet habe. In der Topologie selbst war der Ring nicht auf den ersten Blick sichtbar. Ausserdem hatte ich nach dem ersten Fehler oft das Gefühl, fertig zu sein. Dass in A3 und A9 noch ein zweiter Fehler steckte, habe ich nur gemerkt, weil ich trotzdem alle Geräte fertig geprüft habe.

Bei der Challenge war die Herausforderung, nicht sofort an IP-Fehler zu denken. Die IP-Konfiguration war korrekt, der Fehler lag in der VLAN-Zuordnung des Ports, also eine Schicht tiefer.

## Mein Lernprozess

Mein Vorgehen mit dem gleichen Schema für alle zehn Topologien hat sich bewährt, ab der dritten Topologie ging es deutlich schneller. Hilfreich war auch, nach jeder Korrektur sofort erneut zu testen, so wusste ich immer, welche Änderung was bewirkt hat.

Die Musterlösung habe ich erst am Schluss zum Abgleich geöffnet. So konnte ich sehen, dass ich alle Fehler gefunden hatte und sogar zwei zusätzliche. Beim nächsten Mal möchte ich die Printscreens direkt während der Arbeit machen und nicht erst danach, das spart Zeit. Die Verbindung zu TAG-7 ist mir klar geworden: Mit einer guten Netzwerkdokumentation wäre jeder dieser Fehler viel schneller gefunden, weil der Soll-Zustand bekannt ist.
