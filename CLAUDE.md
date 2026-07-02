# Config anonymisieren — Claude Code Projektkontext

Browser-Tool zum Anonymisieren von Cisco-Konfigurationen. Läuft vollständig im Browser, kein Server.

---

## URLs & Remotes

| | URL |
|---|---|
| **GitHub Repo** | `https://github.com/dmichel999/Config-anonymisieren` |
| **GitHub Pages (Live)** | `https://dmichel999.github.io/Config-anonymisieren-/` |
| **Gitea (intern)** | `https://gitea.home.internal/claude/Config-anonymisieren` |

### Git Remotes (lokal)

```bash
git remote -v
# origin  → https://<token>@github.com/dmichel999/Config-anonymisieren.git
# gitea   → https://claude:<token>@gitea.home.internal/claude/Config-anonymisieren.git
```

### Pushen

```bash
git push          # → GitHub (origin, default)
git push gitea    # → Gitea (intern)
# oder beide auf einmal:
git push && git push gitea
```

### Deployment

GitHub Pages deployed automatisch nach `git push origin main`. Die aktive Version ist `index.html` — für die neueste Version `index-v3.html` → `index.html` kopieren/ersetzen und pushen.

### Credentials

- **GitHub Token:** `~/.claude/access/github.md` (Homeserver-Projekt) — Berechtigungen: Contents R/W, Administration R/W, Pages R/W
- **Gitea Token:** `~/.claude/access/gitea.md` (Homeserver-Projekt) — User: `claude`
- **Gitea SSL:** `http.sslVerify=false` ist im lokalen Git-Config gesetzt (selbstsigniertes Zert)

### GitHub Repo-Metadaten via API aktualisieren

```bash
TOKEN=$(git remote get-url origin | grep -o 'gh[^@]*')

# Beschreibung + Homepage
curl -s -X PATCH "https://api.github.com/repos/dmichel999/Config-anonymisieren" \
  -H "Authorization: token $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"description": "...", "homepage": "..."}'

# Topics
curl -s -X PUT "https://api.github.com/repos/dmichel999/Config-anonymisieren/topics" \
  -H "Authorization: token $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"names":["cisco","anonymizer","network","privacy","browser-tool"]}'
```

---

## Features

- Automatische Erkennung: IPs, Hostnamen, Passwörter/Secrets, SNMP, BGP, VLAN-IDs/Namen, Interface Descriptions, NTP, RADIUS, DNA-Token, Benutzernamen, FTP/SSH-Pubkey-Credentials, VRF-Namen, ACL-Namen
- **DHCP-Relay Isolation** (`ip helper-address`): bekommt eigenen Platzhalter, nie geteilt mit DNS — kein Rückschluss auf Infrastruktur möglich (`isolate: true` Flag in RULES)
- Globales Wörterbuch (dateiübergreifend, gleicher Wert → gleicher Platzhalter)
- Merge-Funktion (anonymisierte Config mit Originaldaten wiederherstellen)
- Auto-Save via localStorage
- Platzhalterformat: `*HOSTNAME_001*`
- **Vorschau** mit farblich hervorgehobenen Platzhaltern (je Typ eigene Farbe)
- **Manuelles Anonymisieren** direkt in der Vorschau (Text markieren → erscheint im Input → Typ wählen → Anonymisieren)
- **Lernfunktion** — manuell anonymisierte Werte werden gespeichert und in zukünftigen Configs automatisch erkannt (🧠)
- **Originalwert aufdecken** — Klick auf Platzhalter in der Vorschau zeigt den Originalwert; nochmal klicken versteckt ihn
- **Gelernte Regeln verwalten** im Wörterbuch-Tab (filtern, einzeln oder alle löschen)

---

## Dateien

| Datei | Zweck |
|---|---|
| `index.html` | Aktive Version (GitHub Pages Einstiegspunkt) |
| `index-v2.html` | + Vorschau mit Highlighting + manuelles Anonymisieren |
| `index-v3.html` | + Lernfunktion + Originalwert aufdecken + Gelernte verwalten |
| `cisco-config-anonymizer.html` | Ältere Alternativversion |
| `README.md` | User-Doku (auf GitHub sichtbar) |

---

## Versionshistorie

