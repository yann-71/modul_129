# Reflexion TAG-3

**Datum:** 31.08.2026

## Mein Lernertrag

Subnetting kann ich jetzt zügig von Hand rechnen, ohne jedes Mal alles binär aufzuschreiben. Der Trick mit der Blockgrösse hat bei mir den Knoten gelöst: Host-Bits zählen, Zweierpotenz bilden und dann schauen, in welchen Block die Adresse fällt. Damit finde ich Netzadresse, Broadcast und Hostbereich sehr schnell.

Ausserdem verstehe ich jetzt VLSM. Wichtig ist, immer mit dem grössten Netz zu beginnen und den Bedarf inklusive Router-Interface zu rechnen. Auch klar geworden ist mir, warum Standleitungen zwischen zwei Routern als /30 gemacht werden.

## Meine Herausforderungen

Am Anfang habe ich bei der Plausibilitätsprüfung nicht gesehen, dass eine Netzadresse ein Vielfaches der Blockgrösse sein muss, das musste ich zweimal durchdenken. Schwierig war auch, bei den Kreisdiagrammen die Grenzen sauber einzuzeichnen, statt einfach nur die Tabelle auszufüllen.

Bei Aufgabe 3 fehlten mir zuerst die Werte aus der Angabe, darum konnte ich sie erst später nachliefern. Die Challenge 3 (Tabelle mit drei eingebauten Fehlern) habe ich generiert, die Fehlersuche und die Besprechung stehen noch aus. Offen ist für mich noch, wie man ein Adresskonzept in einer echten Firma plant, wo auch VLANs und Reserven für Standorte dazukommen.

## Mein Lernprozess

Ich habe jede Aufgabe zuerst selbst gerechnet und danach mit dem IP-Calculator (jodies.de/ipcalc) kontrolliert. Das war eine gute Kombination, weil ich so sofort gemerkt habe, wenn ich mich bei der Blockgrösse vertan habe. Den Rechenweg habe ich bewusst mitgeschrieben, damit ich ihn später nachvollziehen kann.

Etwas unglücklich war, dass Packet Tracer bei mir zuerst nicht sauber installiert war, darum konnte ich die praktischen Teile erst später nachholen. Beim nächsten Mal richte ich die Software vor dem Unterrichtsblock ein, damit ich im Unterricht direkt starten kann.
