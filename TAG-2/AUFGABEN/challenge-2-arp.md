# Challenge 2 – ARP-Protokoll (Address Resolution Protocol)

**Modul:** M129, Unterrichtsblock 2
**Datum:** 31.08.2026
**Tool:** Wireshark 4.6.3, Windows-Kommandozeile

---

## 1. ARP-Request und ARP-Reply dokumentieren

Im aufgezeichneten Datenstrom auf dem Interface WLAN wurde folgendes Request/Reply-Paar gefunden:

| Nr. | Zeit | Source | Destination | Info |
|---|---|---|---|---|
| 115184 | 560.173846 | Intel_6e:a0:87 (80:c0:1e:6e:a0:87) | Intel_40:1a:a5 (4c:a9:54:40:1a:a5) | Who has 10.62.105.180? Tell 10.62.109.103 |
| 115187 | 560.180979 | Intel_40:1a:a5 (4c:a9:54:40:1a:a5) | Intel_6e:a0:87 (80:c0:1e:6e:a0:87) | 10.62.105.180 is at 4c:a9:54:40:1a:a5 |

**Erklärung:** Mein Notebook (10.62.109.103) wollte mit dem Gerät 10.62.105.180 kommunizieren, kannte aber dessen MAC-Adresse noch nicht. Deshalb wurde ein ARP-Request als Broadcast-ähnliche Anfrage verschickt ("Who has 10.62.105.180?"). Das Zielgerät hat direkt darauf mit seiner MAC-Adresse geantwortet (ARP-Reply), damit die spätere Kommunikation auf Layer 2 (Ethernet) adressiert werden kann.

![ARP Request und Reply für 10.62.105.180](screenshots/req_reply_180.png)

---

## 2. Selbst ausgelöster ARP-Vorgang

Um gezielt einen eigenen ARP-Vorgang auszulösen, wurde ein Gerät angepingt, dessen MAC-Adresse zum Zeitpunkt des Tests bereits einmal bekannt war (10.62.105.14), um sicherzustellen, dass es im Netz aktiv ist und antwortet.

**Befehl:**
```
ping 10.62.105.14
```

**Ergebnis in Wireshark (Filter: arp):**

| Nr. | Zeit | Source | Destination | Info |
|---|---|---|---|---|
| 142966 | 744.169956 | Intel_6e:a0:87 | Intel_8c:ba:fe | Who has 10.62.105.14? Tell 10.62.109.103 |
| 142968 | 744.201490 | Intel_8c:ba:fe | Intel_6e:a0:87 | 10.62.105.14 is at 9c:97:1b:8c:ba:fe |

**Erklärung:** Durch das Ausführen von `ping 10.62.105.14` musste mein Notebook zuerst die MAC-Adresse des Zielgeräts auflösen, bevor das eigentliche ICMP-Paket (Ping) verschickt werden konnte. Das ist der klassische Ablauf: Vor jeder Kommunikation im lokalen Netz zwischen zwei Geräten, deren MAC-Adresse noch nicht im ARP-Cache steht, wird zuerst ein ARP-Request/Reply durchgeführt.

![ARP Request und Reply für 10.62.105.14, ausgelöst durch ping](screenshots/ping_105_14.png)

---

## 3. ARP-Cache zu verschiedenen Zeitpunkten

**Verwendeter Befehl:** `arp -a`

### Zeitpunkt 1 (nach dem Ping-Test)

Auszug aus dem Interface 10.62.109.103 (relevante Einträge):

```
10.62.104.1    4c-01-f7-ff-fc-df   dynamisch
10.62.105.14   9c-97-1b-8c-ba-fe   dynamisch
10.62.105.37   e0-c2-64-be-92-9a   dynamisch
10.62.105.147  d8-b3-2f-10-11-bb   dynamisch
10.62.105.180  4c-a9-54-40-1a-a5   dynamisch
10.62.105.239  2c-0d-a7-cb-29-0b   dynamisch
... (insgesamt ca. 120 Einträge)
```

