# M129 – Challenge-Aufgabe: Netzwerkdatenverkehr abschätzen

*Lösung und Dokumentation von Yann Kobler, Netz «KOB»*
*Datum: 24. August 2026*

---

## 1. Ausgangslage

Im Netzwerk «Netz KOB» hängen am Core-Switch SW-C1 zwei Server (FileSrv mit 10 Gbit/s und BackupSrv mit 10 Gbit/s). Vom Core-Switch führen zwei 1-Gbit/s-Leitungen zu den Verteil-Switches SW-A2 und SW-B3, die zusätzlich über eine 100-Mbit/s-Redundanzleitung verbunden sind. An SW-A2 hängen die Gruppen NOTE und OBST, an SW-B3 die Gruppen LERN, ECHT und RAUM. Ziel ist es, den theoretischen Bandbreitenbedarf im Worst-Case (alle PCs greifen gleichzeitig auf FileSrv zu) zu berechnen und Engpässe zu identifizieren.

---

## 2. Aufgabe 1 – Theoretischer Bedarf pro Gruppe

Massgebend ist jeweils die tatsächliche Engstelle zwischen PC und Access-Switch, da hier die einzelnen PCs limitiert werden, bevor der Traffic überhaupt gebündelt wird.

```
Bedarf Gruppe = Anzahl PCs × Anbindung PC → Access-Switch
```

| Gruppe | Anzahl PCs | Anbindung/PC | Rechnung | Bedarf |
|---|---|---|---|---|
| NOTE | 7 | 100 Mbit/s | 7 × 100 Mbit/s | 700 Mbit/s |
| OBST | 4 | 100 Mbit/s | 4 × 100 Mbit/s | 400 Mbit/s |
| LERN | 9 | 1 Gbit/s | 9 × 1000 Mbit/s | 9'000 Mbit/s (9 Gbit/s) |
| ECHT | 3 | 100 Mbit/s | 3 × 100 Mbit/s | 300 Mbit/s |
| RAUM | 5 | 1 Gbit/s | 5 × 1000 Mbit/s | 5'000 Mbit/s (5 Gbit/s) |

Wichtig bei ECHT: Die Access-Switch-PCs sind nur mit 100 Mbit/s angebunden, obwohl der Access-Switch selbst mit 1 Gbit/s zum Verteil-Switch verbunden ist. Die Engstelle liegt hier klar bei den PCs.

---

## 3. Aufgabe 2 – Auslastung der Verteil-Switches

