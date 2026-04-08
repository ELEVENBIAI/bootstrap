# OpenCLAW Bootstrap Skill

> Ein **portabler Claude Code Skill**, der ein vollständiges KI-gesteuertes Entwicklungs-Governance-Framework für jedes neue Projekt einrichtet — in 5 geführten Phasen, ohne externe Abhängigkeiten.

**Ursprung:** [OpenCLAW Trading System](https://github.com/vibercoder79/openclaw_trading) — seit 2025 im Produktivbetrieb erprobt.

---

## Warum dieses Framework?

Die meisten AI-Development-Frameworks sind entweder zu viel Automation (Black Box, kein Traceability) oder zu wenig Struktur (Cursor-Rules ohne Governance). Dieses Framework trifft den Sweet Spot:

| Stärke | Was das bedeutet |
|--------|-----------------|
| 🔒 **Governance-Enforcement durch Git Hooks** | `spec-gate.sh` blockiert jeden Commit ohne Spec-File. `doc-version-sync.sh` blockiert jeden Push bei Versions-Drift. Kein anderes AI-Framework erzwingt das maschinell. |
| 🔗 **Vollständige Traceability** | Jede Änderung folgt dem Pfad: Idee → Linear Issue → Spec → Commit → Changelog. Lückenlos nachvollziehbar, auch Monate später. |
| 🔄 **Self-Healing als Safety-Net** | Ein Cron-Agent prüft alle 15 Minuten: Versionen synchron? Dateien vorhanden? Daemons laufen? Und korrigiert automatisch — ohne menschliche Intervention. |
| 👤 **Human-in-the-Loop konsequent erzwungen** | Kein Code-Change ohne Operator-Freigabe. Kein Issue ohne Spec. Kein Spec ohne Architekturdimensionen. Claude fragt — du entscheidest. |

> **Vergleich:** CrewAI hat Role-based Crews. AutoGen hat Debate-Pattern. Dieses Framework hat **erzwungene Governance** — das einzige Framework das maschinell sicherstellt, dass KI-generierter Code dieselben Qualitätsstandards erfüllt wie menschlicher Code.

---

## Detaillierter Framework-Vergleich

### OpenCLAW Bootstrap vs. CrewAI vs. AutoGen vs. BMAD vs. Cursor Rules

> Tausende AI-Development-Frameworks existieren. Hier ist eine ehrliche Einordnung — was andere besser machen, was dieses Framework einzigartig macht, und wann du welches wählen solltest.

| Dimension | **OpenCLAW Bootstrap** | CrewAI | AutoGen / AG2 | BMAD | Cursor Rules |
|-----------|------------------------|--------|---------------|------|--------------|
| **Governance-Enforcement** | ✅ Maschinell erzwungen (Git Hooks) | ❌ Keine | ❌ Keine | ⚠️ Manuell | ❌ Keine |
| **Traceability** | ✅ Idee → Issue → Spec → Commit | ❌ | ❌ | ⚠️ Partiell | ❌ |
| **Human-in-the-Loop** | ✅ Erzwungen (Spec-Freigabe) | ⚠️ Optional | ⚠️ Optional | ✅ Explizit | ❌ |
| **Self-Healing** | ✅ Cron, 15 Min, auto-korrigiert | ❌ | ❌ | ❌ | ❌ |
| **Learning-Loop** | ✅ Outcome-Check + LEARNINGS.md | ❌ | ❌ | ❌ | ❌ |
| **Modell-Routing** | ✅ Opus/Sonnet/Haiku je Task-Typ | ⚠️ Konfigurierbar | ✅ Gut | ❌ | ❌ |
| **Multi-Agent Orchestrierung** | ✅ Agent-Teams + Parallel-Subagents | ✅ Stark | ✅ Sehr stark | ⚠️ Manuell | ❌ |
| **Deploy-Automation** | ⚠️ Teilweise (Git Push + Manual) | ❌ | ❌ | ❌ | ❌ |
| **Portabilität** | ✅ Zero Dependencies, 1 Ordner | ⚠️ pip install | ⚠️ pip install | ⚠️ Prompt-Files | ✅ |
| **Projekt-Setup-Zeit** | ~30 Min (geführt) | Stunden | Stunden | ~1h | Minuten |
| **Zielgruppe** | Solo-Dev bis kleines Team | Enterprise-Teams | Forschung / Quality | Agile Teams | Einzelentwickler |

### Was andere Frameworks besser machen

| Framework | Echte Stärke | Wann bevorzugen |
|-----------|-------------|-----------------|
| **CrewAI** | Skalierbare Role-based Crews für Enterprise — 60% der Fortune 500 nutzen es. Beste Wahl wenn >10 Agents koordiniert werden müssen. | Großes Team, viele parallele Workflows, Enterprise-Compliance-Anforderungen |
| **AutoGen / AG2** | Debate-Pattern: 2 Agents argumentieren gegeneinander bis zur besten Lösung. Höchste Ausgabequalität für komplexe Analyse-Aufgaben. | Forschung, Code-Review mit höchsten Qualitätsanforderungen, offline Batch-Prozesse |
| **BMAD** | Strukturierter Agile-Workflow mit klaren Rollen (PM, Architect, Developer). Gut dokumentiert, große Community. | Teams die Scrum/Agile bereits kennen und einen AI-nativen Workflow wollen |
| **Cursor Rules** | Sofort einsatzbereit, keine Setup-Zeit, direkt im Editor. | Einzelentwickler die schnell starten wollen ohne Governance-Overhead |

### Was OpenCLAW Bootstrap einzigartig macht

**1. Governance ist maschinell erzwungen — nicht nur dokumentiert**

Andere Frameworks haben READMEs mit Best Practices. Dieses Framework hat Git Hooks die physisch blockieren:
```
git commit → spec-gate.sh → BLOCKED wenn kein specs/ISSUE-XX.md existiert
git push   → doc-version-sync.sh → BLOCKED wenn Doku-Dateien veraltet sind
```
Kein anderes Framework in diesem Vergleich hat das.

**2. Vollständige Traceability ohne manuellen Aufwand**

Jede Änderung hinterlässt automatisch eine vollständige Spur:
```
💡 Idee → /ideation → 📋 Linear Issue (4 Perspektiven + ACs)
→ specs/ISSUE-XX.md (Operator-Freigabe) → git commit "T1: ..."
→ CHANGELOG.md (auto) → Obsidian Vault (auto-sync) → Issue Done
```
6 Monate später weißt du exakt warum jede Codezeile existiert.

**3. Learning-Loop: Framework wird mit jeder Story klüger**

Beim Setup: Primärmetrik + Baseline + Ziel definieren (z.B. "WinRate 33% → 45%").
Nach jedem Issue-Close: Outcome-Check-Datum gesetzt, Ergebnis in `journal/LEARNINGS.md`.
Claude weiß nach 10 Stories: "Stories mit Typ X verbessern die Metrik, Typ Y nicht."
Kein anderes Framework tracked Business-Outcomes systematisch.

**4. Self-Healing ohne Ops-Team**

Ein Cron-Agent läuft alle 15 Minuten und korrigiert automatisch:
- Versions-Drift zwischen Doku-Dateien → auto-sync
- Gestoppte Daemons → auto-restart mit Backoff
- Telegram-Alert bei Anomalien

Solo-Entwickler oder kleines Team ohne dediziertes Ops kann damit zuverlässig produzieren.

**5. Portabel — ein Ordner, null Dependencies**

```bash
cp -r bootstrap/ /root/.claude/skills/bootstrap/
# Fertig. Keine pip install, keine npm, keine Cloud-Abhängigkeiten.
```
Läuft auf Mac, VPS, Claude Code Desktop — überall gleich.

### Wann OpenCLAW Bootstrap wählen

✅ **Ideal wenn:**
- Solo-Entwickler oder Team bis ~5 Personen
- Langlebiges Projekt (>3 Monate) das wartbar bleiben muss
- Produktion mit echten Business-Metriken (WinRate, Conversion, Latenz)
- Compliance oder Audit-Anforderungen (vollständige Traceability benötigt)
- Claude Code als primäres AI-Tool

⚠️ **Nicht ideal wenn:**
- Kurzes Experiment oder Proof-of-Concept (<2 Wochen)
- Großes Team (>10) mit eigener CI/CD-Pipeline (→ CrewAI oder AutoGen besser)
- Maximale Ausgabequalität wichtiger als Governance (→ AutoGen Debate-Pattern)
- Kein Linear-Account gewünscht (Ticket-System ist Kernvoraussetzung)

---

## Was dieser Skill macht

Wenn du `/bootstrap` in Claude Code eingibst, führt er dich durch die Einrichtung von:

| Was | Warum |
|-----|-------|
| **GOVERNANCE.md** | Blueprint für den KI-gesteuerten Entwicklungslebenszyklus — Regeln, Workflows, Qualitätsgates |
| **CLAUDE.md** | Identitäts- und Regeldatei für Claude als KI-Operator |
| **Self-Healing Agent** | Überwacht Dokumentversionen + Daemon-Gesundheit alle 15 Min (Cron) |
| **Doc-Sync-Modul** | Hält alle Docs auf derselben Version, optional gespiegelt nach Obsidian |
| **Issue-Schreibrichtlinien** | Strukturiertes Story-Format für KI + Mensch-Kollaboration |
| **Skills-Installation** | Verknüpft ideation, implement, backlog, architecture-review und mehr |
| **Linear + GitHub + Obsidian** | Verbindet deine Toolchain zu einem kohärenten Lebenszyklus |

---

## Industriestandard 7-stufiger Entwicklungsprozess

Dieses Framework orientiert sich an den bewährten Entwicklungspraktiken führender Tech-Unternehmen wie **Google, Amazon und Meta** und bildet deren **7-stufigen Software Development Lifecycle (SDLC)** vollständig mit KI-unterstützten Skills ab.

> **Kernprinzip:** Jede Entwicklungsphase — von der Idee bis zur Überwachung — wird durch einen dedizierten Claude Code Skill unterstützt. Claude Code ist der Operator, der diese Skills orchestriert und sicherstellt, dass Governance-Regeln in jeder Phase eingehalten werden.

| # | Phase | Google/Amazon-Standard | Unser Äquivalent | Skill(s) | Status |
|---|-------|------------------------|------------------|----------|--------|
| 1 | **Anforderungen** | PRD, User Stories, Stakeholder Input | `/ideation` → Linear Issue (4 Perspektiven, ACs, Abhängigkeiten) | `/ideation` | ✅ Abgedeckt |
| 2 | **Design** | Design Doc, Architecture Review, ADRs | `/ideation` (8 Dimensionen) + Architecture Design Doc im Issue + `/architecture-review` | `/ideation`, `/architecture-review` | ✅ Abgedeckt |
| 3 | **Planung** | Task Breakdown, Sprint Planning, Spec | `/implement` Schritt 4 → `specs/ISSUE-XX.md` (neu!) + `/backlog` für Priorisierung | `/implement`, `/backlog` | ✅ Abgedeckt |
| 4 | **Build** | Code, Tests, CI Pipeline | `/implement` Schritte 5–6 → Tasks aus Spec (T1→Verify→Commit→T2...) | `/implement` | ✅ Abgedeckt |
| 5 | **Review** | Code Review, QA, Security Review | `/implement` Schritt 7 → Post-Implement Validation (AC, Architektur-Quick-Check, Smoke Test, Security-Findings) | `/implement` | ✅ Abgedeckt |
| 6 | **Deploy** | CI/CD, Staging, Rollout | Git Push → Handoff → System liest CLAUDE.md. Daemon-Prozesse per Start-Scripts neugestartet | — | ⚠️ Teilweise |
| 7 | **Monitor** | Observability, Alerting, Incident Response | Self-Healing (Cron, 15 Min), Telegram-Alerts, `/breakfix`, `/status`, Morning Briefing | `/breakfix`, `/status` | ✅ Abgedeckt |

> **Fazit: 6 von 7 Phasen** sind vollständig durch Skills abgedeckt. **Deploy (Phase 6)** ist projektspezifisch und wird nicht automatisiert — hier gibt es eine separate [Monitoring-Empfehlung](#monitoring-empfehlung-außerhalb-bootstrap) mit Prometheus + Grafana.

```mermaid
flowchart LR
    P1["1️⃣ Anforderungen\n/ideation\nLinear Issue"] --> P2["2️⃣ Design\n/ideation\n/architecture-review"]
    P2 --> P3["3️⃣ Planung\n/implement\n/backlog + Spec"]
    P3 --> P4["4️⃣ Build\n/implement\nT1→T2→... Commits"]
    P4 --> P5["5️⃣ Review\n/implement\nPost-Validation"]
    P5 --> P6["6️⃣ Deploy\nGit Push\n⚠️ Manuell"]
    P6 --> P7["7️⃣ Monitor\n/breakfix + /status\nSelf-Healing"]
    P7 -.->|"Feedback Loop"| P1
```

---

## Die Kernidee

```
Idee → /ideation → Linear Issue → /backlog → /implement → Code + Docs → Git Push → Fertig
```

Jede Änderung ist:
1. **Autorisiert** durch ein Linear-Issue (kein Code ohne Ticket)
2. **Dokumentiert** im selben Commit (kein Code ohne Doku-Update)
3. **Überwacht** durch den Self-Healing Agent (Versions-Drift in 15 Min erkannt)
4. **Reproduzierbar**, weil jeder Workflow ein Skill ist

```mermaid
flowchart LR
    A["💡 Idee"] --> B["/ideation\nRecherche + Story"]
    B --> C["📋 Linear Issue"]
    C --> D["/implement\n7-stufiger SDLC"]
    D --> E{"🔒 spec-gate.sh\nSpec vorhanden?"}
    E -->|"Kein Spec"| F["Erstelle\nspecs/ISSUE-XX.md"]
    F --> E
    E -->|"Spec OK"| G{"🔒 doc-version-sync.sh\nVersionen synchron?"}
    G -->|"Drift"| H["Sync alle DOC_FILES"]
    H --> G
    G -->|"Synchron"| I["📦 git push\nCode + Docs"]
    I --> J["✅ Fertig + Changelog"]
    K["🔄 Self-Healing\nalle 15 Min"] -.-> J
```

---

## Installation

### Auf einem bestehenden Claude Code System (gleicher Server)

```bash
# Bootstrap Skill in das Claude Code Skills-Verzeichnis kopieren
cp -r bootstrap/ /root/.claude/skills/bootstrap/

# Claude Code in einem beliebigen Verzeichnis starten und eingeben:
# /bootstrap
```

### Auf einem neuen Server (portabler Modus)

```bash
# 1. Claude Code installieren
# 2. Diesen Ordner in das Skills-Verzeichnis kopieren
mkdir -p /root/.claude/skills/
cp -r bootstrap/ /root/.claude/skills/bootstrap/

# 3. Claude Code öffnen
claude

# 4. Eingeben: /bootstrap
```

Keine Abhängigkeiten auf andere Dateien. Alle Templates sind in `references/` eingebettet.

---

## Was du vorher brauchst

Claude fragt dich in Phase 0 nach folgenden Informationen:

**Pflichtangaben:**
- Projektname & Ein-Satz-Beschreibung
- Absoluter Pfad zum Projektverzeichnis
- GitHub Repository URL
- Linear Team-Name (Slug) + Issue-Präfix (z.B. `PROJ-`)
- Startversion (z.B. `1.0.0`)
- Obsidian Vault-Pfad (für Doc-Sync)

**Optional:**
- Telegram Bot Token (für Self-Healing Alerts)
- OpenRouter/Perplexity API Key (für `/research` Skill)
- Miro Board URL (für `/visualize` Skill)

---

## Dateistruktur

```
bootstrap/
├── SKILL.md                                    ← Skill-Definition (Claude liest dies)
├── README.md                                   ← Diese Datei
├── docs/
│   └── diagrams/                               ← Visuelle Diagramme (Excalidraw + PNG-Exports)
│       ├── 00-big-picture.excalidraw           ← Gesamtüberblick: 7-stufiger SDLC
│       ├── 00-big-picture.png
│       ├── 01-anforderungen.excalidraw         ← Phase 1: Anforderungen (/ideation)
│       ├── 01-anforderungen.png
│       ├── 02-design.excalidraw               ← Phase 2: Design (/architecture-review)
│       ├── 02-design.png
│       ├── 03-planung.excalidraw              ← Phase 3: Planung (/backlog + Spec)
│       ├── 03-planung.png
│       ├── 04-build.excalidraw                ← Phase 4: Build (/implement)
│       ├── 04-build.png
│       ├── 05-review.excalidraw               ← Phase 5: Review (Post-Validation)
│       ├── 05-review.png
│       ├── 06-deploy.excalidraw               ← Phase 6: Deploy (Git Push)
│       ├── 06-deploy.png
│       ├── 07-monitor.excalidraw              ← Phase 7: Monitor (/breakfix + /status)
│       └── 07-monitor.png
└── references/
    ├── info-gathering.md                       ← Checkliste der zu sammelnden Infos
    ├── file-templates.md                       ← config.js, CLAUDE.md, etc. Templates
    ├── governance-template.md                  ← Vollständige GOVERNANCE.md (eingebettet, portabel)
    ├── self-healing-template.js                ← Self-Healing Agent Starter-Code
    ├── doc-sync-template.js                    ← Doc-Sync-Modul Starter-Code
    ├── issue-writing-guidelines-template.md    ← Issue-Format-Richtlinien
    ├── skills-setup.md                         ← Symlinks vs. Kopien, Reihenfolge
    └── global-registry-update.md              ← Wie Projekt in CLAUDE.md registrieren
```

---

## Die 5 Bootstrap-Phasen

| Phase | Was passiert | Eingabe nötig? |
|-------|-------------|----------------|
| **0 — Info-Gathering** | Claude stellt 14 Fragen zum Projekt | Ja — einmalig beantworten |
| **1 — Grundstruktur** | Erstellt Verzeichnisse, Git, alle Kerndateien aus Templates | Bestätigung .env |
| **2 — Skills** | Installiert/verknüpft ideation, implement, backlog etc. | Skill-Tier wählen |
| **3 — Self-Healing** | Schreibt + testet Self-Healing + Doc-Sync | Keine |
| **4 — Daemon** (optional) | Linear Automation Daemon Skeleton | Ja, wenn aktiviert |
| **5 — Registry** | Aktualisiert globale CLAUDE.md + Memory | Keine |

```mermaid
flowchart LR
    P0["Phase 0\n📋 Info-Gathering\n14 Fragen"] --> P1["Phase 1\n🏗️ Grundstruktur\nDateien + Git + Hooks"]
    P1 --> P2["Phase 2\n🛠️ Skills\nideation + implement\n+ breakfix + ..."]
    P2 --> P3["Phase 3\n🔄 Self-Healing\nVersions-Monitor + Sync"]
    P3 --> P4["Phase 4\n⚡ Daemon\noptional"]
    P4 --> P5["Phase 5\n🌐 Registry\nGlobale CLAUDE.md"]
    P5 --> DONE["🚀 Bereit für\n/ideation"]
```

---

## Was erstellt wird

Nach `/bootstrap` hat dein Projekt:

```
mein-projekt/
├── lib/
│   ├── config.js          ← VERSION + DOC_FILES — einzige Wahrheitsquelle
│   └── doc-sync.js        ← Synchronisiert Versionen in alle Docs + Obsidian
├── agents/
│   └── self-healing.js    ← Cron-fähiger Gesundheitsmonitor (alle 15 Min)
├── CLAUDE.md              ← Claude's Identität, Fähigkeiten, Regeln
├── SYSTEM_ARCHITECTURE.md ← Architektur-Dok (beim Wachsen des Systems ausfüllen)
├── COMPONENT_INVENTORY.md ← Datei-Inventar (Self-Healing prüft dies)
├── DEVELOPMENT_PROCESS.md ← Wie in diesem Projekt entwickelt wird
├── GOVERNANCE.md          ← Das vollständige Governance-Blueprint
├── SECURITY.md            ← API Key-Richtlinie, Bedrohungsmodell
├── CHANGELOG.md           ← Automatisch aktualisiert durch doc-sync
├── .env                   ← Deine API Keys (gitignored)
├── .env.example           ← Variablen-Namen ohne Werte
└── .claude/
    ├── ISSUE_WRITING_GUIDELINES.md
    └── skills/
        ├── ideation/      → Symlink oder Kopie
        ├── implement/     → Symlink oder Kopie
        ├── backlog/       → Symlink oder Kopie
        └── ...
```

---

## Die 8 unverbrüchlichen Regeln

Claude folgt diesen Regeln im gesamten Framework:

1. **Niemals ohne Linear-Issue implementieren** — jede Änderung muss nachverfolgbar sein
2. **Niemals ein Issue ohne Changelog schließen** — die Geschichte muss vollständig sein
3. **Niemals Code ändern ohne vorherige Rückfrage** — Mensch-in-der-Schleife für Risikokontrolle
4. **Niemals "fertig" behaupten ohne Git Push** — Code muss immer im Remote sein
5. **Niemals ein Operator-Briefing in Linear kürzen** — Originaltext ist Wahrheit
6. **Niemals ein Issue ohne Labels anlegen** — Labels sind essenziell für die Filterung
7. **Niemals Sub-Tasks direkt nach Done verschieben** — immer durch "In Progress" zuerst
8. **Niemals eine API-Integration hinzufügen ohne das API-Inventar zu aktualisieren**

```mermaid
sequenceDiagram
    participant Dev as Claude Code
    participant SG as 🔒 spec-gate.sh
    participant DS as 🔒 doc-version-sync.sh
    participant GH as 📦 GitHub

    Dev->>SG: git commit Versuch
    alt Kein Spec-File gefunden
        SG-->>Dev: BLOCKIERT — erstelle specs/ISSUE-XX.md zuerst
    else Spec-File vorhanden
        SG->>DS: ✅ Spec OK — Versionen prüfen
        alt VERSION-Mismatch in DOC_FILES
            DS-->>Dev: BLOCKIERT — sync alle Docs auf aktuelle VERSION
        else Alle DOC_FILES synchron
            DS->>GH: ✅ Commit + Push
            GH-->>Dev: Fertig — Issue kann geschlossen werden
        end
    end
```

---

## Der Self-Healing-Mechanismus

```
Cron (alle 15 Min)
    └── node agents/self-healing.js
            ├── Check M: Alle DOC_FILES auf derselben VERSION wie config.js?
            │   → Nein: Alert + Auto-Sync via doc-sync.js
            ├── Check U: Alle dokumentierten Komponenten auf dem Dateisystem?
            │   → Nein: Warnung
            └── Check P: Alle Daemon-Prozesse laufen (Lock-Files)?
                → Nein: Neustart via Start-Script + Backoff
```

Die Versionsnummer in `config.js` ist die **einzige Wahrheitsquelle**. Wenn du sie erhöhst, aktualisiert Self-Healing automatisch alle Dok-Dateien beim nächsten Cron-Lauf.

---

## Alle Skills im Überblick

Dieser Bootstrap-Skill richtet dein Projekt für folgende Skills ein:

| Skill | Auslöser | Zweck | SDLC Phase |
|-------|---------|-------|------------|
| `/ideation` | "Ich habe eine Idee" | Recherche → Architektur-Design → Linear Issue | 1, 2 |
| `/implement` | "los", "starte ISSUE-XX" | 7-stufiger SDLC-Workflow mit Qualitätsgates | 3, 4, 5 |
| `/backlog` | "was steht an" | Sprint-Planung + Abhängigkeitsanalyse | 3 |
| `/architecture-review` | "Architektur prüfen" | 8-Dimensionen-Qualitätsbericht | 2 |
| `/sprint-review` | "Sprint Review" | Quartals-Audit + Tech Debt | 5 |
| `/research` | "recherchiere X" | 2-Tier: WebSearch + Perplexity Deep Research | 1, 2 |
| `/breakfix` | "System kaputt" | Incident Response: Detect → Fix → Document | 7 |
| `/status` | "Status" | System Status Dashboard | 7 |
| `/wrap-up` | "Exit", "Ende" | Session-Abschluss + Memory-Persistierung | 7 |

Alle Skills sind aus dem gleichen OpenCLAW-Framework und arbeiten zusammen.

---

## Monitoring-Empfehlung (außerhalb Bootstrap)

> ⚠️ **Wichtiger Hinweis:** Dieser Abschnitt beschreibt eine **Empfehlung** — keinen automatischen Bootstrap-Schritt. Der hier beschriebene Setup muss **manuell nach dem Bootstrap** eingerichtet werden und ist **nicht** Teil des Bootstrap-Prozesses.

Der Bootstrap-Prozess richtet ein einfaches Self-Healing-Monitoring ein (Check M, U, P — alle 15 Min). Für ein vollständiges **Produktions-Monitoring** empfehlen wir folgenden Stack — so wie wir es im CLAW Trading System produktiv einsetzen:

### Empfohlener Monitoring-Stack

| Komponente | Zweck | Kosten |
|-----------|-------|--------|
| **Prometheus** | Metriken sammeln + speichern (Time-Series-Datenbank) | Open Source — kostenlos |
| **Node Exporter** | Server-Metriken exportieren (CPU, RAM, Disk, Prozesse) | Open Source — kostenlos |
| **Grafana Cloud** | Dashboards + Alerting + Visualisierung | Free Tier verfügbar |

### Warum dieser Stack?

- **Prometheus** ist der Industriestandard für Metriken-Sammlung (verwendet von Google, Netflix, Spotify)
- **Grafana Cloud** ermöglicht professionelle Dashboards ohne eigene Infrastruktur
- Der **`/grafana` Skill** (OpenCLAW Framework) erstellt und verwaltet Dashboards direkt via Grafana MCP Server — Claude baut die Dashboards für dich

### Kurzanleitung

```bash
# 1. Prometheus auf deinem Server installieren
# → Offizielle Doku: https://prometheus.io/docs/introduction/first_steps/

# 2. Node Exporter für Server-Metriken installieren
# → Offizielle Doku: https://prometheus.io/docs/guides/node-exporter/

# 3. Grafana Cloud Account anlegen (Free Tier reicht für den Einstieg)
# → https://grafana.com/products/cloud/

# 4. Prometheus als Data Source in Grafana konfigurieren
# → Grafana UI: Connections → Data Sources → Prometheus → URL eingeben

# 5. Optional: /grafana Skill in Claude Code installieren
# → Erstellt und verwaltet Dashboards direkt via Grafana MCP Server
cp -r /root/.claude/skills/grafana/ .claude/skills/grafana/
```

### Was du damit überwachst

- Laufende Daemon-Prozesse (via Node Exporter)
- API-Latenz und Fehlerrate (Custom Metriken)
- Versions-Drift-Warnungen (Self-Healing Alerts)
- Projekt-spezifische Business-Metriken

---

## Portabilität

Dieser Skill hat **keine externen Abhängigkeiten**:

| Gebraucht | Quelle |
|-----------|--------|
| GOVERNANCE.md Inhalt | `references/governance-template.md` (eingebettet) |
| Self-Healing Skript | `references/self-healing-template.js` (eingebettet) |
| Doc-Sync Skript | `references/doc-sync-template.js` (eingebettet) |
| Issue-Richtlinien | `references/issue-writing-guidelines-template.md` (eingebettet) |
| Datei-Templates | `references/file-templates.md` (eingebettet) |

Den `bootstrap/` Ordner irgendwohin kopieren → es funktioniert sofort.

---

## Voraussetzungen

### Pflicht

| Was | Warum |
|-----|-------|
| **Claude Code** | claude.ai/claude-code — der KI-Operator |
| **Node.js** | für self-healing + doc-sync |
| **GitHub Repository** | bereits angelegt (leer oder mit Code) |
| **SSH-Zugang zu GitHub** | damit `git push` ohne Passwort funktioniert — **siehe unten** |
| **Linear** Account | Issue-Tracking (Free Tier reicht) |

### SSH-Zugang zu GitHub einrichten

Bootstrap führt am Ende `git push` aus. Dafür muss SSH konfiguriert sein.

**Mac / lokaler PC:**
```bash
# 1. SSH Key generieren (falls noch keiner vorhanden)
ssh-keygen -t ed25519 -C "deine@email.com"
# → Key liegt in ~/.ssh/id_ed25519.pub

# 2. Public Key in GitHub hinterlegen
# GitHub → Settings → SSH and GPG Keys → New SSH Key
cat ~/.ssh/id_ed25519.pub  # diesen Text in GitHub einfügen

# 3. Testen
ssh -T git@github.com
# Erwartet: "Hi username! You've successfully authenticated..."
```

**VPS / Server (z.B. Hostinger):**
```bash
# Gleicher Prozess — auf dem Server ausführen
ssh-keygen -t ed25519 -C "vps@meinprojekt.com"
cat ~/.ssh/id_ed25519.pub
# → In GitHub unter Settings → SSH Keys hinterlegen
ssh -T git@github.com  # Test
```

**Claude Code Desktop (Mac App):**
```bash
# Claude Code nutzt den SSH-Agenten des Systems
# Wenn ssh -T git@github.com funktioniert → Claude Code kann pushen
# Falls nicht: ssh-add ~/.ssh/id_ed25519
```

> **Hinweis:** Bootstrap prüft SSH in Phase 0 automatisch mit `ssh -T git@github.com`.
> Wenn der Test fehlschlägt, hält Bootstrap an und zeigt die Einrichtungsanleitung.

### Optional

| Was | Wofür |
|-----|-------|
| **Obsidian** | Doc-Sync in Vault |
| **Telegram Bot** | Self-Healing Alerts |
| **OpenRouter API Key** | `/research` Deep-Tier via Perplexity |
| **Hostinger API Key** | `/cloud-system-engineer` Skill |
| **Miro Access Token** | `/visualize` Skill |
| **notebooklm-py** CLI | `/notebooklm` Skill |
| **Grafana Cloud Account** | Monitoring-Dashboards (Empfehlung — siehe oben) |
| **Prometheus** | Metriken-Sammlung (Empfehlung — siehe oben) |

---

## Lizenz

MIT — frei verwendbar, adaptierbar für dein Projekt.

Teil des **OpenCLAW Governance Frameworks**.
Quelle: [github.com/vibercoder79/openclaw_trading](https://github.com/vibercoder79/openclaw_trading)
