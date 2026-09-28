# Theorie TAG-5: Routing

## Weshalb Routing

Durch Subnetting wird der direkte Verkehr zwischen den Subnetzen getrennt. Damit die Subnetze trotzdem miteinander kommunizieren können, braucht es ein Bindeglied, den Router. Ein Switch reicht nicht, weil er auf Layer 2 arbeitet und nur MAC-Adressen kennt.

## Ablauf einer Übertragung

1. Der PC vergleicht mit seiner Subnetzmaske, ob das Ziel im eigenen Netz liegt.
2. Liegt es im eigenen Netz, schickt er direkt (ARP auf die Ziel-MAC).
3. Liegt es in einem fremden Netz, schickt er das Paket an sein Standardgateway.
4. Der Router sucht in seiner Routingtabelle den passenden Eintrag und leitet über das richtige Interface weiter.
5. Bei jedem Hop werden die MAC-Adressen neu gesetzt, die IP-Adressen bleiben gleich und die TTL wird um 1 reduziert.

Erreicht die TTL den Wert 0, wird das Paket verworfen und der Router meldet dies mit ICMP zurück. Das verhindert endlose Schleifen und wird von traceroute bzw. tracert ausgenutzt.

## Die Routingtabelle

Eine Routingtabelle enthält pro Eintrag: Zielnetz mit Maske, Next Hop (oder "direkt"), Metrik und Ausgangsinterface.

| Zielnetz | Next Hop | Metrik | Interface |
|---|---|---|---|
| 192.168.1.0/26 | direkt | 0 | Fa0/0 |
| 192.168.1.64/26 | 192.168.1.130 | 1 | Se2/0 |
| 0.0.0.0/0 | 203.0.113.1 | unknown | Fa1/0 |

Passen mehrere Einträge, gewinnt immer der spezifischste, also der mit dem längsten Präfix (Longest Prefix Match). Die Defaultroute 0.0.0.0/0 passt auf alles und wird nur genommen, wenn kein spezifischerer Eintrag existiert. Sie zeigt typischerweise Richtung Internet.

Wichtig: Ein Ping funktioniert nur, wenn **Hin- und Rückweg** korrekt konfiguriert sind.

## Statisches und dynamisches Routing

| | Statisch | Dynamisch |
|---|---|---|
| Einträge | von Hand | automatisch über ein Routingprotokoll |
| Ausfall | keine automatische Umleitung | automatische Neuberechnung |
| Aufwand | steigt mit der Netzgrösse | einmalig konfigurieren |
| Last | keine | etwas Bandbreite und CPU |
| Sinnvoll bei | kleinen, stabilen Netzen, Stub-Netzen, Defaultroute zum ISP | grösseren Netzen mit redundanten Wegen |

## RIP

RIP (Routing Information Protocol) ist ein Distanzvektorprotokoll und eignet sich gut zum Einstieg.

- Metrik ist der Hop Count, maximal 15 Hops, 16 gilt als unerreichbar.
- Updates alle 30 Sekunden, Route nach 180 Sekunden invalid, nach weiteren 120 Sekunden gelöscht.
- Transport über **UDP Port 520**.
- RIPv1 ist classful und sendet per Broadcast, RIPv2 ist classless (Maske wird mitgeschickt) und sendet per Multicast an 224.0.0.9.
- Split Horizon verhindert Schleifen: Eine gelernte Route wird nicht dorthin zurückgemeldet, woher sie kam.

Alternative Protokolle: OSPF und IS-IS (Link-State), EIGRP (Cisco, hybrid) sowie BGP als EGP zwischen autonomen Systemen.

## Typische Konfiguration auf einem Cisco-Router

```text
interface FastEthernet0/0
 ip address 192.168.1.1 255.255.255.192
 no shutdown
exit
ip route 192.168.1.64 255.255.255.192 192.168.1.130
ip route 0.0.0.0 0.0.0.0 203.0.113.1
end
copy running-config startup-config
```

Ohne `copy running-config startup-config` (kurz `write memory`) ist die Konfiguration nach dem Ausschalten weg. Bei einer seriellen Standleitung muss auf der DCE-Seite zusätzlich die Taktrate gesetzt werden (`clock rate 64000`).