### Zeitpunkt 2 (einige Minuten später)

Auszug aus demselben Interface:

```
10.62.104.1    4c-01-f7-ff-fc-df   dynamisch
10.62.105.14   9c-97-1b-8c-ba-fe   dynamisch
10.62.105.37   e0-c2-64-be-92-9a   dynamisch
10.62.105.147  d8-b3-2f-10-11-bb   dynamisch
10.62.105.180  4c-a9-54-40-1a-a5   dynamisch
... (insgesamt ca. 150 Einträge)
```

### Vergleich

- **Neu dazugekommen:** ca. 38 neue Einträge (z. B. 10.62.104.140, 10.62.105.34, 10.62.105.105, 10.62.108.68, 10.62.109.74 usw.). Diese stammen von anderen Geräten im Schulnetz, mit denen mein PC in der Zwischenzeit über Broadcasts oder Netzwerkdienste in Kontakt kam.
- **Verschwunden:** der Eintrag 10.62.105.239 war zu Zeitpunkt 2 nicht mehr vorhanden.
- **Stabil geblieben:** mein selbst ausgelöster Eintrag 10.62.105.14 war zu beiden Zeitpunkten noch im Cache vorhanden, ist also innerhalb der beobachteten Zeitspanne nicht verfallen.

**Erkenntnis:** Der ARP-Cache unter Windows ist sehr dynamisch. In einem grossen, aktiven Netz (wie hier im Schul-WLAN) kommen laufend neue Einträge dazu, weil viele Geräte kommunizieren. Einzelne Einträge verschwinden nach einer gewissen Zeit wieder, wenn keine weitere Kommunikation mit diesem Gerät stattfindet, das Standard-Timeout für dynamische ARP-Einträge liegt unter Windows typischerweise im Bereich von wenigen Minuten.

---

## 4. ARP-Spoofing

**Wie funktioniert ein ARP-Spoofing-Angriff?**
Bei ARP-Spoofing sendet ein Angreifer gefälschte ARP-Replies ins lokale Netz, in denen er behauptet, die IP-Adresse eines anderen Geräts (z. B. des Gateways) gehöre zu seiner eigenen MAC-Adresse. Da ARP keine Authentifizierung kennt, übernehmen die Zielgeräte diese falsche Zuordnung ungeprüft in ihren ARP-Cache.

**Welche Risiken entstehen dadurch?**
Der gesamte Datenverkehr, der eigentlich an das echte Gerät (z. B. den Router) gehen sollte, wird stattdessen über den Angreifer geleitet (Man-in-the-Middle). Dadurch kann der Angreifer den Datenverkehr mitlesen, manipulieren oder ganz blockieren (Denial of Service), unverschlüsselte Daten wie Passwörter können so abgefangen werden.

**Mit welchen Massnahmen kann sich ein Netzwerk dagegen schützen?**
- Dynamic ARP Inspection (DAI) auf Managed Switches, die ARP-Pakete gegen eine vertrauenswürdige Tabelle prüfen
- Statische ARP-Einträge für kritische Geräte (z. B. Gateway)
- Port Security auf Switches, um nur bestimmte MAC-Adressen pro Port zuzulassen
- Netzwerksegmentierung (VLANs), um die Angriffsfläche zu verkleinern
- Verschlüsselung der Kommunikation (z. B. HTTPS, VPN), damit mitgelesene Daten nicht verwertbar sind

---

## Fazit

Die Übung zeigt, wie ARP im Hintergrund bei jeder neuen Kommunikation im lokalen Netz abläuft und wie dynamisch der ARP-Cache eines Geräts in einem grossen Netzwerk ist. Gleichzeitig wird deutlich, weshalb ARP ohne zusätzliche Schutzmassnahmen ein Sicherheitsrisiko darstellt, da es keine Authentifizierung der Antworten vorsieht.
