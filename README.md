# Cisco Config Anonymizer

Browser-basiertes Tool zum Anonymisieren von Cisco-Konfigurationen (IOS · IOS-XE · WLC).

**🔒 Keine Daten verlassen den Browser** — alles läuft lokal, kein Server, keine Cloud.

## Features

- **Automatische Erkennung** von kundenspezifischen Daten:
  - IP-Adressen (Interface, BGP, HSRP, DHCP-Relay, Tunnel, ACL, …)
  - Hostnamen & Domainnamen
  - Passwörter, Secrets & Keys (Enable, Username, Line, TACACS, RADIUS, VPN)
  - SNMP Community Strings
  - BGP AS-Nummern & Neighbor-IPs
  - VLAN-Namen
  - NTP-, Syslog-, DNS-, RADIUS-Server
  - RADIUS Server-Namen & Referenzen
  - Cisco DNA Token
  - Benutzernamen (username, dot1x)
  - IP Host Einträge
  - Allgemeiner IP-Scan für verbleibende Adressen

- **Globales Wörterbuch**: Gleicher Wert → gleicher Platzhalter, dateiübergreifend
- **Merge-Funktion**: Anonymisierte Config mit Original-Daten wiederherstellen
- **Auto-Save**: Datendatei automatisch im Browser gespeichert (kein manueller Download nötig)
- **Platzhalterformat**: `*HOSTNAME_001*` — gut sichtbar in der Config

## Verwendung

1. [**→ Tool öffnen**](https://dmichel999.github.io/Config-anonymisieren-/)
2. Cisco-Konfiguration laden (Drag & Drop oder Einfügen)
3. „Analysieren" klicken
4. Erkannte Werte prüfen, ggf. deaktivieren
5. Anonymisierte Config herunterladen

### Merge (Originaldaten wiederherstellen)

1. Tab „Merge" öffnen
2. Anonymisierte Config laden → Datendatei wird automatisch erkannt
3. „Merge & Herunterladen" klicken

## Datenschutz

Alle Verarbeitungsschritte finden ausschließlich im Browser statt.  
Es werden keine Daten an externe Server übertragen.
