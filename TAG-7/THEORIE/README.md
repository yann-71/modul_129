# Theorie TAG-7: Netzwerkdokumentation

## Wozu eine Netzwerkdokumentation?

Die Leitfrage aus dem Unterricht: *Was passiert, wenn die Person, die das Netzwerk eingerichtet hat, nicht mehr da ist?* Ohne Dokumentation steckt das Wissen nur im Kopf einer einzelnen Person. Fällt diese aus, muss jede Störung und jede Erweiterung zuerst mühsam rekonstruiert werden.

Eine Netzwerkdokumentation wird gebraucht für:

| Zweck | Beispiel |
|---|---|
| Betrieb | Wer darf was, wo steht welches Gerät, wie komme ich auf die Konfiguration? |
| Fehlersuche | Soll-Zustand mit Ist-Zustand vergleichen: Welche IP sollte der Drucker haben, in welchem VLAN ist Port 5? |
| Erweiterung | Wie viele Adressen sind im Client-Netz noch frei, welche Switchports sind belegt? |
| Neuaufbau / Notfall | Ein defekter Switch kann anhand der dokumentierten Konfiguration schnell ersetzt werden. |
| Übergabe | Eine neue Fachperson oder eine externe Firma kann das Netz übernehmen. |
| Sicherheit / Audit | Welche Netze sind getrennt, wer hat Zugriff, welche Firmware läuft? |

**Merksatz:** Eine Netzwerkdokumentation muss so gut sein, dass eine fremde Fachperson das Netzwerk verstehen, betreiben und erweitern kann.

## Die fünf Bestandteile

| Nr. | Bestandteil | Inhalt | Beantwortet die Frage |
|---|---|---|---|
| 1 | Netzwerkschema | logisches Layout (Netze, VLANs, IP, Gateways) und physischer Verkabelungsplan (Geräte, Ports, Kabel, Racks) | Wie hängt alles zusammen? |
| 2 | Inventar | Gerät, Typ/Modell, Standort, Seriennummer, Firmware, Garantie, Zuständigkeit | Was steht wo? |
| 3 | IP-Adressplan | Netze und Masken, statische Adressen, DHCP-Bereiche, Reserven | Welche Adresse gehört wem? |
| 4 | Konfigurationsinformationen | Hostname, VLANs, Portbelegung, Trunks, Routen, DHCP, Management-IP, Backups der Konfigurationen | Was ist auf welchem Gerät eingestellt? |
| 5 | Designentscheide | Begründungen in Frage/Antwort-Form | Warum ist es so gebaut? |

### Logisches und physisches Netzwerkschema

| Logisches Schema | Physisches Schema |
|---|---|
| zeigt Netze, VLANs, Subnetze, IP-Adressen, Gateways und Routing | zeigt Geräte, Ports, Kabel, Dosen, Racks und Räume |
| beantwortet: Wer kann mit wem kommunizieren? | beantwortet: Welches Kabel steckt wo? |
| wird für Fehlersuche ab Schicht 3 gebraucht | wird für Fehlersuche auf Schicht 1/2 gebraucht |

Ein gutes Schema enthält die **Interface-Namen auf beiden Seiten** einer Verbindung (z. B. `Gi1/0/1 - Gi0/1`), die Netzadressen mit Präfix und eine Legende für Symbole und Farben.

## Designentscheide dokumentieren

Designentscheide sind der Teil, der in der Praxis am häufigsten fehlt, aber am meisten hilft. Die Konfiguration zeigt nur **was** eingestellt ist, die Designentscheide erklären **warum**. Ohne Begründung traut sich später niemand, etwas zu ändern, oder jemand ändert etwas, das absichtlich so gebaut wurde.

Typische Designentscheide:

- Warum wurden VLANs eingeführt und warum genau diese? (Sicherheit, Broadcast-Domänen verkleinern, Gäste isolieren)
- Warum wurde ein bestimmter IP-Bereich gewählt? (privater Bereich, kein Konflikt mit typischen Heimnetzen bei VPN, Erweiterungsreserve)
- Warum /24 und nicht kleiner? (Übersicht, Reserve, das dritte Oktett entspricht der VLAN-Nummer)
- Warum macht der Router das Inter-VLAN-Routing und nicht der Switch?
- Warum ist der Drucker in einem eigenen VLAN?

## Gute Praxis

- **Namenskonzept:** sprechende, einheitliche Hostnamen (`SW-CORE01`, `SW-ACC01`, `RTR01`), Schema auch für Dosen und Patchpanel.
- **Eine Quelle der Wahrheit:** Dokumentation an einem zentralen Ort (Wiki, Git-Repo, NetBox), nicht verteilt in Excel-Dateien auf verschiedenen Laptops.
- **Aktuell halten:** jede Änderung wird nachgeführt (Änderungsprotokoll mit Datum, Person, Grund). Eine veraltete Dokumentation ist gefährlich, weil man ihr vertraut.
- **Keine Passwörter im Klartext:** in der Dokumentation steht nur, *wo* die Zugangsdaten liegen (z. B. Passwortmanager, Eintrag "RTR01").
- **Konfigurations-Backups:** die Running-Config der Netzwerkgeräte wird regelmässig gesichert, damit ein Ersatzgerät schnell eingerichtet ist.
- **Zielgruppe beachten:** eine Fachperson muss die Dokumentation ohne Rückfragen verstehen.

## Werkzeuge

| Werkzeug | Einsatz |
|---|---|
| draw.io / Visio | Netzwerkschema zeichnen |
| Mermaid (in Markdown) | einfache Schemas direkt im Git-Repo |
| Markdown / Wiki | Inventar, IP-Plan, Designentscheide |
| NetBox, phpIPAM | IP-Adressverwaltung und Inventar als Datenbank |
| Packet Tracer | Netz nachbauen und Konfiguration testen |
