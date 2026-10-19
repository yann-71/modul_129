# Challenge 8 - Supportticket analysieren

**Modul:** M129, Unterrichtsblock 8
**Datum:** 26.10.2026

Die Challenge wurde mit dem vorgegebenen KI-Prompt individuell generiert. Zuerst der Prompt und das erhaltene Supportticket, danach meine Analyse.

```text
Du bist Netzwerkadministrator und Prüfer im Modul 129.
Erstelle ein realistisches Netzwerkproblem für Lernende der ICT-Berufsbildung.
Vorgaben:
- Das Problem soll in einem kleinen LAN auftreten.
- Verwende IPv4.
- Beschreibe die Netzwerktopologie in Textform.
- Nenne die Konfiguration von mindestens zwei Geräten.
- Baue genau einen Fehler ein.
- Verrate die Lösung nicht.
- Gib die Informationen in Form eines Supporttickets aus.
Erstelle ein anderes Szenario als üblich.
Gib keine Lösungen oder Hinweise bekannt.
```

---

# Erhaltenes Supportticket

```text
Ticket-Nr.:    INC-2026-10-0412
Kunde:         Käserei Bergblick GmbH, Wattwil (12 Mitarbeitende)
Gemeldet von:  Martin Gähwiler, Käsermeister
Priorität:     Hoch
Kategorie:     Netzwerk / Überwachung

Beschreibung:
Seit Dienstagmorgen zeigt die Überwachungssoftware "CellarWatch" auf dem
Büro-PC für den Reifekeller keine neuen Temperatur- und Feuchtigkeitswerte
mehr an. Die Werte bleiben auf dem Stand von Montag, 16:42 Uhr, stehen.
Die Software meldet "Gateway nicht erreichbar". Wenn die Werte nicht
überwacht werden, verlieren wir im schlimmsten Fall eine ganze Charge
Käse. Am Montagnachmittag hat der Elektriker im Keller die Wand neu
verputzt und dabei die Netzwerkdose versetzt. Das Sensor-Gateway hat er
danach wieder eingesteckt, die grüne LED am Gateway leuchtet. Internet,
Mail, Drucker und das Kassensystem im Hofladen funktionieren normal.

Topologie:
- Router (Internet, Inter-VLAN-Routing) im Büro, verbunden mit SW-BUERO Gi0/1
- SW-BUERO (Cisco 2960, 24 Ports) im Büro
  - Fa0/1-12:  Büro-PCs, Drucker, NAS
  - Fa0/13-16: Hofladen (Kasse, Kunden-WLAN-AP)
  - Fa0/15 führt über das Patchpanel (PP-15) zur neuen Dose im Keller
  - Fa0/7  führte bisher über das Patchpanel (PP-07) zur alten Dose im Keller
- Büro-PC "PC-KAESEREI" mit CellarWatch an Fa0/3
- Sensor-Gateway "KELLER-GW" (Modbus/TCP, 4 Funksensoren) im Reifekeller

Netze:
- VLAN 10 BUERO:   192.168.10.0/24, Gateway 192.168.10.1
- VLAN 20 LADEN:   192.168.20.0/24, Gateway 192.168.20.1

Konfiguration PC-KAESEREI:
  IP-Adresse:    192.168.10.21
  Subnetzmaske:  255.255.255.0
  Gateway:       192.168.10.1
  DNS:           192.168.10.1
  CellarWatch:   Gateway-Adresse 192.168.10.50, Port 502

Konfiguration KELLER-GW (Weboberfläche, über Notebook direkt ausgelesen):
  IP-Adresse:    192.168.10.50 (statisch)
  Subnetzmaske:  255.255.255.0
  Gateway:       192.168.10.1
  Modbus/TCP:    aktiv, Port 502

Auszug Konfiguration SW-BUERO:
  interface FastEthernet0/3
   switchport mode access
   switchport access vlan 10
  interface FastEthernet0/7
   switchport mode access
   switchport access vlan 10
  interface FastEthernet0/13
   switchport mode access
   switchport access vlan 20
  interface FastEthernet0/14
   switchport mode access
   switchport access vlan 20
  interface FastEthernet0/15
   switchport mode access
   switchport access vlan 20
  interface FastEthernet0/16
   switchport mode access
   switchport access vlan 20
  interface GigabitEthernet0/1
   switchport mode trunk
```

---

# Meine Analyse

## 1. Welches Symptom liegt vor?

- Die Software CellarWatch auf PC-KAESEREI (192.168.10.21) erreicht das Sensor-Gateway KELLER-GW (192.168.10.50) nicht mehr. Die letzten Messwerte stammen von Montag, 16:42 Uhr.
- Das Gateway selbst scheint zu laufen (grüne LED, Konfiguration über das Notebook auslesbar) und hat eine Link-Verbindung.
- Alle anderen Dienste (Internet, Mail, Drucker, Kasse) funktionieren. Der Fehler betrifft also nur dieses eine Gerät.
- Zeitlicher Zusammenhang: Das Problem begann direkt nach den Arbeiten des Elektrikers, der die Netzwerkdose versetzt hat.

## 2. Auf welcher OSI-Schicht vermute ich den Fehler?

**Schicht 2 (Sicherung), genauer die VLAN-Zuordnung des Switchports.**

Begründung nach dem Ausschlussverfahren:

