# MGD — ToDo SKILL

Ein universeller Skill für KI-Agenten (Claude Code & ChatGPT Codex), der Projekt-Todos in einer **selbst-gehosteten, sortierbaren und durchsuchbaren `TODO.html`** verwaltet — ohne externe Services, ohne Datenbank, vollständig im eigenen Repo.

---

## Was dieser Skill macht

| Befehl | Was passiert |
|--------|-------------|
| `/todo` | Zeigt alle offenen Todos sortiert nach Priorität |
| `/todo-setup` | startet Assistent für die Ersteinrichtung |
| `/todo-add <titel>` | Fügt ein neues Todo in die HTML ein |
| `/todo-close <id>` | Markiert ein Todo als erledigt |
| `/todo-update` | Interaktives Aktualisieren von Status und Notizen |
| `/todo-pfad <pfad>` | Setzt den Pfad zur TODO.html (gespeichert in `.todo-config`) |
| `/todo-sync` | Importiert neue Todos aus Projekt-Quellen (PlayTest, CODEX-TASKS, …) |
| `/todo-debug` | Validiert die HTML-Struktur, findet kaputte Einträge |
| `/todo-export` | Markdown-Export aller offenen Todos |

---

## Warum eine HTML-Datei?

- **Keine Infrastruktur nötig** — öffne einfach die Datei im Browser
- **Sortierbar + durchsuchbar** — vollständig im Browser, kein Server
- **Self-contained** — kein CDN, kein Framework, funktioniert offline
- **Versionierbar** — liegt im Repo, Änderungen sind in Git nachvollziehbar
- **KI-editable** — einfaches HTML, das Claude Code und Codex direkt bearbeiten können

---

## Schnellstart

### 1. Template kopieren

```bash
cp todo/TODO.template.html PROJEKT/TODO/TODO.html
```

### 2. Skill installieren (Claude Code)

```bash
# In ~/.claude/skills/todo/ ablegen
cp SKILL.md ~/.claude/skills/todo/SKILL.md
```

Oder `.claude/commands/todo.md` ins Projekt-Root kopieren (Projekt-lokal).

### 3. Skill installieren (ChatGPT Codex)

```bash
cp .codex/commands/todo.md .codex/commands/todo.md
# Im Projekt verwenden:
codex --instructions .codex/commands/todo.md "/todo"
```

### 4. Ersten Einsatz starten

```
/todo-pfad PROJEKT/TODO/TODO.html
/todo-add "Mein erstes Todo"
/todo
```

---

## HTML-Struktur

Todos sind einfache `<tr>`-Zeilen mit data-Attributen:

```html
<tr data-status="offen" data-prio="hoch" data-kat="app">
  <td class="id-col">T-001</td>
  <td class="titel-col">Mein Todo</td>
  <td><span class="badge k-app">App-Feature</span></td>
  <td><span class="badge p-hoch">Hoch</span></td>
  <td><span class="badge s-offen">Offen</span></td>
  <td class="quelle-col">Manuell</td>
  <td class="notizen-col">Optionale Notiz</td>
  <td class="datum-col">2026-06-16</td>
</tr>
```

### Status-Werte

| Wert | Badge-Klasse | Anzeige |
|------|-------------|---------|
| `offen` | `s-offen` | Offen |
| `progress` | `s-progress` | In Arbeit |
| `done` | `s-done` | ✓ Erledigt |

### Prioritäten

| Wert | Badge-Klasse |
|------|-------------|
| `kritisch` | `p-kritisch` |
| `hoch` | `p-hoch` |
| `mittel` | `p-mittel` |
| `niedrig` | `p-niedrig` |

### Kategorien

| Wert | Badge-Klasse | Bedeutung |
|------|-------------|-----------|
| `app` | `k-app` | App-Feature |
| `editor` | `k-editor` | Editor-Feature |
| `backend` | `k-backend` | Backend/API |
| `infra` | `k-infra` | Infrastruktur |
| `ux` | `k-ux` | UX/Design |
| `playtest` | `k-playtest` | Test-Aufgaben |
| `doku` | `k-doku` | Dokumentation |

---

## Pfad-Konfiguration

Der Skill liest den Pfad zur TODO.html aus:
1. `PROJEKT/TODO/.todo-config` (enthält nur den Pfad, eine Zeile)
2. Default-Fallback: `PROJEKT/TODO/TODO.html`

```bash
# .todo-config Beispiel
PROJEKT/TODO/TODO.html
```

---

## Integration mit anderen Skills

Dieser Skill ist darauf ausgelegt, mit anderen KI-Skills zusammenzuarbeiten:

- **[MGD-DEV-Skill](https://github.com/MichaelGahnDESIGN/MGD-DEV-Skill)** — vor jedem Deploy `/todo` aufrufen: kritische Todos blockieren den Release
- **[MGD-AI-PlayTest-Skill](https://github.com/MichaelGahnDESIGN/MGD-AI-PlayTest-Skill)** — nach Playtest `/todo-sync` aufrufen: neue Bugfix-Todos automatisch importieren
- **[MGD-ProjectClean-Skill](https://github.com/MichaelGahnDESIGN/MGD-ProjectClean-Skill)** — `/todo-export` für Release-Notes als Anhang
- **[MGD-AI-Project-Updater-Skill](https://github.com/MichaelGahnDESIGN/MGD-AI-Project-Updater-Skill)** — nach Staging-Tests Todos synchronisieren
- **Claude Superpower** — `/todo-add` direkt aus Superpower-Findings aufrufen
- **Playwright** — nach Testlauf fehlgeschlagene Tests als Todos eintragen

---

## Sicherheitsregeln

- **Keine Secrets** in TODO-Einträgen (keine Passwörter, Tokens, API-Keys, personenbezogene Daten)
- **Nie löschen** — Todos nur als `done` markieren, nie aus der HTML entfernen
- **Keine externen Dependencies** — TODO.html ist vollständig self-contained (inline CSS/JS)
- **Kein CDN** — funktioniert offline, kein Tracking

---

## Weitere öffentliche Skills von MGD

| Skill | Beschreibung |
|-------|-------------|
| [MGD-DEV-Skill](https://github.com/MichaelGahnDESIGN/MGD-DEV-Skill) | Release, Sync, Backup, Cleanup, Tests, Wissensdokumentation |
| [MGD-AI-PlayTest-Skill](https://github.com/MichaelGahnDESIGN/MGD-AI-PlayTest-Skill) | Live-Playtest einer Web-App aus Erstnutzer-Perspektive |
| [MGD-ProjectClean-Skill](https://github.com/MichaelGahnDESIGN/MGD-ProjectClean-Skill) | Abschluss- und Aufräum-Workflow: Version, Backup, Cleanup |
| [MGD-AI-Project-Updater-Skill](https://github.com/MichaelGahnDESIGN/MGD-AI-Project-Updater-Skill) | Sichere lokale Staging-, Docker- und Update-Workflows |
| [MGD-AI-Basic-Projektordner](https://github.com/MichaelGahnDESIGN/MGD-AI-Basic-Projektordner) | Projektordner-Vorlage für ChatGPT Codex, Cowork & Claude Code |

---

## Lizenz

MIT — frei verwendbar, anpassbar, weitergeben mit Namensnennung.

---

*Entwickelt von [Michael Gahn DESIGN](https://michael-gahn.de) — gepflegt mit Claude Code & ChatGPT Codex.*
