# Theorie TAG-3: Subnetting

## Weshalb Subnetting

Ein grosses Netz wird in kleinere Subnetze unterteilt, um Broadcastdomänen zu verkleinern, den Verkehr zu trennen, die Sicherheit zu erhöhen (Trennung von Abteilungen) und die Adressen bedarfsgerecht zu verteilen. Die Subnetze werden anschliessend über Router wieder verbunden.

## Die wichtigsten Begriffe

| Begriff | Bedeutung |
|---|---|
| Netzwerkadresse | alle Host-Bits auf 0, bezeichnet das Netz selbst |
| Broadcastadresse | alle Host-Bits auf 1, spricht alle Geräte im Netz an |
| Hostbereich | alle Adressen dazwischen, nutzbar für Geräte |
| Subnetzmaske | trennt Netz-ID von Host-ID, zum Beispiel 255.255.255.0 |
| CIDR-Präfix | Kurzschreibweise für die Maske, zum Beispiel /24 |

## Rechenweg mit der Blockgrösse

1. Präfix anschauen und Host-Bits bestimmen: Host-Bits = 32 − Präfix.
2. Blockgrösse = 2^(Host-Bits) im betroffenen Oktett.
3. Die Blöcke laufen in Schritten der Blockgrösse: 0, 8, 16, 24 usw.
4. Die Adresse in den passenden Block einordnen, das ist die Netzwerkadresse.
5. Broadcast = nächste Netzadresse − 1, Hostbereich = alles dazwischen.
6. Nutzbare Hosts = Blockgrösse − 2 (Netz- und Broadcastadresse).

**Beispiel 123.45.67.89/29:** 32 − 29 = 3 Host-Bits, Blockgrösse 8. Blöcke: 80, 88, 96. Die .89 liegt im Block ab .88, also Netz 123.45.67.88, Broadcast 123.45.67.95, Hosts .89 bis .94, das sind 6 nutzbare Adressen.

## Wichtige Masken

| Präfix | Maske | Adressen | nutzbare Hosts |
|---|---|---|---|
| /24 | 255.255.255.0 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 6 |
| /30 | 255.255.255.252 | 4 | 2 |

Ein /30 wird typischerweise für Punkt-zu-Punkt-Verbindungen zwischen zwei Routern verwendet, weil dort genau zwei nutzbare Adressen gebraucht werden.

## Plausibilität und Zugehörigkeit

Eine Netzwerkadresse muss immer ein Vielfaches der Blockgrösse sein. 10.128.10.12 kann zum Beispiel bei /29 (Blockgrösse 8) keine Netzadresse sein, weil 12 kein Vielfaches von 8 ist. Ob zwei Adressen im selben Netz liegen, prüft man, indem man für beide die Netzadresse berechnet und vergleicht.

## VLSM

Bei VLSM (Variable Length Subnet Mask) erhalten die Teilnetze unterschiedlich grosse Masken, je nach Bedarf. Vorgehen:

1. Bedarf pro Netz ermitteln (Geräte plus Router-Interface).
2. Auf die nächste Zweierpotenz aufrunden und daraus die Maske bestimmen.
3. Netze von gross nach klein der Reihe nach vergeben, sonst entstehen Lücken.
4. Reserve für Erweiterungen einplanen.

Das Kreisdiagramm hilft beim Zeichnen: Der ganze Kreis ist das Ursprungsnetz, jede Unterteilung halbiert einen Sektor, und an den Unterteilungsradien stehen die numerischen Grenzen mit der Maske.
