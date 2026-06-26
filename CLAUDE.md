# Config anonymisieren — Claude Code Projektkontext

Ältere / öffentliche Version des Cisco Config Anonymizers, deployed via GitHub Pages.

---

## Was ist das?

Browser-Tool zum Anonymisieren von Cisco-Konfigurationen. Läuft vollständig im Browser, kein Server.

**GitHub Pages URL:** `https://dmichel999.github.io/Config-anonymisieren-/`

---

## Features

- Automatische Erkennung: IPs, Hostnamen, Passwörter/Secrets, SNMP, BGP, VLANs, NTP, RADIUS, DNA-Token, Benutzernamen
- Globales Wörterbuch (dateiübergreifend, gleicher Wert → gleicher Platzhalter)
- Merge-Funktion (anonymisierte Config mit Originaldaten wiederherstellen)
- Auto-Save via localStorage
- Platzhalterformat: `*HOSTNAME_001*`
- **Vorschau** mit farblich hervorgehobenen Platzhaltern (je Typ eigene Farbe)
- **Manuelles Anonymisieren** direkt in der Vorschau (Text markieren → Typ wählen → Anonymisieren)
- **Lernfunktion** — manuell anonymisierte Werte werden gespeichert und in zukünftigen Configs automatisch erkannt (🧠)
- **Originalwert aufdecken** — Klick auf Platzhalter in der Vorschau zeigt den Originalwert; nochmal klicken versteckt ihn
- **Gelernte Regeln verwalten** im Wörterbuch-Tab (filtern, einzeln oder alle löschen)

---

## Dateien

| Datei | Zweck |
|---|---|
| `index.html` | Originalversion (GitHub Pages Einstiegspunkt) |
| `index-v2.html` | + Vorschau mit Highlighting + manuelles Anonymisieren |
| `index-v3.html` | + Lernfunktion + Originalwert aufdecken + Gelernte verwalten |
| `cisco-config-anonymizer.html` | Ältere Alternativversion |

---

## Versionshistorie

| Version | Neue Features |
|---|---|
| `index.html` | Basisversion: Erkennung, Wörterbuch, Merge, Auto-Save |
| `index-v2.html` | Vorschau (Step 3) mit farbigen Platzhaltern; manuelles Anonymisieren per Textmarkierung |
| `index-v3.html` | Lernfunktion (🧠 gelernte Werte auto-erkennen); Klick auf Platzhalter zeigt Originalwert; Wörterbuch-Tab mit Filter + "Gelernte löschen"-Button |

---

## Technische Details (v3)

### Lernfunktion
- Manuell anonymisierte Werte werden im globalen Wörterbuch (`localStorage`) mit `learned: true` gespeichert
- In `extractData()` läuft nach allen Regelspässen ein "Learned Values Scan": durchsucht `out[]` nach bekannten gelernten Werten, ersetzt sie und setzt `learned: true, manual: true` auf dem Item
- `manual: true` ist zwingend nötig, damit `buildAnon()` das Item im finalen Pass ersetzt
- Gelernte Items erscheinen in der Erkannte-Daten-Tabelle mit 🧠-Icon
- ✕-Button in der Tabelle löscht das Item aus `S.items` UND aus dem globalen Wörterbuch (sonst kommt es beim nächsten Analysieren wieder)
- Scope: Browser-übergreifend via localStorage → für projektspezifische Werte "Gelernte löschen" im Wörterbuch-Tab nutzen

### Originalwert aufdecken
- Jeder Platzhalter-`<span>` in der Vorschau trägt `data-ph` (Platzhalter) und `data-orig` (Originalwert) als Attribute
- `toggleReveal(span)` wechselt `textContent` zwischen Platzhalter und Originalwert
- Guard: `if (window.getSelection().toString().trim()) return` verhindert Auslösung bei Textauswahl
- Nur einzelne Tokens aufdeckbar — kein "Alle aufdecken"
- Platzhalter markieren (nicht klicken) → Toolbar zeigt Originalwert direkt an

### Manuelles Anonymisieren — Funktionsweise
- Textauswahl in der Vorschau (`<pre>`) triggert `document.mouseup`-Handler
- Der Handler prüft `anchorNode` UND `focusNode` (rückwärts-Selektion funktioniert)
- **Ausgewählter Text wird direkt in `<input id="s3-val-input">` in der Toolbar geschrieben** — kein JS-State, kein Timing-Problem
- `manualAnonymize()` liest einfach `input.value.trim()` → absolut zuverlässig
- User kann den Wert im Input auch manuell korrigieren, bevor er anonymisiert
- `if (existing)` setzt jetzt auch `manual: true` + aktualisiert Typ → globale Ersetzung greift immer

### Cascade-Schutz
- Selektion die Teile eines Platzhalters enthält (z.B. `IP_020*` ohne führendes `*`) wird ignoriert
- Check: `if (/\*[A-Z_]+_\d{3}\*/.test(text)) return`

### buildAnon() — wichtige Reihenfolge
1. Regel-basierte Passes (VLAN-Namen, RULES-Array, General IP Scan)
2. Manueller Pass: `S.items.filter(i => i.enabled && i.manual)` — globale String-Ersetzung
- Nur Items mit `manual: true` werden im finalen Pass ersetzt
- Items aus `extractData()` Regel-Matches haben kein `manual: true` → werden nur durch Regel-Passes ersetzt
- Items aus Lern-Scan und manueller Anonymisierung müssen `manual: true` haben

---

## Deployment

Änderungen werden via `git push` auf GitHub deployed. GitHub Pages picked es automatisch auf.
Aktuell deployed: `index.html` (Originalversion). Für neue Version `index-v3.html` → `index.html` umbenennen/ersetzen.

---

## Technische Hinweise

- Reines HTML/CSS/JS, kein Build-System
- Testen: Datei lokal im Browser öffnen
- Kein Server-Setup nötig
