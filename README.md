# MGD — ToDo SKILL

Ein universeller Skill für KI-Agenten (Claude Code & ChatGPT Codex), der Projekt-Todos in einer **selbst-gehosteten, sortierbaren und durchsuchbaren `TODO.html`** verwaltet — ohne externe Services, ohne Datenbank, vollständig im eigenen Repo.

## Das Problem

Todo-Listen für KI-Agenten landen oft in externen Tools — Board, Tracker oder Datenbank, die eingerichtet, mit Zugängen versehen und gepflegt werden müssen, und die ein Agent nicht ohne API einfach selbst bearbeiten kann. Dieser Skill verzichtet bewusst darauf: Die Todos leben als eine einzige HTML-Datei im Repo.

> [!NOTE]
> **👤 FÜR ENTWICKLER** — Warum eine HTML-Datei?
>
> - Keine Infrastruktur nötig — öffne einfach die Datei im Browser
> - Sortierbar + durchsuchbar — vollständig im Browser, kein Server
> - Self-contained — kein CDN, kein Framework, funktioniert offline
> - Versionierbar — liegt im Repo, Änderungen sind in Git nachvollziehbar
> - KI-editable — einfaches HTML, das Claude Code und Codex direkt bearbeiten können

## Installation

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

> [!NOTE]
> **👤 FÜR ENTWICKLER** — Der Pfad zur TODO.html wird aus `PROJEKT/TODO/.todo-config` gelesen (eine Zeile), mit Fallback auf `PROJEKT/TODO/TODO.html`. Ändern mit `/todo-pfad <pfad>`:
>
> ```bash
> # .todo-config Beispiel
> PROJEKT/TODO/TODO.html
> ```

## Erste Schritte

```
/todo-pfad PROJEKT/TODO/TODO.html
/todo-add "Mein erstes Todo"
/todo
```

## Die wichtigsten Funktionen/Befehle

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

> [!TIP]
> **🤖 FÜR KI-AGENTEN** — `/todo-sync` kennt genau drei Quellen und schlägt neue Einträge zur Bestätigung vor, statt sie automatisch anzulegen: `PlayTest/Test-Todo.md` (offene Checkboxen `- [ ]`), `AI/CODEX-TASKS/*.md` (Aufgaben-Titel aus H2/H3-Überschriften) und `PROJEKT/TODO/*.md` (weitere Todo-Dateien).

### HTML-Struktur

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

**Status-Werte**

| Wert | Badge-Klasse | Anzeige |
|------|-------------|---------|
| `offen` | `s-offen` | Offen |
| `progress` | `s-progress` | In Arbeit |
| `done` | `s-done` | ✓ Erledigt |

**Prioritäten**: `kritisch` (`p-kritisch`), `hoch` (`p-hoch`), `mittel` (`p-mittel`), `niedrig` (`p-niedrig`)

**Kategorien**: `app` (App-Feature), `editor` (Editor-Feature), `backend` (Backend/API), `infra` (Infrastruktur), `ux` (UX/Design), `playtest` (Test-Aufgaben), `doku` (Dokumentation)

### Export-Beispiel

`/todo-export` gibt alle offenen Todos als Markdown-Tabelle aus:

```markdown
| ID    | Titel | Prio | Kategorie | Status |
|-------|-------|------|-----------|--------|
| T-004 | Freunde-System (Backend + UI) | Hoch | App-Feature | Offen |
```

## Grenzen

- Reine Client-Datei: Es gibt keine Mehrbenutzer-Synchronisierung in Echtzeit. Bearbeiten zwei Personen oder Agenten die TODO.html gleichzeitig, entstehen normale Git-Merge-Konflikte in derselben Datei.
- `/todo-sync` kennt nur die drei fest benannten Quellen oben — andere Formate oder Orte werden nicht automatisch erkannt.
- Kein Server, keine Benachrichtigungen: Die Übersicht muss aktiv per `/todo` oder durch Öffnen der Datei im Browser abgerufen werden.
- Für ChatGPT Codex muss der Skill weiterhin explizit über `codex --instructions <pfad> "/todo-<befehl>"` aufgerufen werden — kein automatischer Slash-Befehl wie in Claude Code.

## Wiki

