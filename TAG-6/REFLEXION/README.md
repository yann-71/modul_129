# Reflexion TAG-6

**Datum:** 28.09.2026

## Mein Lernertrag

Ich kann jetzt Redundanz und Load Balancing klar auseinanderhalten. Redundanz zielt auf Verfügbarkeit, Load Balancing auf Leistung, und eine zweite aktive Leitung bringt beides gleichzeitig. Neu ist mir auch, dass Load Balancing pro Verbindung verteilt und nicht pro Paket, ein einzelner Download wird dadurch also nicht schneller.

Beim Analysieren des Netzwerkfalls habe ich gelernt, systematisch nach Single Points of Failure zu suchen, also Komponente für Komponente zu fragen, was passiert, wenn genau diese ausfällt. Ausserdem kann ich Verfügbarkeit jetzt in Zahlen einordnen: 99,9 Prozent heissen rund neun Stunden Ausfall pro Jahr, und mit zwei bis drei Wochen Lieferzeit für ein Ersatzgerät ist das nicht zu schaffen.

Technisch neu war für mich die Floating Static Route, also eine Ersatzroute mit schlechterer Distanz, die erst einspringt, wenn die Hauptroute wegfällt. Das passt gut zu dem, was wir im letzten Block mit den statischen Routen gemacht haben.

## Meine Herausforderungen

Schwierig war die Priorisierung. Technisch könnte man alles doppelt auslegen, aber dann wird es teuer und unnötig komplex. Ich musste mich zwingen, zuerst zu fragen, welcher Ausfall den Betrieb wirklich stoppt, und erst danach Massnahmen aufzulisten.

Unsicher bin ich noch bei der konkreten Umsetzung von Policy Based Routing und QoS, das kenne ich bisher nur aus der Theorie. Auch HSRP und VRRP habe ich noch nie selbst konfiguriert, das würde ich gerne einmal in Packet Tracer ausprobieren.

## Mein Lernprozess

Ich bin beim Fall zuerst die Topologie durchgegangen und habe eine Liste mit Single Points of Failure gemacht, bevor ich überhaupt an Lösungen gedacht habe. Diese Reihenfolge hat gut funktioniert, weil die Massnahmen sich danach fast von selbst ergeben haben.

Geholfen hat auch, die Empfehlung in sofort, mittelfristig und organisatorisch aufzuteilen. So wirkt sie wie ein echter Vorschlag an einen Chef und nicht wie eine Wunschliste. Beim nächsten Mal möchte ich zusätzlich grobe Kostenschätzungen einbauen, weil eine Empfehlung ohne Zahlen in der Praxis schwer zu beurteilen ist. Ausserdem will ich die offenen Punkte aus Block 5 (RIP und die Ausfalltests) in diesem Block endlich abschliessen.
