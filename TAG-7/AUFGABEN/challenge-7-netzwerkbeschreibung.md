# Challenge 7 - Lückenhafte Netzwerkbeschreibung

**Modul:** M129, Unterrichtsblock 7
**Datum:** 19.10.2026

Die lückenhafte Netzwerkbeschreibung wurde mit dem im Auftrag vorgegebenen Prompt individuell generiert. Zuerst der Prompt und die erhaltene Beschreibung, danach meine Lösung.

```text
Erstelle eine kurze, absichtlich lückenhafte Netzwerkdokumentation eines KMU mit 10 bis 50 Mitarbeitenden. Enthalten sein sollen:
- Router
- mindestens 2 Switches
- ein Server
- Drucker
- WLAN

Lass bewusst 8 bis 12 wichtige Informationen weg, die für den Betrieb, die Fehlersuche oder Erweiterungen relevant wären. Gib die Dokumentation in 10 bis 15 Zeilen ohne weiteren Text aus.
```

---

# Erhaltene Netzwerkbeschreibung

```text
Netzwerkdokumentation Bäckerei-Konditorei Sonnenberg AG, Winterthur (28 Mitarbeitende)
Internet: Glasfaseranschluss 500 Mbit/s, Router Zyxel USG Flex 200 im Büro
LAN: 192.168.10.0, Gateway 192.168.10.1
Switch 1: Netgear GS724T, Serverraum, verbunden mit Router
Switch 2: Netgear GS108, Backstube, über Switch 1 angeschlossen
Server: Windows Server 2019 (SRV-SB01), Dateiablage und Warenwirtschaft, IP statisch
DHCP: aktiv, verteilt Adressen an PCs und Kassen
Drucker: Brother MFC-L8900, Büro, 192.168.10.20
Etikettendrucker Backstube: über Switch 2 angeschlossen
WLAN: 2 Access-Points (Büro, Laden), SSID "Sonnenberg"
Gäste-WLAN: im Laden verfügbar
Kassen: 3 Kassensysteme im Laden, über WLAN verbunden
Backup: wird regelmässig gemacht
Fernwartung Warenwirtschaft: durch externen Partner
```

Die Situation: Die bisherige Administratorin verlässt das Unternehmen in 5 Minuten. Ich darf ihr nur 5 Fragen stellen.

---

# Meine Lösung

## 1. Welche Informationen fehlen?

Bevor ich Fragen formuliere, habe ich die Beschreibung Zeile für Zeile durchgesehen und alle Lücken notiert.

| Nr. | Lücke | Kann ich es selbst herausfinden? |
|---|---|---|
| 1 | Zugangsdaten (Router, Switches, Access-Points, Server, Domänen-Admin) | nein |
| 2 | Subnetzmaske des LANs fehlt (nur "192.168.10.0") | ja, mit `ipconfig` auf einem PC |
| 3 | IP-Adresse des Servers ("statisch", aber welche?) | ja, mit `ipconfig` auf dem Server oder `nslookup SRV-SB01` |
| 4 | Wer macht DHCP (Router oder Server)? DHCP-Bereich, DNS-Server | teilweise, mit `ipconfig /all` (DHCP-Server, DNS) |
| 5 | Backup: was, wohin, wie oft, wurde die Wiederherstellung getestet? | kaum, nur durch Suchen auf dem Server |
| 6 | Fernwartung: welcher Partner, Kontakt, wie erfolgt der Zugriff (VPN, TeamViewer)? | nein |
| 7 | Trennung Gäste-WLAN vom Firmennetz und von den Kassen (VLAN?) | nur mit Zugang zum Router und zu den APs |
| 8 | WLAN-Verschlüsselung und Passwörter, wo stehen die APs genau, welches Modell? | teilweise |
| 9 | IP-Adresse des Etikettendruckers und der Kassen | ja, mit einem Netzwerkscan oder `arp -a` |
| 10 | Provider, Vertragsnummer, öffentliche IP, Störungsnummer | nein, nur über Unterlagen der Buchhaltung |
| 11 | Portbelegung der Switches, welcher Port ist der Uplink, Management-IP der Switches | ja, mit Zugriff auf die Switches |
| 12 | Firewall-Regeln und Portweiterleitungen auf dem Router | nur mit Zugang zum Router |

**Erkenntnis:** Viele Lücken (Maske, IP-Adressen, Portbelegung) kann ich später mit `ipconfig`, `arp -a`, einem Netzwerkscan oder auf den Geräten selbst herausfinden. In 5 Minuten frage ich deshalb nach den Informationen, die **nur die Administratorin weiss** oder ohne die ich **gar nicht erst an die Geräte herankomme**.

## 2. Meine 5 Fragen, priorisiert

