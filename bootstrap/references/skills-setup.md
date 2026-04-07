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
ln -s /root/.claude/skills/wrap-up .claude/skills/wrap-up
ln -s /root/.claude/skills/skill-creator .claude/skills/skill-creator
# Optional:
ln -s /root/.claude/skills/cloud-system-engineer .claude/skills/cloud-system-engineer
ln -s /root/.claude/skills/excalidraw-diagram .claude/skills/excalidraw-diagram
ln -s /root/.claude/skills/notebooklm .claude/skills/notebooklm
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
| `backlog/SKILL.md` | Linear Team-Name + Issue-Prefix |
| `wrap-up/SKILL.md` | Memory-Pfad + Projekt-spezifische Synthese-Hinweise |

## Projekt-spezifische Skills (werden generiert, nicht kopiert)

Diese Skills werden vom Bootstrap-Skill mit interaktiven Fragen generiert.
Sie haben KEINE Quell-Kopie in `/root/.claude/skills/` — sie entstehen neu für jedes Projekt.

| Skill | Template | Was Bootstrap fragt |
|-------|----------|---------------------|
| `/breakfix` | `bootstrap/references/breakfix-template.md` | Issue-Prefix, Incident-Dir, Daemons, Logs |
| `/integration-test` | `bootstrap/references/integration-test-template.md` | Tier-1/2 Checks, Post-Implement? |
| `/status` | `bootstrap/references/status-template.md` | Daemons, Signal-Files, Dashboard, Logs |

**Workflow für jeden generierten Skill:**
1. Bootstrap liest Template-Datei
2. Bootstrap stellt die dort definierten Fragen
3. Bootstrap schreibt `{PROJECT_PATH}/.claude/skills/{skill}/SKILL.md` mit Platzhaltern befüllt
4. Skill enthält `## TODO`-Sektion für weitere Konkretisierung nach System-Aufbau

**calibrate:** Wird NICHT generiert — zu domain-spezifisch (Scoring/Gewichtungs-Kalibrierung).
Bei Bedarf: manuell als neuen Skill aufbauen (→ `/skill-creator`).

## Optionale Skills mit Voraussetzungen

| Skill | Voraussetzung | Wofür |
|-------|---------------|-------|
| `research` | `OPENROUTER_API_KEY` (OpenRouter-Account) | Deep Research via Perplexity |
| `cloud-system-engineer` | Hostinger MCP Server (`npx @hostinger/mcp-server`) | VPS-Infrastruktur via Hostinger |
| `excalidraw-diagram` | keine | Architektur-Diagramme als Excalidraw JSON |
| `notebooklm` | `notebooklm-py` CLI installiert (`pip install notebooklm-py`) | Google NotebookLM Automation |
| `visualize` | Miro MCP Server + MIRO_ACCESS_TOKEN | Architektur-Diagramme in Miro |
| `grafana` | Grafana MCP Server + GRAFANA_URL + GRAFANA_API_KEY | Dashboard-Entwicklung in Grafana Cloud |

**Hostinger MCP Setup** (für cloud-system-engineer):
```bash
# In Claude Code settings.json:
{
  "mcpServers": {
    "hostinger-mcp": {
      "command": "npx",
      "args": ["-y", "@hostinger/mcp-server"],
      "env": { "HOSTINGER_API_KEY": "your_api_key" }
    }
  }
}
```

## .claude/ISSUE_WRITING_GUIDELINES.md

Diese Datei ist nicht Teil eines Skills, muss direkt erstellt werden:
- Vorlage liegt im Bootstrap-Skill: `bootstrap/references/issue-writing-guidelines-template.md`
- Bootstrap Phase 1 schreibt sie automatisch nach `{PROJECT_PATH}/.claude/ISSUE_WRITING_GUIDELINES.md`
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

1. `/research` — keine Abhängigkeiten (benötigt OPENROUTER_API_KEY für Deep Tier)
2. `/ideation` — benötigt story-templates + Linear
3. `/backlog` — benötigt Linear
4. `/implement` — benötigt change-checklist + git
5. `/architecture-review` — benötigt dimensions-definition
6. `/wrap-up` — benötigt Memory-Pfad (wird bei Session-Ende aufgerufen)
7. `/breakfix` — wird generiert: Issue-Prefix, Incident-Dir, Logs
8. `/integration-test` — wird generiert: Tier-Checks definieren
9. `/status` — wird generiert: Daemons + Dashboard definieren
10. `/cloud-system-engineer` — benötigt Hostinger MCP (optional)
11. `/excalidraw-diagram` — standalone, keine Voraussetzungen
12. `/notebooklm` — benötigt notebooklm-py CLI
13. `/sprint-review` — benötigt alle anderen
14. `/visualize` — benötigt Miro Token + MCP
15. `/skill-creator` — standalone