| Seite | Inhalt |
|-------|--------|
| [Übersicht](https://github.com/MichaelGahnDESIGN/MGD_Todo_SKILL/wiki/Home) | Einstieg, Schnell-Referenz, warum eine HTML-Datei |
| [Befehle](https://github.com/MichaelGahnDESIGN/MGD_Todo_SKILL/wiki/Befehle) | Alle `/todo-*`-Befehle im Detail, mit Beispielausgaben |
| [HTML-Format](https://github.com/MichaelGahnDESIGN/MGD_Todo_SKILL/wiki/HTML-Format) | Struktur der TODO.html und data-Attribute |
| [Integration](https://github.com/MichaelGahnDESIGN/MGD_Todo_SKILL/wiki/Integration) | Zusammenspiel mit anderen MGD-Skills |
| [Setup](https://github.com/MichaelGahnDESIGN/MGD_Todo_SKILL/wiki/Setup) | Installation und Konfiguration im Detail |

## Integration mit anderen Skills

Dieser Skill ist darauf ausgelegt, mit anderen KI-Skills zusammenzuarbeiten:

- **[MGD_DEV_SKILL](https://github.com/MichaelGahnDESIGN/MGD_DEV_SKILL)** — vor jedem Deploy `/todo` aufrufen: kritische Todos blockieren den Release
- **[MGD_AI-PlayTest_SKILL](https://github.com/MichaelGahnDESIGN/MGD_AI-PlayTest_SKILL)** — nach Playtest `/todo-sync` aufrufen: neue Bugfix-Todos automatisch importieren
- **[MGD_ProjectClean_SKILL](https://github.com/MichaelGahnDESIGN/MGD_ProjectClean_SKILL)** — `/todo-export` für Release-Notes als Anhang
- **[MGD_AI-Project-Updater_SKILL](https://github.com/MichaelGahnDESIGN/MGD_AI-Project-Updater_SKILL)** — nach Staging-Tests Todos synchronisieren
- **Claude Superpower** — `/todo-add` direkt aus Superpower-Findings aufrufen
- **Playwright** — nach Testlauf fehlgeschlagene Tests als Todos eintragen

## Sicherheitsregeln

> [!WARNING]
> **⚠️ FALLSTRICK** — Nie Secrets in Todo-Einträgen speichern (Passwörter, Tokens, API-Keys, personenbezogene Daten). Die TODO.html landet im Git-Repo und ist damit für jeden mit Repo-Zugriff sichtbar.

- **Nie löschen** — Todos nur als `done` markieren, nie aus der HTML entfernen
- **Keine externen Dependencies** — TODO.html ist vollständig self-contained (inline CSS/JS)
- **Kein CDN** — funktioniert offline, kein Tracking

## Verwandte MGD Projekte

| Projekt | Beschreibung |
|---------|-------------|
| [MGD_Software-Updater_SKILL](https://github.com/MichaelGahnDESIGN/MGD_Software-Updater_SKILL) | Software-Update-Systeme planen und implementieren |
| [MGD_BugReport_SKILL](https://github.com/MichaelGahnDESIGN/MGD_BugReport_SKILL) | Feedback-Hub: Bug-Meldung, Ideen und Support |
| [MGD_DEV_SKILL](https://github.com/MichaelGahnDESIGN/MGD_DEV_SKILL) | Release, Sync, Backup und Wissensdokumentation |
| [MGD_AI-PlayTest_SKILL](https://github.com/MichaelGahnDESIGN/MGD_AI-PlayTest_SKILL) | Live-Playtest aus Nutzerperspektive |
| [MGD_ProjectClean_SKILL](https://github.com/MichaelGahnDESIGN/MGD_ProjectClean_SKILL) | Abschluss- und Aufräum-Workflow |
| [MGD_AI-Basic-Projektordner_TOOL](https://github.com/MichaelGahnDESIGN/MGD_AI-Basic-Projektordner_TOOL) | Projektvorlage für KI-Agenten |

## Lizenz

MIT — frei verwendbar, anpassbar, weitergeben mit Namensnennung. Siehe [LICENSE](LICENSE).

---

*Entwickelt von [Michael Gahn DESIGN](https://michael-gahn.de) — gepflegt mit Claude Code & ChatGPT Codex.*

---

## Impressum

Angaben gemäß § 5 DDG — Siehe [`IMPRESSUM.md`](IMPRESSUM.md).
