# Reflexion TAG-4

**Datum:** 07.09.2026

## Mein Lernertrag

Ich verstehe jetzt, warum redundante Leitungen zwischen Switches ohne STP ein Problem sind, nämlich weil es auf Layer 2 kein TTL gibt und Broadcasts endlos kreisen würden. Die Wahl der Root Bridge kann ich selbst durchrechnen: zuerst die Priorität vergleichen und erst bei Gleichstand die MAC-Adresse.

Ebenfalls klar geworden ist der Unterschied zwischen Redundanz und Bandbreite. Ein blockierter Port liegt nur in Reserve und bringt im Normalbetrieb gar nichts an Geschwindigkeit. Für mehr Bandbreite braucht es EtherChannel. Diese Aussage aus Auftrag 7 fand ich sehr lehrreich, weil sie genau das typische Missverständnis trifft.

## Meine Herausforderungen

Am meisten Mühe hatte ich bei der Bestimmung der Portrollen, vor allem bei der Strecke, auf der beide Switches dieselben Pfadkosten haben. Dass dann die tiefere Bridge-ID des Nachbarn entscheidet, musste ich mehrmals nachlesen. Auch die Vorhersage beim vierten Switch war anspruchsvoll, weil plötzlich zwei Ports blockiert werden.

Zusätzlich hatte ich Probleme mit Packet Tracer, darum konnte ich meine Vorhersagen noch nicht mit `show spanning-tree` verifizieren. Das ist mein offener Punkt aus diesem Block.

## Mein Lernprozess

Ich habe zuerst alles auf Papier vorhergesagt und wollte es danach in Packet Tracer prüfen. Dieses Vorgehen finde ich gut, weil man so wirklich merkt, ob man die Regeln verstanden hat, statt einfach die Ausgabe abzuschreiben.

Weniger gut gelaufen ist, dass ich die praktische Überprüfung wegen der Softwareprobleme aufgeschoben habe. Beim nächsten Mal löse ich technische Hindernisse sofort, oder ich frage jemanden, statt die Aufgabe liegen zu lassen. Ausserdem will ich die offenen Punkte direkt in der Dokumentation markieren, das hat hier gut funktioniert und hilft mir beim Nachholen.
