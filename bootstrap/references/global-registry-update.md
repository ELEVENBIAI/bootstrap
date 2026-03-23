# Global Registry Update — Neues Projekt registrieren

Nach dem Setup eines neuen Projekts müssen diese globalen Dateien aktualisiert werden:

## 1. /root/.claude/CLAUDE.md

In der Projektstruktur-Tabelle einen neuen Eintrag ergänzen:

```markdown
| `/path/to/new/project/` | **Projekt-Root** — Git-Repo, Code, Skills, CLAUDE.md | Wir (Claude Code) |
```

Und den "Korrekter Start-Pfad" Abschnitt um einen Eintrag ergänzen:
```bash
cd /path/to/new/project && claude
```

## 2. /root/.claude/projects/-root/memory/MEMORY.md

In der Tabelle "Aktive Projekte" ergänzen:

```markdown
| **{{PROJECT_NAME}}** | `/path/to/project/` | {{LINEAR_TEAM}} | {{ISSUE_PREFIX}} | `vault-name/` |
```

Im Abschnitt "Projekt-Navigation" ergänzen:
```markdown
- `cd /path/to/project && claude`
```

## 3. Neues Projekt-spezifisches Memory anlegen

Datei: `/root/.claude/projects/-root/memory/project_{{project_slug}}_init.md`

```markdown
---
name: {{PROJECT_NAME}} — Initial Setup
description: Setup-Status und Quick Reference für {{PROJECT_NAME}}
type: project
---

**Projekt:** {{PROJECT_NAME}}
**Pfad:** {{PROJECT_PATH}}
**Linear:** {{LINEAR_TEAM}} / {{ISSUE_PREFIX}}
**GitHub:** {{GITHUB_REPO}}
**Obsidian:** {{OBSIDIAN_VAULT}}
**Version:** {{VERSION_START}}
**Setup-Datum:** {{TODAY}}

**Why:** Neues Projekt mit OpenCLAW Governance Framework aufgesetzt.
**How to apply:** Projekt-Kontext für künftige Conversations.

### Installierte Skills
[Liste der installierten Skills]

### Ausstehend
[Was noch fehlt / geplant ist]
```

## 4. Verifizieren

```bash
# Prüfen ob alle Einträge korrekt sind
cat /root/.claude/CLAUDE.md | grep "PROJECT_NAME"
cat /root/.claude/projects/-root/memory/MEMORY.md | grep "PROJECT_NAME"
```
