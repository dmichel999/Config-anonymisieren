# Cisco Config Anonymizer

Browser-basiertes Tool zum Anonymisieren von Cisco-Konfigurationen (IOS · IOS-XE · WLC).

**🔒 Keine Daten verlassen den Browser** — alles läuft lokal, kein Server, keine Cloud.

---

## Funktionen im Überblick

### Automatische Erkennung

Das Tool erkennt kundenspezifische Daten automatisch:

| Kategorie | Beispiele |
|---|---|
| IP-Adressen | Interface, BGP, HSRP, DHCP-Relay, Tunnel, ACL, … |
| Hostnamen & Domainnamen | `hostname`, `ip domain-name` |
| Passwörter & Secrets | Enable, Username, Line, TACACS, RADIUS, VPN, NTP Auth-Keys |
| SNMP | Community Strings |
| BGP | AS-Nummern, Neighbor-IPs |
| VLAN-Namen | `name` unter `vlan` |
| VRF-Namen | `ip vrf`, `vrf definition` |
| ACL-Namen | `ip access-list extended/standard` |
| Server | NTP, Syslog, DNS, RADIUS |
| Weitere | Cisco DNA Token, Benutzernamen, IP-Host-Einträge |

Jeder erkannte Wert bekommt einen eindeutigen Platzhalter im Format `*HOSTNAME_001*`.

---

### Vorschau mit farbigen Platzhaltern

Nach der Analyse zeigt die **Vorschau** die anonymisierte Config mit farbig hervorgehobenen Platzhaltern — je nach Typ in einer anderen Farbe. So ist auf einen Blick erkennbar, was anonymisiert wurde und was noch sichtbar ist.

---

### Manuelles Anonymisieren direkt in der Vorschau

Wenn das Tool einen Wert nicht automatisch erkennt, kann er manuell anonymisiert werden:

1. In der Vorschau den gewünschten Text **markieren**
2. Der markierte Text erscheint in der Toolbar
3. **Typ** aus dem Dropdown wählen (z. B. Hostname, IP-Adresse, Passwort)
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
