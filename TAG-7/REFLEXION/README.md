# Reflexion TAG-7

**Datum:** 19.10.2026

## Mein Lernertrag

Ich kenne jetzt die fünf Bestandteile einer Netzwerkdokumentation: Netzwerkschema, Inventar, IP-Adressplan, Konfigurationsinformationen und Designentscheide. Bisher hätte ich unter Dokumentation vor allem ein Schema und eine Liste mit IP-Adressen verstanden. Neu ist für mich, wie wichtig die Designentscheide sind. Die Konfiguration kann man zur Not vom Gerät auslesen, die Begründung dahinter aber nicht.

Beim Erstellen der Dokumentation habe ich gemerkt, wie viele Angaben im vorgegebenen Schema fehlen. Ein Beispiel ist die Leitung zwischen Core- und Access-Switch: Im Schema steht nur VLAN 10, aber weil der Access-Switch eine Management-Adresse hat, muss es ein Trunk sein. Solche Details fallen erst auf, wenn man alles konsequent zu Ende denkt.

Bei der Challenge habe ich gelernt, Informationen danach zu unterscheiden, ob ich sie selbst herausfinden kann oder nicht. Eine Subnetzmaske finde ich mit `ipconfig` in Sekunden, ein Passwort oder den Kontakt des Fernwartungspartners nie.

## Meine Herausforderungen

Schwierig war es, "realistische Werte" zu ergänzen, ohne zu übertreiben. Ich habe zuerst zu viele Details eingebaut und musste mich fragen, was eine Fachperson wirklich braucht. Ausserdem musste ich darauf achten, dass die Adressbereiche zusammenpassen: Die DHCP-Pools dürfen sich nicht mit den statischen Adressen überschneiden.

Unsicher bin ich noch bei der genauen Konfiguration der Access-Points mit mehreren SSIDs und VLANs. Das habe ich bisher nur theoretisch dokumentiert und noch nicht selbst eingerichtet.

## Mein Lernprozess

Hilfreich war, die Dokumentation in der Reihenfolge der fünf Bestandteile aufzubauen und erst am Schluss die Designentscheide zu schreiben. Beim Schreiben der Begründungen habe ich zwei Fehler in meinem eigenen Konzept gefunden, das hat gezeigt, dass die Designentscheide auch für mich selber ein Kontrollinstrument sind.

Das Netzwerkschema habe ich als Mermaid-Diagramm direkt im Markdown erstellt, damit es im Git-Repo versioniert ist und zusammen mit dem Text aktuell bleibt. Für die LB2 im nächsten Block will ich die Theorie zu Block 7 und die Fehlersuche-Befehle aus den bisherigen Blöcken nochmals repetieren.
