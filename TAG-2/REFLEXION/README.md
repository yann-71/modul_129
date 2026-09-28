# Reflexion TAG-2

**Datum:** 24.08.2026

## Mein Lernertrag

Das OSI-Modell kann ich jetzt nicht nur auswendig aufsagen, sondern auch anwenden, also sagen, auf welcher Schicht ein Gerät oder ein Protokoll arbeitet und wo ein Fehler liegen könnte. Sehr geholfen hat mir der Vergleich mit dem TCP/IP-Modell, weil man in der Praxis eher mit diesem arbeitet.

Richtig spannend war ARP. Vorher war für mich einfach klar, dass ein Ping funktioniert. Jetzt weiss ich, dass vorher ein ARP-Request und ein Reply laufen müssen, und dass bei einem Ziel in einem fremden Subnetz die MAC-Adresse des Routers im Cache landet und nicht die des Zielgeräts. In Wireshark habe ich das auch tatsächlich so gesehen, das hat die Theorie bestätigt.

## Meine Herausforderungen

Wireshark war am Anfang sehr unübersichtlich, weil im Schul-WLAN extrem viel Verkehr läuft. Ich musste zuerst lernen, mit Filtern zu arbeiten, sonst findet man das passende Paket gar nicht. Auch der Vergleich der ARP-Caches war schwierig, weil in einem so grossen Netz laufend Einträge dazukommen und wieder verschwinden.

Noch nicht ganz klar ist mir, wie lange ein ARP-Eintrag unter Windows genau gültig bleibt, dort finde ich unterschiedliche Angaben. Ausserdem möchte ich Dynamic ARP Inspection gerne einmal an einem echten Switch sehen.

## Mein Lernprozess

Ich habe zuerst die Theorie durchgearbeitet und danach direkt am eigenen Notebook mitgeschnitten. Dieses Vorgehen war gut, weil ich die Theorie sofort im echten Datenverkehr wiedererkannt habe. Die Screenshots habe ich direkt beim Arbeiten gemacht und nicht erst nachträglich, das hat mir beim Dokumentieren viel Zeit gespart.

Beim nächsten Mal will ich die Filter von Anfang an setzen, statt zuerst minutenlang durch die ganze Aufzeichnung zu scrollen. Ausserdem möchte ich mir angewöhnen, bei jedem Screenshot gleich zu notieren, was genau darauf zu sehen ist.