| Prio | Frage | Begründung |
|---|---|---|
| **1** | **Wo sind die Zugangsdaten für Router, Switches, Access-Points, Server und den Domänen-Admin abgelegt (Passwortmanager, Master-Passwort)?** | Ohne Zugangsdaten kann ich nichts konfigurieren, nichts prüfen und auch alle anderen Lücken nicht selbst schliessen. Das ist der Schlüssel zu allem anderen. |
| **2** | **Wie funktioniert das Backup: Was wird gesichert, wohin, wie oft, und wann wurde zuletzt eine Wiederherstellung getestet?** | Auf dem Server liegen Dateiablage und Warenwirtschaft, also Bestellungen, Rezepturen, Lieferanten, Buchhaltung. "Wird regelmässig gemacht" sagt nichts aus. Fehlt ein funktionierendes Backup, kann ein Festplattendefekt oder ein Verschlüsselungstrojaner die Firma lahmlegen. |
| **3** | **Wie ist das Gäste-WLAN vom Firmennetz und von den Kassen getrennt (eigenes VLAN, eigenes Netz, Firewallregel)?** | Die Kassen hängen im WLAN und im Laden gibt es ein Gäste-WLAN. Wenn beide im gleichen Netz 192.168.10.0 liegen, können Gäste die Kassen und den Server erreichen. Das ist ein grosses Sicherheitsrisiko, gerade weil an den Kassen Kartenzahlungen laufen. |
| **4** | **Welcher externe Partner macht die Fernwartung, wie erreiche ich ihn, und wie greift er technisch zu (VPN, TeamViewer, Portweiterleitung)?** | Das kann ich nirgends nachlesen. Ich muss wissen, wer von aussen Zugriff auf unser Netz hat, um die Sicherheit beurteilen zu können, und ich brauche den Kontakt, wenn die Warenwirtschaft ausfällt. Offene Portweiterleitungen sind ein typisches Einfallstor. |
| **5** | **Wer verteilt die Adressen per DHCP (Router oder Server), welcher Bereich ist reserviert, und welche Geräte haben feste Adressen?** | Ich brauche das IP-Konzept für die Fehlersuche und für Erweiterungen, sonst vergebe ich beim nächsten Gerät eine Adresse doppelt. Einzelne Adressen kann ich zwar selbst auslesen, aber nicht, welche Bereiche bewusst reserviert wurden. |

### Warum diese Reihenfolge?

- **Prio 1** öffnet die Tür zu allen Geräten. Mit den Zugangsdaten kann ich Lücken wie Maske, Portbelegung, WLAN-Verschlüsselung oder Firewallregeln selber nachschauen.
- **Prio 2 bis 4** sind Informationen, die nur die Administratorin kennt und die bei Verlust das grösste Risiko darstellen (Datenverlust, Sicherheitslücke, kein Support).
- **Prio 5** könnte ich mit viel Aufwand selbst rekonstruieren, die Antwort spart mir aber viel Zeit.

Bewusst nicht gefragt habe ich nach der Subnetzmaske, der Server-IP oder der IP des Etikettendruckers. Das finde ich mit `ipconfig /all`, `arp -a` oder einem Scan in wenigen Minuten selbst heraus.

## 3. Bonus: Welche Folgen hätte es, wenn ich eine Antwort nicht erhalte?

| Prio | Fehlende Information | Mögliche Folgen |
|---|---|---|
| 1 | Zugangsdaten | Router, Switches und APs müssten auf Werkseinstellungen zurückgesetzt und komplett neu eingerichtet werden. Das Netz steht während dieser Zeit still, die Kassen im Laden funktionieren nicht. Beim Server droht im schlimmsten Fall der Verlust des Zugriffs auf die Domäne. |
| 2 | Backup | Man merkt erst im Ernstfall, dass das Backup seit Monaten nicht läuft. Datenverlust bei Warenwirtschaft und Buchhaltung, bei einem Ransomware-Angriff droht die Erpressung. |
| 3 | Trennung Gäste-WLAN | Unerkannte Sicherheitslücke: Gäste oder ein infiziertes Smartphone erreichen Kassen und Server. Probleme mit den Sicherheitsvorgaben für Kartenzahlungen und dem Datenschutz. |
| 4 | Fernwartung | Bei einem Ausfall der Warenwirtschaft weiss niemand, wen man anrufen muss. Ein unbekannter Fernzugriff bleibt offen, eventuell mit einem Standardpasswort. |
| 5 | DHCP und IP-Konzept | Doppelte IP-Adressen bei Erweiterungen, längere Fehlersuche, Drucker oder Kassen sind plötzlich nicht mehr erreichbar. |

## 4. Fazit

Die Beschreibung zeigt, welche Geräte vorhanden sind, aber kaum, wie sie konfiguriert sind und warum. Es fehlen vor allem Zugangsdaten, Betriebsinformationen (Backup, Fernwartung) und die Sicherheitsüberlegungen (Netztrennung). Genau diese Punkte gehören in die Bestandteile **Konfigurationsinformationen** und **Designentscheide** einer vollständigen Netzwerkdokumentation, die hier praktisch komplett fehlen.
