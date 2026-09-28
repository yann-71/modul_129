# Reflexion TAG-5

**Datum:** 21.09.2026

## Mein Lernertrag

Ich kann jetzt erklären, wie ein Router anhand der Routingtabelle entscheidet, wohin ein Paket geht, und ich kann eine Routingtabelle selbst aufstellen, inklusive Defaultroute. Neu verstanden habe ich das Longest Prefix Match, also dass ein spezifischer Eintrag immer die Defaultroute schlägt. Genau darauf beruht auch der Fehler in meiner Challenge 5.

In der Praxis habe ich ein Netz mit zwei Standorten in Packet Tracer aufgebaut, subnetzt, die Router konfiguriert und getestet. Dabei ist mir klar geworden, wie wichtig der Rückweg ist: Ein Ping geht nur, wenn beide Richtungen stimmen. Mit tracert kann ich den Weg jetzt auch belegen, und an der TTL sehe ich, wie viele Router ein Paket durchlaufen hat.

## Meine Herausforderungen

Der Aufbau in Packet Tracer war anspruchsvoller als gedacht. Beim PT-Empty-Router werden die Slots von rechts nach links nummeriert, darum hiessen meine ersten Interfaces FastEthernet8/0 und 9/0 statt 0/0 und 1/0. Ausserdem muss das Gerät zwingend ausgeschaltet sein, bevor man ein Modul einbauen kann, sonst passiert einfach nichts.

Bei der seriellen Verbindung musste ich zuerst herausfinden, dass die Taktrate auf der DCE-Seite gesetzt werden muss. Offen sind bei mir noch die Ausfalltests mit abgeschaltetem Switch und der ganze RIP-Teil, den möchte ich als Nächstes fertig machen.

## Mein Lernprozess

Ich habe zuerst das Adresskonzept und das logische Netzwerkschema erstellt und erst danach in Packet Tracer gebaut. Das war genau richtig, weil ich beim Konfigurieren nur noch abtippen musste und nicht mehr überlegen. Die Konfigurationen habe ich pro Router zuerst als Textblock vorbereitet, das hat Tippfehler reduziert.

Gut funktioniert hat auch, dass ich jeden Test sofort ins Testprotokoll eingetragen habe, statt am Schluss aus dem Gedächtnis. Beim nächsten Mal möchte ich früher mit dem praktischen Teil starten, damit ich am Schluss nicht unter Zeitdruck komme, und die Screenshots konsequent gleich beim Testen machen.
