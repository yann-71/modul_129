# Aufgaben TAG-4

| Datei | Inhalt |
|---|---|
| [challenge-4-spanning-tree.md](challenge-4-spanning-tree.md) | **Challenge 4:** Wer wird Root Bridge? |
| [screenshots/](screenshots/) | Screenshots aus Packet Tracer |

## Challenge 4 in Kürze

Drei Switches sind im Dreieck redundant verbunden, an jedem hängt ein PC. Mit individuell zugewiesenen Prioritäten (SW1 16384, SW2 32768, SW3 12288) habe ich bestimmt, welcher Switch Root Bridge wird, welche Ports Root-, Designated- und Blocked-Ports sind, was bei einem Root-Bridge-Wechsel passiert und wie sich ein vierter Switch auswirkt. Zum Schluss beurteile ich die Aussage, drei Kabel würden mehr Bandbreite bringen.

**Noch offen:** Die rechnerischen Vorhersagen muss ich noch mit den echten Ausgaben von `show spanning-tree` und den Ping-Tests aus Packet Tracer belegen. Die entsprechenden Stellen sind in der Challenge markiert.
