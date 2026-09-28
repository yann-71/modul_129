# Challenge 3 - Subnetting-Fehlersuche

**Modul:** M129, Unterrichtsblock 3
**Datum:** 31.08.2026

Die Aufgabe wurde mit dem im Auftrag vorgegebenen Prompt individuell generiert:

```text
Erfinde eine Subnetting-Aufgabe mit 8 bis 12 Angaben zu Netzwerkadressen, Broadcastadressen, Hostbereichen und Subnetzmasken.
Baue genau 3 Fehler ein.
Die Fehler dürfen sein:
- falsche Netzwerkadresse
- falsche Broadcastadresse
- überlappende Netze
- ungültiger Hostbereich
- falsche CIDR-Maske
Liefere nur die Tabelle, keine Lösung.
```

---

## Aufgabenstellung

In der folgenden Tabelle sind **genau 3 Fehler** enthalten. Finde sie, begründe, weshalb der Eintrag falsch ist, und gib die korrekte Angabe an.

| Nr. | Netzwerkadresse | CIDR | Subnetzmaske | Hostbereich | Broadcastadresse |
|---|---|---|---|---|---|
| 1 | 192.168.10.0 | /26 | 255.255.255.192 | 192.168.10.1 - 192.168.10.62 | 192.168.10.63 |
| 2 | 192.168.10.64 | /26 | 255.255.255.192 | 192.168.10.65 - 192.168.10.126 | 192.168.10.127 |
| 3 | 192.168.10.128 | /27 | 255.255.255.224 | 192.168.10.129 - 192.168.10.158 | 192.168.10.191 |
| 4 | 192.168.10.160 | /28 | 255.255.255.240 | 192.168.10.161 - 192.168.10.174 | 192.168.10.175 |
| 5 | 172.16.4.0 | /22 | 255.255.252.0 | 172.16.4.1 - 172.16.7.254 | 172.16.7.255 |
| 6 | 172.16.8.0 | /21 | 255.255.248.0 | 172.16.8.1 - 172.16.15.254 | 172.16.15.255 |
| 7 | 10.20.30.0 | /25 | 255.255.255.128 | 10.20.30.1 - 10.20.30.126 | 10.20.30.127 |
| 8 | 10.20.30.128 | /25 | 255.255.255.128 | 10.20.30.129 - 10.20.30.254 | 10.20.30.255 |
| 9 | 10.20.31.2 | /30 | 255.255.255.252 | 10.20.31.3 - 10.20.31.4 | 10.20.31.5 |
| 10 | 192.168.20.0 | /26 | 255.255.255.192 | 192.168.20.1 - 192.168.20.62 | 192.168.20.63 |
| 11 | 192.168.20.32 | /28 | 255.255.255.240 | 192.168.20.33 - 192.168.20.46 | 192.168.20.47 |

**Auftrag:**

1. Prüfe jede Zeile mit dem Rechenweg über die Blockgrösse.
2. Markiere die drei fehlerhaften Zeilen.
3. Begründe pro Fehler, um welche Fehlerart es sich handelt (falsche Netzwerkadresse, falsche Broadcastadresse, überlappende Netze, ungültiger Hostbereich oder falsche CIDR-Maske).
4. Gib für jeden Fehler die korrigierte Angabe an.

---

## Meine Lösung

> Diese Challenge löse ich selbst und melde mich danach bei der Lehrperson zur Besprechung an.
> Hier kommen mein Rechenweg, die drei gefundenen Fehler und die Korrekturen hinein.

### Zeile für Zeile geprüft

| Nr. | Blockgrösse | Erwartete Netzadresse | Erwarteter Broadcast | Beurteilung |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |
| 4 | | | | |
| 5 | | | | |
| 6 | | | | |
| 7 | | | | |
| 8 | | | | |
| 9 | | | | |
| 10 | | | | |
| 11 | | | | |

### Gefundene Fehler

| Fehler | Zeile | Fehlerart | Begründung | Korrektur |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |
