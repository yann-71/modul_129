# Auftrag 1 und 2 - Warum eine Netzwerkdokumentation?

**Modul:** M129, Unterrichtsblock 7
**Datum:** 19.10.2026

---

## Auftrag 1: Warum eine Netzwerkdokumentation?

Leitfragen: *"Was passiert, wenn die Person, die das Netzwerk eingerichtet hat, nicht mehr da ist?"* und *"Wenn ihr morgen eine fremde Firma übernehmen müsstet: Welche Informationen bräuchtet ihr?"*

### Was passiert ohne Dokumentation?

- Bei einer Störung weiss niemand, wie der Soll-Zustand aussieht. Man sucht dann nicht den Fehler, sondern zuerst einmal, wie das Netz überhaupt aufgebaut ist.
- Zugangsdaten zu Router, Switches und WLAN fehlen. Im schlimmsten Fall müssen Geräte zurückgesetzt und neu konfiguriert werden.
- Bei Erweiterungen werden IP-Adressen doppelt vergeben oder Ports im falschen VLAN belegt.
- Verträge (Internetanschluss, Support, Lizenzen) laufen aus, ohne dass es jemand bemerkt.
- Die Firma ist von einer einzigen Person abhängig. Das ist ein Single Point of Failure, nur eben organisatorisch statt technisch.

### Was ich bei der Übernahme einer fremden Firma wissen möchte

| Bereich | Benötigte Information |
|---|---|
| Überblick | Netzwerkschema logisch und physisch, Standorte, Räume, Racks |
| Geräte | Inventar mit Modell, Standort, Seriennummer, Firmware, Garantie |
| Adressen | IP-Adressplan, Subnetze, VLANs, DHCP-Bereiche, DNS |
| Konfiguration | Hostname, Portbelegung, Trunks, Routing, Backups der Running-Config |
| Zugänge | wo die Zugangsdaten liegen (Passwortmanager), wer Admin-Rechte hat |
| Extern | Provider, Vertragsnummer, statische öffentliche IP, Support-Hotline |
| Sicherheit | Firewallregeln, Gast-WLAN, Fernzugriff (VPN) |
| Betrieb | Backupkonzept, Wartungsfenster, bekannte Probleme, Änderungsprotokoll |
| Hintergrund | Designentscheide: warum ist es so gebaut? |

### Ergebnis der Gruppendiskussion

In der Gruppe waren wir uns schnell einig, dass ein Netzwerkschema und eine Liste der IP-Adressen das Minimum sind. Diskutiert haben wir vor allem zwei Punkte:

1. **Gehören Passwörter in die Dokumentation?** Nein, nicht im Klartext. Die Dokumentation sagt nur, wo die Zugangsdaten liegen (z. B. Eintrag im Passwortmanager). Sonst ist die Dokumentation selbst ein Sicherheitsrisiko.
2. **Wie detailliert muss es sein?** So detailliert, dass eine Fachperson ohne Rückfragen arbeiten kann. Jede einzelne Netzwerkdose aufzuschreiben ist aufwendig, spart aber bei der Fehlersuche auf Schicht 1 viel Zeit.

---

## Auftrag 2: Die fünf Bestandteile einer Netzwerkdokumentation

| Nr. | Bestandteil | Was gehört dazu | Wozu dient es |
|---|---|---|---|
| 1 | **Netzwerkschema** | logisches Layout und Verkabelungsplan: Geräte, Interface-Namen, IP-Adressen, Subnetze, VLANs | schneller Überblick, Grundlage jeder Fehlersuche |
| 2 | **Inventar** | Gerät, Typ, Standort, ergänzt durch Seriennummer, Firmware, Garantie | Präzisierungen, die im Schema keinen Platz haben, Ersatzbeschaffung |
| 3 | **IP-Adressplan** | statische Adressen für Server, Drucker, Netzwerkkomponenten, DHCP-Bereiche | Adresskonflikte vermeiden, Erweiterungen planen |
| 4 | **Konfigurationsinformationen** | Hostname, VLANs, Management-IP, Portbelegung | Gerät nach einem Defekt identisch wieder aufsetzen |
| 5 | **Designentscheide** | Fragen und Antworten: Warum VLAN 20? Warum /24? | Überlegungen nachvollziehen, Ergänzungen richtig planen |

Die Bestandteile bauen aufeinander auf: Das **Schema** zeigt den Zusammenhang, das **Inventar** und der **IP-Plan** liefern die Details, die **Konfiguration** beschreibt den Ist-Zustand der Geräte und die **Designentscheide** erklären, warum dieser Zustand gewollt ist.

Am wichtigsten finde ich die Designentscheide. Eine Konfiguration kann man zur Not mit `show running-config` vom Gerät auslesen, die Überlegungen dahinter aber nicht.

**Merksatz:** Eine Netzwerkdokumentation muss so gut sein, dass eine fremde Fachperson das Netzwerk verstehen, betreiben und erweitern kann.
