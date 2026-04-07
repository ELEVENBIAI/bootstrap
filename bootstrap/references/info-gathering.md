# Info-Gathering Checkliste — Neues Projekt

Alle diese Informationen VOR dem Setup vom Operator einsammeln.
Felder mit * sind Pflicht. Optionale Felder können später ergänzt werden.

## Pflicht-Informationen

| Variable | Frage an Operator | Beispiel |
|----------|------------------|---------|
| `PROJECT_NAME` * | Wie heißt das Projekt? | `MyAnalytics` |
| `PROJECT_DESC` * | Ein Satz: Was macht das System? | "Ein Datenanalyse-Tool für Marketing-KPIs" |
| `PROJECT_PATH` * | Absoluter Pfad zum Projekt-Verzeichnis | `/docker/myproject/data/workspace/` |
| `GITHUB_REPO` * | GitHub Repository URL | `github.com/user/repo` |
| `LINEAR_TEAM` * | Linear Team-Name (Slug) | `myteam` |
| `ISSUE_PREFIX` * | Issue-Nummern-Prefix in Linear | `PROJ-` |
| `VERSION_START` * | Start-Version | `1.0.0` |
| `OBSIDIAN_VAULT` * | Absoluter Pfad zum Obsidian Vault | `/root/myvault/` |

## Optionale Informationen

| Variable | Frage an Operator | Default |
|----------|------------------|---------|
| `TELEGRAM_BOT_TOKEN` | Telegram Bot Token für Alerts? | — (skip) |
| `PERPLEXITY_API_KEY` | Perplexity API Key für Deep Research? | — (OPENROUTER_API_KEY alternativ) |
| `MIRO_BOARD_URL` | Miro Board URL für /visualize? | — (skip) |
| `DAEMON_ENABLED` | Automation Daemon einrichten? (Ja/Nein) | Nein |

## Architektur-Dimensionen

Welche der 8 Standard-Dimensionen sind relevant?
Welche domain-spezifischen Dimensionen braucht das Projekt?

Standard (immer relevant):
- Reliability, Data Integrity, Security, Performance, Observability, Maintainability

Optional je nach Domäne:
- Cost Efficiency (bei API-lastigen Systemen)
- Signal Quality (bei ML/Analytics-Systemen)
- Custom Dimension (z.B. "Compliance" für regulierte Branchen)

## Skill-Auswahl

Welche Skills sollen installiert werden?

**Generische Skills (Symlink — keine Anpassung nötig):**
- [ ] /ideation (empfohlen)
- [ ] /implement (empfohlen)
- [ ] /backlog (empfohlen)
- [ ] /wrap-up (empfohlen — Session-Abschluss + Auto-Memory)
- [ ] /architecture-review
- [ ] /sprint-review
- [ ] /research (benötigt OPENROUTER_API_KEY für Deep-Tier via Perplexity)
- [ ] /excalidraw-diagram (Architektur-Diagramme als Excalidraw JSON — keine Deps)
- [ ] /cloud-system-engineer (nur wenn Hostinger VPS + MCP Server eingerichtet)
- [ ] /notebooklm (benötigt notebooklm-py CLI — `pip install notebooklm-py`)
- [ ] /visualize (nur wenn Miro MCP + MIRO_ACCESS_TOKEN vorhanden)
- [ ] /skill-creator

**Projekt-spezifische Skills (werden vom Bootstrap generiert — Fragen werden gestellt):**
- [ ] /breakfix — Incident Response mit projekt-spezifischen Diagnose-Checks
  → Bootstrap fragt: Issue-Prefix, Incident-Verzeichnis, Daemons, Log-Files
- [ ] /integration-test — System-Integrationstests nach /implement
  → Bootstrap fragt: Tier-1 Checks, Tier-2 Checks, Post-Implement Auto-Run
- [ ] /status — System Status Dashboard
  → Bootstrap fragt: Daemons, Signal-Files, Dashboard-URL, Log-Files

**Hinweis:** calibrate, briefing-dieter, grafana sind zu domain-spezifisch
und werden NICHT generisch angeboten. Bei Bedarf manuell aufbauen (→ /skill-creator).

## Label-Taxonomie

Projektspezifische Labels für Linear definieren.
Minimum-Set (immer anlegen):
- `architecture`, `bug`, `feature`, `refactor`, `docs`, `infra`

Domain-spezifisch ergänzen (Beispiele):
- Trading: `agents`, `signals`, `risk`, `broker`
- Analytics: `pipeline`, `dashboard`, `data-quality`
- Web-App: `frontend`, `backend`, `api`, `ux`