| Schicht | Überlegung | Ergebnis |
|---|---|---|
| 1 | Die LED am Gateway leuchtet grün, es gibt also einen Link zum Switch. | eher ausgeschlossen |
| 2 | Die neue Dose hängt über PP-15 an Port Fa0/15. Dieser Port gehört laut Konfiguration zu **VLAN 20 (LADEN)**, die alte Dose an Fa0/7 war in VLAN 10. | **verdächtig** |
| 3 | IP 192.168.10.50/24 und Gateway 192.168.10.1 des Gateways passen zum Netz von PC-KAESEREI. Am Gerät wurde nichts geändert. | korrekt konfiguriert |
| 4-7 | CellarWatch hat bis Montag funktioniert, Adresse und Port 502 stimmen mit dem Gateway überein. | ausgeschlossen |

## 3. Welche Prüfungen würde ich durchführen?

| Nr. | Prüfung | Befehl / Ort | Erwartetes Ergebnis bei meiner Vermutung |
|---|---|---|---|
| 1 | Erreichbarkeit vom PC aus | `ping 192.168.10.50` auf PC-KAESEREI | `Request timed out` |
| 2 | ARP-Eintrag prüfen | `arp -a` auf PC-KAESEREI nach dem Ping | kein Eintrag für 192.168.10.50 |
| 3 | Port-Status und VLAN | `show interfaces status` auf SW-BUERO | Fa0/15 `connected`, VLAN 20 |
| 4 | VLAN-Zuordnung | `show vlan brief` | Fa0/15 ist in VLAN 20 (LADEN) aufgeführt |
| 5 | Wo hängt die MAC des Gateways? | `show mac-address-table address <MAC KELLER-GW>` | MAC an Fa0/15 in **VLAN 20** |
| 6 | Verkabelung kontrollieren | Patchpanel im Büro, Beschriftung PP-15 | Kabel der neuen Kellerdose steckt an Fa0/15 |
| 7 | Gegenprobe | Notebook mit IP aus 192.168.20.0/24 an einen freien Port in VLAN 20, Ping auf 192.168.10.50 | geht ebenfalls nicht, da das Gateway im falschen VLAN mit einer Adresse aus VLAN 10 isoliert ist |

## 4. Was ist die wahrscheinlichste Ursache?

Der Elektriker hat die neue Kellerdose über das Patchpanel PP-15 an den Switchport **Fa0/15** angeschlossen. Dieser Port ist als Access-Port im **VLAN 20 (LADEN)** konfiguriert, weil er zum Bereich Hofladen (Fa0/13-16) gehört. Das Sensor-Gateway hat aber eine statische Adresse aus dem Büronetz VLAN 10 (192.168.10.50).

Dadurch befindet sich das Gateway physisch in der Broadcast-Domäne von VLAN 20. Die ARP-Anfrage von PC-KAESEREI für 192.168.10.50 wird nur in VLAN 10 verteilt und erreicht das Gateway nie. Der Router kann auch nicht helfen, weil das Gateway aus seiner Sicht eine Adresse aus VLAN 10 hat, aber in VLAN 20 sitzt. Der Link ist aktiv (grüne LED), deshalb sieht von aussen alles normal aus.

## 5. Wie würde ich den Fehler beheben?

**Variante A (bevorzugt): den Port ins richtige VLAN legen.** Fa0/15 wird für den Keller gebraucht, also wird er dem Büronetz zugeordnet:

```text
SW-BUERO# configure terminal
SW-BUERO(config)# interface fa0/15
SW-BUERO(config-if)# description KELLER-GW Reifekeller (PP-15)
SW-BUERO(config-if)# switchport mode access
SW-BUERO(config-if)# switchport access vlan 10
SW-BUERO(config-if)# end
SW-BUERO# copy running-config startup-config
```

**Variante B:** das Patchkabel PP-15 auf den bisherigen Port Fa0/7 (VLAN 10) umstecken. Das ist schneller, aber nur sinnvoll, wenn Fa0/7 nicht inzwischen anders belegt ist.

Ich würde Variante A wählen, weil damit der Switch zur neuen Verkabelung passt und die Port-Beschreibung gleich dokumentiert, was angeschlossen ist. Dafür muss geprüft werden, dass Fa0/15 im Hofladen nicht gebraucht wird. Sonst wäre Variante B die richtige Wahl.

**Kontrolle nach der Behebung:**

1. `show vlan brief`: Fa0/15 in VLAN 10.
2. `ping 192.168.10.50` von PC-KAESEREI erfolgreich, `arp -a` zeigt die MAC des Gateways.
3. CellarWatch zeigt wieder aktuelle Messwerte.
4. Der Kunde prüft die Temperaturkurve seit Montag, um zu sehen, ob die Lücke in der Überwachung ein Risiko für die Charge war.

**Dokumentation und Vorbeugung:**

- Netzwerkdokumentation nachführen: Dose Keller neu an PP-15 / Fa0/15, VLAN 10. Fa0/7 als frei markieren und abschalten.
- Ports am Switch mit `description` beschriften, damit sofort sichtbar ist, welcher Port wohin führt.
- Bei Arbeiten von Drittfirmen an der Verkabelung die IT vorher informieren und danach einen Funktionstest machen.
- Die Überwachungssoftware sollte bei Verbindungsverlust aktiv alarmieren (Mail oder SMS), statt nur eine Meldung auf dem Büro-PC anzuzeigen.
