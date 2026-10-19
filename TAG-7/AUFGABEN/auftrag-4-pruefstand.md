# Auftrag 4 - Netzwerkdokumentation auf dem Prüfstand

**Modul:** M129, Unterrichtsblock 7
**Datum:** 19.10.2026

Die Dokumentation aus [Auftrag 3](auftrag-3-netzwerkdokumentation.md) wird aus der Sicht einer Fachperson geprüft, die das Netzwerk übernehmen müsste. Damit die Prüfung nicht vom Bauchgefühl abhängt, habe ich zuerst eine Checkliste erstellt. Mit der gleichen Checkliste prüfe ich auch die Dokumentation meiner Partnerperson.

## Checkliste

| Nr. | Prüffrage | Erfüllt? |
|---|---|---|
| 1 | Ist die Dokumentation verständlich und nachvollziehbar (Aufbau, Titel, Stand, Verfasser)? | |
| 2 | Sind alle Geräte aus dem Schema im Inventar aufgeführt, inkl. Aufgabe und Standort? | |
| 3 | Sind alle Netze mit Netzadresse, Präfix, Gateway und VLAN aufgeführt? | |
| 4 | Haben alle statischen Geräte eine dokumentierte IP-Adresse? | |
| 5 | Sind die DHCP-Bereiche dokumentiert und überschneiden sie sich nicht mit statischen Adressen? | |
| 6 | Sind auf jeder Verbindung im Schema die Interface-Namen auf beiden Seiten ersichtlich? | |
| 7 | Ist ersichtlich, welche Ports Access und welche Trunk sind, inkl. erlaubte VLANs? | |
| 8 | Sind die Management-Adressen der Netzwerkgeräte dokumentiert? | |
| 9 | Sind die Internetanbindung und der Provider dokumentiert? | |
| 10 | Sind die Designentscheide begründet (VLANs, Adressbereiche, Netzgrösse)? | |
| 11 | Ist festgehalten, wo Zugangsdaten und Konfigurations-Backups liegen, ohne Passwörter im Klartext? | |
| 12 | Gibt es ein Änderungsprotokoll mit Datum und Version? | |

## Selbstprüfung meiner Dokumentation

| Nr. | Ergebnis | Bemerkung |
|---|---|---|
| 1 | ja | Kopfdaten mit Version, Stand, Verfasser vorhanden |
| 2 | ja | alle Geräte inkl. Modem des Providers |
| 3 | ja | Tabelle 1.2 mit allen sechs VLANs und dem WAN-Netz |
| 4 | ja | Tabelle 3.1 |
| 5 | ja | Tabelle 3.2, DHCP ab .100, statisch unter .50 |
| 6 | ja | im logischen Schema und zusätzlich in der Verkabelungstabelle |
| 7 | ja | Modus in der Verkabelungstabelle und in der Switch-Konfiguration |
| 8 | ja | VLAN 99, Adressen .1 bis .5 |
| 9 | teilweise | Provider und Bandbreite vorhanden, Vertragsnummer nur als Verweis |
| 10 | ja | neun Designentscheide in Frage/Antwort-Form |
| 11 | ja | Passwortmanager und Backup-Pfad genannt |
| 12 | ja | Kapitel 7 |

### Was ich nach der Selbstprüfung ergänzt habe

- **Trunk zum Access-Switch:** Im vorgegebenen Schema steht auf dieser Leitung nur "VLAN 10 Clients". Da der Access-Switch aber eine Management-Adresse hat, muss auch VLAN 99 darüber laufen. Ich habe das als Trunk dokumentiert und einen eigenen Designentscheid dazu geschrieben.
- **Unbenutzte Ports:** waren zuerst nicht dokumentiert. Jetzt steht in der Konfiguration, dass sie abgeschaltet und in VLAN 999 sind, und bei den Designentscheiden warum.
- **Schwachstellen:** Kapitel 6 ergänzt, damit eine Nachfolgerin oder ein Nachfolger sofort sieht, wo das Netz bewusst nicht redundant ist.

## Austausch mit der Partnerperson

Ablauf: Dokumentationen austauschen, mit der Checkliste oben prüfen, Unklarheiten notieren, gemeinsam besprechen, Dokumentation ergänzen.

**Fragen, die ich bei der fremden Dokumentation zusätzlich stelle:**

- Könnte ich mit dieser Dokumentation einen defekten Switch ersetzen, ohne jemanden anzurufen?
- Finde ich für ein beliebiges Gerät in weniger als einer Minute seine IP-Adresse und seinen Switchport?
- Weiss ich, was ich nicht ändern darf, und warum?

**Rückmeldungen zu meiner Dokumentation:**

| Nr. | Rückmeldung der Partnerperson | Umgesetzt |
|---|---|---|
| 1 | | |
| 2 | | |
| 3 | | |

Der Austausch findet im Unterricht statt, die Rückmeldungen trage ich danach hier ein.
