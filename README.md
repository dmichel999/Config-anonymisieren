# Cisco Config Anonymizer

Browser-basiertes Tool zum Anonymisieren von Cisco-Konfigurationen (IOS · IOS-XE · WLC).

**🔒 Keine Daten verlassen den Browser** — alles läuft lokal, kein Server, keine Cloud.

---

## Funktionen im Überblick

### Automatische Erkennung

Das Tool erkennt kundenspezifische Daten automatisch:

| Kategorie | Erkannte Kontexte |
|---|---|
| **IP-Adressen** | Interface, BGP Neighbor, HSRP/VRRP, DHCP-Relay, Tunnel, ACL, IP-Route, RADIUS/TACACS Server, Network-Statements |
| **DHCP Relay** | `ip helper-address` — bekommt bewusst einen eigenen Platzhalter, getrennt von DNS/anderen IP-Kontexten |
| **Hostnamen & Domainnamen** | `hostname`, `ip domain-name` |
| **Passwörter & Secrets** | Enable Secret/Password, Username-Secret, Line-Passwort, TACACS/RADIUS Key, VPN Pre-Shared Key, ISAKMP Key, NTP Auth-Key, FTP-Passwort, SSH Key-Hash |
| **Benutzernamen** | `username`, `ip ftp username`, `dot1x username`, SSH Pubkey-Chain (`username` eingerückt) |
| **SNMP** | Community Strings |
| **BGP** | AS-Nummern, Neighbor-IPs |
| **VLAN-IDs** | `vlan <id>`, `interface Vlan<id>`, `switchport access/trunk/native vlan`, Ranges (`1-31`), `ip ftp/tftp source-interface Vlan<id>` |
| **VLAN-Namen** | `name` unter `vlan`-Block |
| **Interface Descriptions** | `description <text>` unter Interface |
| **VRF-Namen** | `ip vrf`, `vrf definition` |
| **ACL-Namen** | `ip access-list extended/standard` |
| **Server** | NTP, Syslog, DNS, RADIUS |
| **Weitere** | Cisco DNA Token, IP-Host-Einträge |

Jeder erkannte Wert bekommt einen eindeutigen Platzhalter im Format `*HOSTNAME_001*`.

**Wichtig:** DHCP-Relay-Adressen (`ip helper-address`) bekommen absichtlich eigene Platzhalter — auch wenn dieselbe IP anderswo als DNS-Server vorkommt. So sind keine Rückschlüsse auf die Netzwerkarchitektur möglich.

---

### Vorschau mit farbigen Platzhaltern

Nach der Analyse zeigt die **Vorschau** die anonymisierte Config mit farbig hervorgehobenen Platzhaltern — je nach Typ in einer anderen Farbe. So ist auf einen Blick erkennbar, was anonymisiert wurde und was noch sichtbar ist.

---

### Manuelles Anonymisieren direkt in der Vorschau

Wenn das Tool einen Wert nicht automatisch erkennt, kann er manuell anonymisiert werden:

1. In der Vorschau den gewünschten Text **markieren**
2. Der markierte Text erscheint im Eingabefeld der Toolbar (kann dort noch korrigiert werden)
3. **Typ** aus dem Dropdown wählen (z. B. Hostname, IP-Adresse, Passwort, VLAN ID, Beschreibung …)
4. **„🔒 Anonymisieren"** klicken

Der Wert wird sofort durch einen Platzhalter ersetzt und in der Vorschau aktualisiert.

---

### Lernfunktion — einmal anonymisieren, nie wieder vergessen

Manuell anonymisierte Werte werden im **globalen Wörterbuch** gespeichert (im Browser, dauerhaft). Bei der nächsten Konfiguration werden sie **automatisch erkannt und ersetzt** — ohne erneuten manuellen Eingriff.

Das ist besonders nützlich für projektspezifische Werte wie eigene Hostnamen, interne IP-Adressen oder Standortkürzel, die in vielen Configs vorkommen.

Gelernte Einträge werden in der Erkannte-Daten-Tabelle mit einem **🧠-Icon** markiert.

---

### Originalwert aufdecken

Platzhalter in der Vorschau können jederzeit aufgedeckt werden:

- **Klick** auf einen Platzhalter → zeigt den Originalwert
- **Nochmal klicken** → versteckt ihn wieder

Alternativ: Platzhalter **markieren** (nicht klicken) → Originalwert erscheint in der Toolbar.

---

### Erkannte Werte verwalten

In der **Erkannte-Daten-Tabelle** (neben der Vorschau) sind alle gefundenen Werte aufgelistet:

- Einzelne Einträge über das **✕** deaktivieren und aus dem Wörterbuch entfernen
- Werte nach Typ filtern (z. B. nur IPs anzeigen)

---

### Globales Wörterbuch & Merge

- **Globales Wörterbuch**: Gleicher Wert bekommt in allen Configs immer denselben Platzhalter — auch dateiübergreifend
- **Merge-Funktion**: Anonymisierte Config kann mit dem gespeicherten Wörterbuch wieder in die Original-Config zurückverwandelt werden (Tab „Merge")

---

### Gelernte Einträge verwalten (Wörterbuch-Tab)

Im Tab **„Wörterbuch"** sind alle gespeicherten Einträge sichtbar:

- Nach Name filtern
- Einzelne Einträge löschen
- Alle gelernten Einträge auf einmal löschen („🗑 Gelernte löschen")

---

## Verwendung

1. [**→ Tool öffnen**](https://dmichel999.github.io/Config-anonymisieren-/)
2. Cisco-Konfiguration einfügen oder per Drag & Drop laden
3. **„Analysieren"** klicken
4. Vorschau prüfen — fehlende Werte manuell markieren und anonymisieren
5. Anonymisierte Config herunterladen

### Merge (Originaldaten wiederherstellen)

1. Tab **„Merge"** öffnen
2. Anonymisierte Config laden → Datendatei wird automatisch erkannt
3. **„Merge & Herunterladen"** klicken

---

## Datenschutz

Alle Verarbeitungsschritte finden ausschließlich im Browser statt.  
Es werden keine Daten an externe Server übertragen.  
Das Wörterbuch wird lokal im Browser gespeichert (`localStorage`) und verlässt das Gerät nicht.