| Version | Neue Features |
|---|---|
| `index.html` | Basisversion: Erkennung, Wörterbuch, Merge, Auto-Save |
| `index-v2.html` | Vorschau (Step 3) mit farbigen Platzhaltern; manuelles Anonymisieren per Textmarkierung |
| `index-v3.html` | Lernfunktion (🧠); Klick auf Platzhalter zeigt Originalwert; Wörterbuch-Tab mit Filter + "Gelernte löschen"-Button; manuelles Anonymisieren via Input-Feld (statt fragiler mouseup-State-Kette) |
| `index-v3.html` (Update 1) | Wildcard-Masken-Fix (`isMask()`): keine False Positives mehr bei ACL-Einträgen; neue Typen: VRF-Namen, ACL-Namen, NTP-Auth-Keys |
| `index-v3.html` (Update 2) | VLAN-IDs anonymisieren (`vlan <id>`, `interface Vlan`, `switchport`); VLAN-Ranges (`1-31`); Interface Descriptions; username+secret `noBreak`-Fix |
| `index-v3.html` (Update 3) | DHCP-Relay Isolation (`isolate: true`); FTP username/password; SSH Pubkey-Chain username+key-hash; `ip ftp/tftp source-interface Vlan` |

---

## Technische Details (v3)

### Manuelles Anonymisieren — Funktionsweise

- Textauswahl in der Vorschau (`<pre>`) triggert `document.mouseup`-Handler
- Der Handler prüft `anchorNode` UND `focusNode` (rückwärts-Selektion funktioniert)
- **Ausgewählter Text wird direkt in `<input id="s3-val-input">` in der Toolbar geschrieben** — kein JS-State, kein Timing-Problem
- `manualAnonymize()` liest `input.value.trim()` → absolut zuverlässig
- User kann den Wert im Input auch manuell korrigieren vor dem Anonymisieren
- `if (existing)` setzt `manual: true` + aktualisiert Typ → globale Ersetzung greift immer

**⚠️ Warum kein JS-State (`S.currentSelection`)?**
Die Event-Kette `mouseup → JS-State setzen → Button-Click → State lesen` ist fragil: der Browser löscht die Textauswahl beim Interagieren mit Dropdown oder Button — abhängig von Browser, Klickgeschwindigkeit, OS. Kein `captureSelection()` auf mousedown löst das zuverlässig. Lösung: Text direkt ins DOM (Input-Feld) schreiben.

### Lernfunktion

- Manuell anonymisierte Werte werden im globalen Wörterbuch (`localStorage`) mit `learned: true` gespeichert
- In `extractData()` läuft ein "Learned Values Scan" nach allen Regel-Passes
- `manual: true` ist zwingend nötig, damit `buildAnon()` das Item im finalen Pass global ersetzt
- ✕-Button löscht Item aus `S.items` UND aus `gd.entries` (sonst kommt es beim nächsten Analysieren wieder)

### Originalwert aufdecken

- Jeder Platzhalter-`<span>` trägt `data-ph` und `data-orig` als Attribute
- `toggleReveal(span)` wechselt zwischen Platzhalter und Originalwert
- Guard: `if (window.getSelection().toString().trim()) return` — verhindert Auslösung bei Textauswahl
- Platzhalter markieren (nicht klicken) → Toolbar zeigt Originalwert in `#s3-orig-preview`

### Cascade-Schutz

- Selektion die Teile eines Platzhalters enthält (z.B. `IP_020*` ohne führendes `*`) wird ignoriert
- Check: `if (/\*[A-Z_]+_\d{3}\*/.test(text)) return`

### buildAnon() — wichtige Reihenfolge

1. VLAN-IDs und Namen (Blöcke + interface/switchport/ftp-Zeilen)
2. Regel-basierte Passes (RULES-Array, `break` nach erstem Match — außer `noBreak: true`)
3. General IP Scan (catch-all für verbleibende IPs)
4. Manueller Pass: `S.items.filter(i => i.enabled && i.manual)` — globale String-Ersetzung

Nur Items mit `manual: true` werden im finalen Pass global ersetzt.

### RULES-Flags

| Flag | Bedeutung |
|---|---|
| `noBreak: true` | Regel matched, aber nächste Regel darf die gleiche Zeile nochmal verarbeiten. Nötig bei `username`-Zeilen die auch ein `secret` enthalten. |
| `isolate: true` | Dedup-Key ist `type:value` statt nur `value`. Verhindert Platzhalter-Sharing zwischen Typen (z.B. DNS-IP ≠ DHCP-Relay-IP). Dict-Eintrag bekommt `originalValue` für Learned-Scan und Wörterbuch-Anzeige. |

### get() — Dedup-Logik

```
mapKey = isolate ? type+':'+value : value
gdKey  = isolate ? type+':'+value : value
```
- Gleicher Wert, gleicher Typ → gleicher Platzhalter (via `valMap`)
- Gleicher Wert, unterschiedlicher Typ mit `isolate` → unterschiedliche Platzhalter
- `item.gdKey` wird gespeichert, damit `deleteItem()` den richtigen Dict-Eintrag löscht

---

## Technische Hinweise

- Reines HTML/CSS/JS, kein Build-System
- Testen: Datei lokal im Browser öffnen (`index-v3.html`)
- Kein Server-Setup nötig
