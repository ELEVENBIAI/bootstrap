# Info-Gathering Checkliste — Neues Projekt

Alle diese Informationen VOR dem Setup vom Operator einsammeln.
Felder mit * sind Pflicht. Optionale Felder können später ergänzt werden.

## Pre-Flight Checklist (vor Phase 0)

Diese Punkte müssen ERFÜLLT sein bevor Bootstrap startet:

| Check | Befehl | Erwartet |
|-------|--------|----------|
| **SSH zu GitHub** | `ssh -T git@github.com` | `Hi username!...authenticated` |
| **Node.js installiert** | `node --version` | `v18.x` oder höher |
| **GitHub Repo existiert** | (manuell prüfen) | Leeres oder bestehendes Repo |
| **Git konfiguriert** | `git config user.email` | Deine E-Mail-Adresse |

**SSH-Zugang ist Voraussetzung für `git push`.**
Bootstrap führt am Ende automatisch `git push origin main` aus —
ohne SSH schlägt das fehl, egal ob Mac, PC, VPS oder Claude Code Desktop.

Wo SSH eingerichtet wird:
- **Mac/PC lokal:** `~/.ssh/` — Key in GitHub Settings hinterlegen
- **VPS/Server:** gleicher Prozess, auf dem Server ausführen
- **Claude Code Desktop:** nutzt SSH-Agenten des Systems — `ssh -add ~/.ssh/id_ed25519` falls nötig

Details: siehe `bootstrap/README.md` Sektion "SSH-Zugang zu GitHub einrichten".

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

## Erfolgsmetriken (Pflicht — schließt den Learning-Loop)

> **Warum Pflicht?** Ohne diese 3 Felder kann Claude nicht beurteilen ob eine Story "gut" war.
> Sie fließen in CLAUDE.md §2 (Proaktive Pflicht), specs/TEMPLATE.md (Erwarteter Outcome)
> und journal/LEARNINGS.md (Baseline Tag 0) ein.

| Variable | Frage an Operator | Beispiel |
|----------|------------------|---------|
| `METRIC_PRIMARY` * | Was ist deine **primäre Erfolgsmetrik**? Ein Wort oder kurzer Begriff. | `WinRate`, `Conversion Rate`, `API-Latenz`, `Deploy-Zeit`, `Fehlerrate` |
| `METRIC_BASELINE` * | Was ist der **aktuelle Wert** dieser Metrik heute? | `33%`, `800ms`, `2.4%`, `45 Min` |
| `METRIC_TARGET` * | Was gilt als **Erfolg**? Zielwert der angestrebt wird. | `> 45%`, `< 200ms`, `> 5%`, `< 10 Min` |

**Claude setzt diese Werte ein in:**
1. `CLAUDE.md §2` → "Verbessert das {{METRIC_PRIMARY}}?" als Proaktive Pflicht
2. `specs/TEMPLATE.md` → `## Erwarteter Outcome` vorausgefüllt mit {{METRIC_PRIMARY}}
3. `journal/LEARNINGS.md` → Baseline-Eintrag bei Setup: "Tag 0: {{METRIC_PRIMARY}} = {{METRIC_BASELINE}}"
4. Outcome-Check nach Issue-Close: War {{METRIC_TARGET}} erreicht?

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
