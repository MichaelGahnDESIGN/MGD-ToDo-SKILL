---
name: todo
description: >-
  Universelles TODO-Management für KI-Agenten: TODO.html lesen, Todos hinzufügen,
  Status aktualisieren, debuggen und aus Projekt-Quellen synchronisieren.
  Kompatibel mit Claude Code und ChatGPT Codex.
---

# /todo — Universelles TODO-Management

Verwalte Projekt-Todos in einer selbst-gehosteten `TODO.html`-Datei, die
sortier- und durchsuchbar ist und lokal im Projektordner liegt.

> Leitsatz: „Kein Todo geht verloren. Alles landet in der HTML. Status immer aktuell."

---

## Konfiguration (einmalig setzen)

Der Pfad zur TODO-Datei wird im Projekt gepflegt. Standard:

```
PROJEKT/TODO/TODO.html
```

Mit `/todo-pfad <neuer-pfad>` kann der Pfad geändert werden.
Der aktuelle Pfad wird in `PROJEKT/TODO/.todo-config` gespeichert.

---

## Befehle

### `/todo-setup` — Neues Projekt einrichten (einmalig)

**Verwende diesen Befehl in jedem neuen Projekt als erstes.** Er richtet alles
automatisch ein, sodass danach alle anderen `/todo-*`-Befehle funktionieren.

Ablauf:
1. Prüfe ob bereits eine TODO.html im Projekt existiert (via `.todo-config` oder
   Default-Pfad `PROJEKT/TODO/TODO.html`)
2. Falls bereits vorhanden: kurze Bestätigung ausgeben, Setup überspringen
3. Falls nicht vorhanden:
   a. Frage nach dem gewünschten Pfad (Default: `PROJEKT/TODO/TODO.html`, Enter = übernehmen)
   b. Erstelle den Ordner falls nötig
   c. Lade das Template von GitHub herunter:
      ```
      https://raw.githubusercontent.com/MichaelGahnDESIGN/MGD-ToDo-SKILL/main/todo/TODO.template.html
      ```
      Falls kein Internetzugang: Erstelle eine minimale TODO.html inline (gleiche
      Struktur, leerer tbody).
   d. Passe den Titel in der HTML an (ersetze „TINTLING TODO" durch den Projektnamen,
      abgeleitet aus dem Verzeichnisnamen oder `package.json`/`pubspec.yaml`)
   e. Schreibe den Pfad in `.todo-config`
   f. Gib Erfolgsmeldung aus: Pfad, nächster Schritt (`/todo-add "Erstes Todo"`)

Hinweis für Codex: Template via `curl` laden, Ordner via `mkdir -p` anlegen.

---

### `/todo` — Übersicht offener Todos

Lies die TODO.html und zeige eine kompakte Übersicht:
- Alle offenen Todos (status="offen" oder status="progress"), sortiert nach Priorität
- Gesamtstatistik: N Offen | N In Arbeit | N Erledigt | N Gesamt
- Jeweils: ID · Titel · Kategorie · Priorität

### `/todo-pfad <pfad>` — Pfad zur TODO.html setzen

Speichert den neuen Pfad in `PROJEKT/TODO/.todo-config`.
Format: absoluter oder relativer Pfad ab Projekt-Root.

### `/todo-update` — Todos manuell aktualisieren

Interaktiver Modus:
1. Zeige aktuelle offene Todos
2. Frage: Welches Todo aktualisieren? (ID oder Titel)
3. Welcher neue Status? (offen / progress / done)
4. Optional: Notiz ergänzen?
5. Schreibe Änderungen in TODO.html (HTML-Attribute data-status aktualisieren, Badge-Klasse tauschen, row-done ergänzen)

### `/todo-add <titel>` — Neues Todo hinzufügen

Parameter:
- `<titel>` — Pflicht, kurzer Titel des Todos
- Optional via interaktivem Dialog:
  - Kategorie (app / editor / backend / infra / ux / playtest / doku)
  - Priorität (kritisch / hoch / mittel / niedrig)
  - Notizen
  - Quelle (z. B. PlayTest v0.1.4, CODEX-TASKS, Manuell)

Generiert die nächste freie T-NNN ID und fügt eine neue `<tr>`-Zeile im
`<tbody id="todoBody">` ein. Datum = heute.

### `/todo-close <id>` — Todo als erledigt markieren

Beispiel: `/todo-close T-004`