Pro Verteil-Switch wird der Bedarf der angehängten Gruppen summiert und mit dem Uplink zum Core-Switch (je 1 Gbit/s = 1'000 Mbit/s) verglichen.

### SW-A2 (Gruppen NOTE + OBST)

```
Bedarf = 700 Mbit/s + 400 Mbit/s = 1'100 Mbit/s
```

Vorhandener Uplink SW-A2 → SW-C1: 1'000 Mbit/s (1 Gbit/s)

**→ Bedarf (1'100 Mbit/s) übersteigt den Uplink (1'000 Mbit/s) knapp. Das ist bereits ein kleiner Engpass, ca. 10% zu wenig Bandbreite im Worst-Case.**

### SW-B3 (Gruppen LERN + ECHT + RAUM)

```
Bedarf = 9'000 Mbit/s + 300 Mbit/s + 5'000 Mbit/s = 14'300 Mbit/s
```

Vorhandener Uplink SW-B3 → SW-C1: 1'000 Mbit/s (1 Gbit/s)

**→ Massiver Engpass: Der Bedarf ist über 14× so hoch wie die vorhandene Uplink-Bandbreite. Das ist der grösste Flaschenhals im ganzen Netzwerk.**

---

## 4. Aufgabe 3 – Gesamtbelastung am Core-Switch

Am Core-Switch SW-C1 kommt theoretisch die Summe aller fünf Gruppen an, wenn alle gleichzeitig auf FileSrv zugreifen:

```
Gesamtbedarf = 700 + 400 + 9'000 + 300 + 5'000 = 15'400 Mbit/s = 15,4 Gbit/s
```

Anbindung von FileSrv (Y-01) an SW-C1: 10 Gbit/s

**→ Auch hier ein Engpass: 15,4 Gbit/s Bedarf stehen nur 10 Gbit/s Serverbandbreite gegenüber. FileSrv könnte im absoluten Worst-Case rund 5,4 Gbit/s nicht bedienen, was etwa 35% des theoretischen Gesamtbedarfs entspricht.**

---

## 5. Aufgabe 4 – Zeitberechnung (Download LERN-Gruppe)

Die Gruppe LERN (9 PCs) lädt gemeinsam eine Datei von 2,5 GByte herunter, alle PCs gleichzeitig und gleichmässig beteiligt.

### Schritt 1: Dateigrösse in Bit umrechnen

```
2,5 GByte × 8 = 20 Gbit = 20'000 Mbit
```

### Schritt 2: Relevante Engstelle finden

Für den gemeinsamen Download der Gruppe zählt nicht die einzelne PC-Anbindung (1 Gbit/s je PC, da genug vorhanden), sondern die geteilten Leitungen, die alle 9 PCs zusammen nutzen müssen:

- Access-Switch SW-L6 → SW-B3: 1 Gbit/s, geteilt durch alle 9 PCs
- SW-B3 → SW-C1 (Core): 1 Gbit/s, ebenfalls geteilt (isoliert für diesen Download betrachtet)
- FileSrv-Anbindung: 10 Gbit/s, kein Engpass für nur eine Gruppe

Die engste gemeinsam genutzte Leitung ist somit 1 Gbit/s = 1'000 Mbit/s.

### Schritt 3: Zeit berechnen

```
Zeit = Datenmenge ÷ Bandbreite = 20'000 Mbit ÷ 1'000 Mbit/s = 20 Sekunden
```

**→ Der Download dauert theoretisch minimal 20 Sekunden, wenn alle 9 PCs gleichmässig die gemeinsame 1-Gbit/s-Leitung ausnutzen (also im Schnitt ca. 111 Mbit/s pro PC).**

---

## 6. Analyse: Flaschenhals und Verbesserungsmassnahmen

### Grösster Engpass: Uplink SW-B3 → SW-C1

Der klar grösste Flaschenhals im Netzwerk ist die 1-Gbit/s-Leitung zwischen SW-B3 und dem Core-Switch SW-C1. Die drei daran hängenden Gruppen (LERN, ECHT, RAUM) haben zusammen einen theoretischen Bedarf von 14'300 Mbit/s, aber nur 1'000 Mbit/s stehen zur Verfügung. Das ist ein Verhältnis von über 14:1, also ein massiver Engpass.

### Verbesserungsmassnahme

- Uplink SW-B3 → SW-C1 auf mindestens 10 Gbit/s aufrüsten (oder mehrere 1-Gbit/s-Leitungen bündeln, z.B. mit Link Aggregation/LACP), damit die Leitung näher am tatsächlichen Bedarf liegt
- Auch der Uplink von SW-A2 sollte leicht erhöht werden, da 1'100 Mbit/s Bedarf gegen 1'000 Mbit/s Kapazität stehen
- Zusätzlich könnte man die Gruppe LERN (mit 9 Gbit/s Bedarf) auf einen eigenen Access-Switch mit direkterem, schnellerem Uplink verlegen, damit sie ECHT und RAUM nicht zusätzlich belastet

### Redundanzleitung SW-A2 ↔ SW-B3 (100 Mbit/s)

Die Redundanzleitung ist im aktuellen Zustand nicht sinnvoll dimensioniert. Sie dient als Backup-Pfad bei Ausfall eines Uplinks, hat aber nur 100 Mbit/s. Fällt z.B. der Uplink von SW-B3 aus, müssten die Gruppen LERN, ECHT und RAUM (14'300 Mbit/s Bedarf) über diese 100-Mbit/s-Leitung und danach über SW-A2 umgeleitet werden. Das wäre ein Verhältnis von 143:1, im Ernstfall also praktisch unbrauchbar.

**Empfehlung: Die Redundanzleitung sollte auf mindestens die gleiche Kapazität wie die regulären Uplinks (1 Gbit/s, besser mehr) ausgebaut werden, damit sie im Ausfallfall tatsächlich eine sinnvolle Alternative bietet und nicht selbst sofort zum Engpass wird.**

---

## 7. Fazit

Die Berechnung zeigt, dass das Netzwerk «KOB» im theoretischen Worst-Case an mehreren Stellen unterdimensioniert ist: am Uplink SW-A2 (leicht), massiv am Uplink SW-B3, an der Serveranbindung von FileSrv und an der Redundanzleitung. In der Praxis tritt dieser absolute Worst-Case selten auf, da nicht alle PCs gleichzeitig mit voller Geschwindigkeit senden. Trotzdem zeigt die Rechnung, wo bei einem realen Rollout oder Systemstart (z.B. morgens) spürbare Engpässe entstehen könnten, und wo eine gezielte Aufrüstung am meisten bringen würde.
