# Skills Setup — Neue Projekt-Installation

## Verfügbare Skills (Quellpfad)

Alle Skills liegen global unter `/root/.claude/skills/`. Für ein neues Projekt werden sie
in das Projekt-Verzeichnis unter `.claude/skills/` verlinkt oder kopiert.

## Strategie: Symlink vs. Kopie

**Empfehlung: Symlink für generische Skills**
```bash
# Im Projekt-Verzeichnis
mkdir -p .claude/skills
ln -s /root/.claude/skills/ideation .claude/skills/ideation
ln -s /root/.claude/skills/implement .claude/skills/implement
ln -s /root/.claude/skills/backlog .claude/skills/backlog
ln -s /root/.claude/skills/architecture-review .claude/skills/architecture-review
ln -s /root/.claude/skills/sprint-review .claude/skills/sprint-review
ln -s /root/.claude/skills/research .claude/skills/research
ln -s /root/.claude/skills/skill-creator .claude/skills/skill-creator
# Optional:
ln -s /root/.claude/skills/cloud-system-engineer .claude/skills/cloud-system-engineer
ln -s /root/.claude/skills/visualize .claude/skills/visualize
```

**Kopie wenn Anpassung nötig** (z.B. projektspezifische Templates):
```bash
cp -r /root/.claude/skills/ideation .claude/skills/ideation
# Dann anpassen:
# - .claude/skills/ideation/references/story-template-feature.md
# - .claude/skills/ideation/references/architecture-dimensions.md
```

## Anpassungspflicht nach Kopie

Diese Referenz-Dateien MÜSSEN nach dem Kopieren projektspezifisch angepasst werden:

| Datei | Was anpassen |
|-------|-------------|
| `ideation/references/story-template-feature.md` | Domain-spezifische Sektionen |
| `ideation/references/architecture-dimensions.md` | Relevante Dimensionen auswählen/ergänzen |
| `implement/references/change-checklist.md` | Spezial-Checklisten (z.B. "Neuer Agent") |
| `backlog/skill.md` | Linear Team-Name + Issue-Prefix |

## .claude/ISSUE_WRITING_GUIDELINES.md

Diese Datei ist nicht Teil eines Skills, muss direkt erstellt werden:
- Kopiere aus `/docker/openclaw-aolv/data/.openclaw/workspace/trading/.claude/ISSUE_WRITING_GUIDELINES.md`
- Passe auf Projekt-Domain an

## settings.json — Skill-Aktivierung

Skills werden automatisch durch Claude Code geladen wenn sie unter `.claude/skills/` liegen.
Keine manuelle Registrierung nötig.

Aber: Für den Automation Daemon werden extra Permissions benötigt:
```json
// /root/.claude/settings.json
{
  "permissions": {
    "allow": [
      "Bash(git:*)",
      "Bash(node:*)",
      "Bash(npm:*)",
      "Bash(claude:*)"
    ]
  }
}
```

## Reihenfolge der Skill-Installation (nach Abhängigkeit)

1. `/research` — keine Abhängigkeiten
2. `/ideation` — benötigt story-templates + Linear
3. `/backlog` — benötigt Linear
4. `/implement` — benötigt change-checklist + git
5. `/architecture-review` — benötigt dimensions-definition
6. `/cloud-system-engineer` — benötigt Hostinger MCP (falls genutzt)
7. `/sprint-review` — benötigt alle anderen
8. `/visualize` — benötigt Miro Token
9. `/skill-creator` — standalone