Ändert in der TODO.html:
- `data-status="done"`
- Badge-Klasse → `s-done`, Text → `✓ Erledigt`
- Zeile bekommt Klasse `row-done`
- `titel-col` erhält Durchstreichung via CSS (automatisch durch row-done)

### `/todo-debug` — Validierung und Diagnose

Prüft die TODO.html auf:
- Gültige HTML-Struktur (öffnende/schließende Tags korrekt)
- Alle `<tr>` haben data-status, data-prio, data-kat
- Keine doppelten IDs (T-NNN)
- Alle Badge-Klassen sind bekannte Klassen (s-*, p-*, k-*)
- Nächste freie ID ausgeben
- Statistik: Offen / In Arbeit / Erledigt / Gesamt

### `/todo-sync` — Aus Projekt-Quellen synchronisieren

Liest bekannte Todo-Quellen im Projekt und schlägt neue Todos vor, die noch nicht
in der HTML stehen:
1. `PlayTest/Test-Todo.md` — offene Checkboxen (`- [ ]`)
2. `AI/CODEX-TASKS/*.md` — Aufgaben-Titel aus H2/H3-Überschriften
3. `PROJEKT/TODO/*.md` — weitere Todo-Dateien

Für jeden gefundenen, noch nicht in der HTML vorhandenen Punkt:
- Zeige Vorschau: Titel + Quelle
- Frage ob hinzufügen (y/n/alle)
- Falls ja: `/todo-add` intern aufrufen

### `/todo-export` — Markdown-Export

Gibt alle offenen Todos als Markdown-Tabelle aus (für Commits, PRs, Mails):

```markdown
| ID    | Titel | Prio | Kategorie | Status |
|-------|-------|------|-----------|--------|
| T-004 | Freunde-System (Backend + UI) | Hoch | App-Feature | Offen |
...
```

---

## HTML-Format der TODO.html

Die TODO.html enthält alle Todos als `<tr>`-Zeilen in `<tbody id="todoBody">`.
Jede Zeile hat folgende data-Attribute für Filter/Sort:

```html
<tr data-status="offen|progress|done"
    data-prio="kritisch|hoch|mittel|niedrig"
    data-kat="app|editor|backend|infra|ux|playtest|doku">
  <td class="id-col">T-NNN</td>
  <td class="titel-col">Titel des Todos</td>
  <td><span class="badge k-KATEGORIE">Kategorie</span></td>
  <td><span class="badge p-PRIO">Priorität</span></td>
  <td><span class="badge s-STATUS">Status</span></td>
  <td class="quelle-col">Quelle</td>
  <td class="notizen-col">Notizen</td>
  <td class="datum-col">YYYY-MM-DD</td>
</tr>
```

Erledigte Zeilen haben zusätzlich `class="row-done"` auf dem `<tr>`.

---

## Sicherheitsregeln

- **Keine Secrets** in TODO-Einträgen: keine Passwörter, Tokens, Zugangsdaten,
  API-Keys, personenbezogene Daten.
- **Keine externen Dependencies**: TODO.html ist vollständig self-contained
  (inline CSS + JS, keine CDN-Links).
- **Niemals löschen**: Todos werden nur als "done" markiert, nie aus der HTML entfernt.
  Vollständige Nachvollziehbarkeit.
- **Lokal first**: TODO.html bleibt im Repo (committed), PlayTest-Artefakte bleiben lokal.

---

## Nutzung in ChatGPT Codex

Diese Datei ist self-contained für Codex. Codex nutzt Shell-Tooling statt
Claude-spezifischer APIs. Die Schritt-Logik ist dieselbe; Befehle via
`codex --instructions <pfad-zu-SKILL.md> "/todo-<befehl>"`.

Pfad-Auflösung für Codex: Lies zuerst `PROJEKT/TODO/.todo-config` (Pfad),
falls nicht vorhanden default `PROJEKT/TODO/TODO.html` ab Repo-Root.

---

## Integration mit anderen Skills

- **`/playtest`** → nach Playtest: `/todo-sync` aufrufen, neue Bugfix-Todos importieren
- **`/dev`** → vor Deploy: `/todo` aufrufen, kritische offene Todos prüfen
- **`/projectclean`** → `/todo-export` für Release-Notes Anhang
- **Superpower (Claude)** → `/todo-add` direkt aus Superpower-Findings aufrufen
- **Playwright** → nach Testlauf fehlgeschlagene Tests als Todos via `/todo-add` eintragen
