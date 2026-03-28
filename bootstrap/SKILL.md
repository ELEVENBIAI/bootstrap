---
name: bootstrap
version: 2.0.0
description: Richtet ein neues Projekt mit dem OpenCLAW Governance Framework ein. Interaktiver Prompt-gefuehrter Prozess in 5 Phasen. Verwenden wenn der Operator ein neues Projekt aufsetzen will oder "/bootstrap" sagt.
tools: [Read, Write, Edit, Bash, Glob, Grep]
portable: true
---

# Bootstrap — Neues Projekt aufsetzen

Interaktiver 5-Phasen-Workflow fuer ein neues Projekt mit OpenCLAW Governance.

**Vollstaendig portabel:** Alle Vorlagen sind in `references/` eingebettet — keine externen Abhaengigkeiten.

Referenzen:
- `references/info-gathering.md` — Pflicht-Infos vor dem Setup
- `references/file-templates.md` — config.js, CLAUDE.md, CHANGELOG, API_INVENTORY, INDEX, PROCESS_CATALOG, specs/TEMPLATE etc.
- `references/governance-template.md` — GOVERNANCE.md vollstaendig eingebettet (inkl. Spec-Driven Dev + ADR)
- `references/hooks-setup.md` — Git Hook Templates (spec-gate.sh, doc-version-sync.sh)
- `references/self-healing-template.js` — Self-Healing Agent Starter
- `references/doc-sync-template.js` — Doc-Sync Module Starter
- `references/issue-writing-guidelines-template.md` — Issue Writing Guidelines
- `references/skills-setup.md` — Symlinks vs. Kopie, Reihenfolge
- `references/global-registry-update.md` — CLAUDE.md + MEMORY.md aktualisieren

---

## Phase 0: Info-Gathering — HUMAN-IN-THE-LOOP

**Lies zuerst** `references/info-gathering.md` fuer die vollstaendige Liste.

### Schritt 0.1: Stack-Frage ZUERST — alleine stellen, auf Antwort warten

Stelle diese eine Frage zuerst — bevor alle anderen:

```
Was möchtest du entwickeln?

a) Node.js / JavaScript Backend (API, CLI, Daemon, Trading-System)
b) Frontend (React, Vue, Vanilla JS — laeuft im Browser)
c) Full-Stack (Node.js Backend + Frontend)
d) Python (KI/ML, Scripts, FastAPI, Django, Data Science)
e) Anderes / Noch nicht klar → kurz beschreiben
```

**Warte auf Antwort.** Die Antwort bestimmt welche Tooling-Dateien Bootstrap anlegt:

| Stack | Linter | Formatter | Config-Dateien |
|-------|--------|-----------|----------------|
| a) Node.js/JS | ESLint | — | `eslint.config.mjs`, `sonar-project.properties` |
| b) Frontend | ESLint + Prettier | Prettier | `eslint.config.mjs`, `.prettierrc`, `sonar-project.properties` |
| c) Full-Stack | ESLint + Prettier | Prettier | `eslint.config.mjs`, `.prettierrc`, `sonar-project.properties` |
| d) Python | Ruff / Flake8 | Black | `pyproject.toml`, `sonar-project.properties` |
| e) Anderes | Gemeinsam entscheiden | — | Je nach Sprache |

Speichere die Antwort als `{{STACK}}` fuer Phase 1.

---

### Schritt 0.2: Restliche Fragen — alle auf einmal

Dann stelle dem Operator diese Fragen als nummerierten Block:

```
Ich brauche folgende Infos fuer das Setup:

PFLICHT:
1. Projektname? (z.B. MyAnalytics)
2. Ein-Satz-Beschreibung? (Was macht das System?)
3. Absoluter Pfad zum Projekt-Verzeichnis?
4. GitHub Repository URL?
5. Linear Team-Name (Slug)?
6. Issue-Prefix? (z.B. PROJ-)
7. Start-Version? (z.B. 1.0.0)
8. Absoluter Pfad zum Obsidian Vault?

OPTIONAL (leer lassen wenn nicht gewuenscht):
9. Telegram Bot Token fuer Alerts?
10. Perplexity / OpenRouter API Key fuer Deep Research?
11. Miro Board URL fuer /visualize?
12. Automation Daemon einrichten? (Ja/Nein, default: Nein)

SKILLS:
13. Welche Skills installieren?
    a) Minimum (ideation, implement, backlog) — empfohlen fuer Start
    b) Standard (+ architecture-review, sprint-review, research, breakfix)
    c) Voll (alle Skills: + cloud-system-engineer, visualize, skill-creator, integration-test, status, calibrate)
    d) Manuell auswaehlen
    e) Optional-Stack angeben (grafana, supabase, vercel)

DOMAIN:
14. Welche Architektur-Dimensionen sind relevant?
    Standard: Reliability, Data Integrity, Security, Performance, Observability, Maintainability
    Optional: Cost Efficiency, Signal Quality, oder eigene?
```

Warte auf Antworten. Dann weiter mit Phase 1.

---

## Phase 1: Grundstruktur anlegen

Pruefe ob PROJECT_PATH existiert. Wenn nicht: frage ob anlegen.

### 1.1 Verzeichnisstruktur

```bash
mkdir -p {PROJECT_PATH}/lib
mkdir -p {PROJECT_PATH}/agents
mkdir -p {PROJECT_PATH}/journal
mkdir -p {PROJECT_PATH}/specs
mkdir -p {PROJECT_PATH}/docs
mkdir -p {PROJECT_PATH}/data
mkdir -p {PROJECT_PATH}/signals
mkdir -p {PROJECT_PATH}/.claude/skills
mkdir -p {PROJECT_PATH}/.claude/hooks
```

### 1.2 Git-Repo initialisieren

```bash
cd {PROJECT_PATH}
git init
git remote add origin https://{GITHUB_REPO}.git
```

Erstelle `.gitignore` (aus `references/file-templates.md` Sektion .gitignore).

### 1.3 Kern-Dateien erstellen

Aus `references/file-templates.md` mit Operator-Angaben befuellen:

**Immer anlegen (stack-unabhaengig):**

| Datei | Template-Sektion |
|-------|-----------------|
| `lib/config.js` | config.js |
| `CLAUDE.md` | CLAUDE.md |
| `SYSTEM_ARCHITECTURE.md` | SYSTEM_ARCHITECTURE.md |
| `COMPONENT_INVENTORY.md` | COMPONENT_INVENTORY.md |
| `.env.example` | .env.example |
| `CHANGELOG.md` | CHANGELOG.md |
| `API_INVENTORY.md` | API_INVENTORY.md |
| `INDEX.md` | INDEX.md |
| `PROCESS_CATALOG.md` | PROCESS_CATALOG.md |
| `specs/TEMPLATE.md` | specs-template |
| `sonar-project.properties` | sonar-project.properties |

**Stack-abhaengige Tooling-Dateien (basierend auf {{STACK}} aus Phase 0.1):**

| Stack | Anlegen |
|-------|---------|
| a) Node.js/JS | `eslint.config.mjs` (Template: eslint.config.mjs) |
| b) Frontend | `eslint.config.mjs` + `.prettierrc` (Template: .prettierrc) |
| c) Full-Stack | `eslint.config.mjs` + `.prettierrc` (Template: .prettierrc) |
| d) Python | `pyproject.toml` (Template: pyproject.toml) — kein ESLint |
| e) Anderes | Gemeinsam mit Operator entscheiden |

Dem Operator nach Abschluss mitteilen welche Tooling-Dateien angelegt wurden und
welche VS Code Extensions dazu passen (aus `references/file-templates.md` Sektion
"VS Code Extensions je Stack").

Ausserdem anlegen (aus eingebetteten Templates — **kein cp von externen Pfaden noetig**):

**GOVERNANCE.md** — aus `references/governance-template.md` lesen und schreiben:
- Alle `{{PLATZHALTER}}` mit Operator-Angaben ersetzen:
  - `{{PROJECT_NAME}}` → Projektname
  - `{{VERSION_START}}` → Start-Version
  - `{{TODAY}}` → heutiges Datum
  - `{{ISSUE_PREFIX}}` → Issue-Prefix (in Regelwerk-Sektionen, z.B. `PROJ-`)

**agents/self-healing.js** — aus `references/self-healing-template.js` lesen und schreiben:
- Keine Platzhalter im Code (alles konfigurierbar ueber config.js)
- `DAEMON_CHECKS`-Array mit projektspezifischen Daemons befuellen (leer lassen wenn unklar)

**lib/doc-sync.js** — aus `references/doc-sync-template.js` lesen und schreiben:
- `OBSIDIAN_MAPPING` mit Vault-Pfaden befuellen (aus Antwort 8)

**.claude/ISSUE_WRITING_GUIDELINES.md** — aus `references/issue-writing-guidelines-template.md` lesen und schreiben:
- `{{PROJECT_NAME}}` ersetzen

Direkt anlegen (kurze Skelette):
- `DEVELOPMENT_PROCESS.md` — Verweis auf GOVERNANCE.md §4, projekt-spezifische Ergaenzungen
- `SECURITY.md` — Minimales Skelett: API Key Policy, Threat Model Placeholder

### 1.4 .env anlegen

Dem Operator mitteilen:
```
Bitte erstelle {PROJECT_PATH}/.env und trage dein LINEAR_API_KEY ein.
Variablen-Namen stehen in .env.example.
NIEMALS echte Keys im Chat nennen.
```

Warte auf Bestaetigung "done" bevor weiter.

### 1.4a Git Hooks einrichten (Governance-Enforcement)

Lies `references/hooks-setup.md` fuer die vollstaendigen Hook-Templates.

**spec-gate.sh** — blockiert `git commit ISSUE-XX` wenn kein Spec-File existiert:
```bash
cp {bootstrap-path}/references/hooks-setup.md /tmp/_hooks_ref.md
# Hook aus Template entnehmen und schreiben:
cat > {PROJECT_PATH}/.claude/hooks/spec-gate.sh << 'EOF'
# [aus references/hooks-setup.md Sektion spec-gate kopieren]
EOF
chmod +x {PROJECT_PATH}/.claude/hooks/spec-gate.sh
```

**doc-version-sync.sh** — blockiert `git commit` wenn config.js VERSION erhoehen aber Doku-Dateien noch auf alter Version:
```bash
cat > {PROJECT_PATH}/.claude/hooks/doc-version-sync.sh << 'EOF'
# [aus references/hooks-setup.md Sektion doc-version-sync kopieren]
EOF
chmod +x {PROJECT_PATH}/.claude/hooks/doc-version-sync.sh
```

**Hooks in `.claude/settings.json` registrieren:**
```json
{
  "hooks": {
    "PreToolUse": [],
    "PostToolUse": []
  }
}
```

Dem Operator mitteilen: "Hooks sind angelegt. Aktivierung via `.claude/settings.json` oder manuell."

### 1.5 Linear Labels einrichten

Anleiten: In Linear mindestens anlegen: `architecture`, `bug`, `feature`, `refactor`, `docs`, `infra`
Plus domain-spezifische Labels aus Antwort 14.

### 1.6 Ersten Git-Commit

```bash
cd {PROJECT_PATH}
git add CLAUDE.md SYSTEM_ARCHITECTURE.md COMPONENT_INVENTORY.md DEVELOPMENT_PROCESS.md
git add GOVERNANCE.md SECURITY.md CHANGELOG.md .gitignore .env.example
git add lib/config.js lib/doc-sync.js agents/self-healing.js
git add .claude/ISSUE_WRITING_GUIDELINES.md
git commit -m "v{VERSION_START} — Initial Governance Setup"
git push -u origin main
```

Phase 1 Checkpoint: Kurze Zusammenfassung ausgeben was angelegt wurde.

---

## Phase 2: Skills installieren

Lies `references/skills-setup.md` fuer Details zu Symlinks vs. Kopie.

Basierend auf Antwort 13 die Skills verlinken oder kopieren.

**Schritt 2.1: Pruefen ob Skills lokal vorhanden**

```bash
ls /root/.claude/skills/ideation 2>/dev/null && echo "LOKAL" || echo "DOWNLOAD"
```

**Wenn LOKAL (Standard — gleiche Maschine):**

```bash
cd {PROJECT_PATH}
# Fuer jeden gewaehlten Skill:
ln -s /root/.claude/skills/{skill-name} .claude/skills/{skill-name}
```

**Wenn DOWNLOAD (neue Maschine — automatisch von GitHub holen):**

```bash
SKILLS_DIR="/root/.claude/skills"
REPO="https://github.com/vibercoder79/KI-Masterclass-Koerting-.git"
mkdir -p "$SKILLS_DIR"

# Sparse Clone — nur Skills, kein Trading-System-Code
cd /tmp
git clone --filter=blob:none --sparse "$REPO" ki-masterclass-skills 2>/dev/null
cd ki-masterclass-skills

# Gewaehlte Skills holen (Minimum: ideation implement backlog)
git sparse-checkout set ideation implement backlog architecture-review sprint-review research skill-creator visualize

# Nach /root/.claude/skills/ kopieren
cp -r ideation implement backlog architecture-review sprint-review research skill-creator visualize "$SKILLS_DIR/"
cd /tmp && rm -rf ki-masterclass-skills
echo "Skills installiert in $SKILLS_DIR"
```

Dann Symlinks ins Projekt setzen:
```bash
cd {PROJECT_PATH}
for SKILL in {gewaehlte-skills}; do
  ln -s /root/.claude/skills/$SKILL .claude/skills/$SKILL
done
```

Minimum (a): ideation, implement, backlog
Standard (b): + architecture-review, sprint-review, research, breakfix
Voll (c): + cloud-system-engineer, visualize, skill-creator, integration-test, status, calibrate
Optional: grafana (wenn Prometheus/Grafana Stack), supabase (wenn Supabase DB), vercel (wenn Vercel Deployment)

Danach domain-spezifische Anpassung (wenn Skills kopiert wurden):
- `ideation/references/story-template-feature.md` — Domain-Sektionen anpassen
- `ideation/references/architecture-dimensions.md` — Dimensionen aus Antwort 14
- `implement/references/change-checklist.md` — Spezial-Checklisten anpassen

Phase 2 Checkpoint: Liste der installierten Skills ausgeben.

---

## Phase 3: Self-Healing und Doc-Sync

### 3.1 Dateien bereits in Phase 1 erstellt

`agents/self-healing.js` und `lib/doc-sync.js` wurden in Phase 1 aus den eingebetteten Templates erstellt.

### 3.2 Anpassen (falls noetig)

In `agents/self-healing.js`:
- `DAEMON_CHECKS`-Array befuellen wenn Projekt Daemon-Prozesse hat
- Optional: Telegram-Alert aktivieren (TELEGRAM_BOT_TOKEN aus Antwort 9)

In `lib/doc-sync.js`:
- `OBSIDIAN_MAPPING` mit Vault-Pfaden befuellen (aus Antwort 8)

### 3.3 Test ausfuehren

```bash
cd {PROJECT_PATH}
node agents/self-healing.js
```

Erwartet: "All X docs at version {VERSION_START}"

### 3.4 Cron-Job

Cron ergaenzen (alle 15 Minuten):
```
*/15 * * * * cd {PROJECT_PATH} && node agents/self-healing.js >> /var/log/self-healing-{slug}.log 2>&1
```

Phase 3 Checkpoint: Self-Healing Test-Output zeigen.

---

## Phase 4: Automation Daemon (nur wenn Antwort 12 = Ja)

### 4.1 Daemon-Datei anlegen

Erstelle `agents/linear-automation-daemon.js` — minimales Skelett:

```javascript
// agents/linear-automation-daemon.js
// Polls journal/automation-queue.json, runs claude -p for each queued issue
'use strict';

const fs = require('fs');
const path = require('path');
const { execSync } = require('child_process');

const PROJECT_PATH = process.env.PROJECT_PATH || path.join(__dirname, '..');
const QUEUE_PATH   = path.join(PROJECT_PATH, 'journal/automation-queue.json');
const ISSUE_PREFIX = require(path.join(PROJECT_PATH, 'lib/config')).CONFIG?.ISSUE_PREFIX || 'ISSUE-';

function readQueue() {
  try { return JSON.parse(fs.readFileSync(QUEUE_PATH, 'utf8')); } catch { return []; }
}

async function processQueue() {
  const queue = readQueue().filter(e => e.status === 'queued');
  for (const entry of queue) {
    console.log(`[Daemon] Processing ${entry.issueId}...`);
    // Update status
    entry.status = 'running';
    // TODO: call claude -p "/implement ISSUE-XX"
    // execSync(`claude -p "/implement ${entry.issueId}"`, { cwd: PROJECT_PATH, stdio: 'inherit' });
    entry.status = 'done';
  }
}

setInterval(processQueue, 30 * 1000);
console.log('[Daemon] Linear Automation Daemon started');
```

### 4.2 Nested-Session-Fix

Dem Operator mitteilen: "Bitte in .env ergaenzen:"
```
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC=1
```

### 4.3 Webhook in Linear konfigurieren

Anleitung:
1. Linear → Settings → API → Webhooks
2. URL: `https://{VPS_IP}:{PORT}/webhook`
3. Events: "Issue updated"
4. Secret generieren → als `LINEAR_WEBHOOK_SECRET` in `.env` eintragen

### 4.4 Daemon starten

```bash
cd {PROJECT_PATH}
node agents/linear-automation-daemon.js &
```

Phase 4 Checkpoint: Daemon-Status pruefen.

---

## Phase 5: Global Registry und Finalisierung

Lies `references/global-registry-update.md` fuer genaue Textstellen.

### 5.1 /root/.claude/CLAUDE.md aktualisieren

Projektstruktur-Tabelle um neuen Eintrag ergaenzen.

### 5.2 Memory-Datei anlegen (falls vorhanden)

```
/root/.claude/projects/{project-memory-path}/memory/MEMORY.md
```

Eintrag: Projekt-Pfad, Linear, GitHub, Obsidian, installierte Skills.

### 5.3 Projekt-Memory-Datei anlegen

```
/root/.claude/projects/{project-slug}/memory/project_{slug}_init.md
```

Inhalt: Projekt-Pfad, Linear, GitHub, Obsidian, installierte Skills, ausstehende Punkte.

### 5.4 Finaler Git-Commit

```bash
cd {PROJECT_PATH}
git add -A
git commit -m "v{VERSION_START} — Complete Governance Bootstrap"
git push
```

### 5.5 Abschluss-Tabelle ausgeben

| Phase | Was | Status |
|-------|-----|--------|
| Phase 0 | Info-Gathering | done |
| Phase 1 | Grundstruktur (Dateien, Git, Linear-Labels) | done |
| Phase 2 | Skills installiert + angepasst | done |
| Phase 3 | Self-Healing + Doc-Sync (aus eingebetteten Templates) | done |
| Phase 4 | Automation Daemon | done / skipped |
| Phase 5 | Global Registry aktualisiert | done |

Naechste Schritte:
1. `cd {PROJECT_PATH} && claude` — erstes Projekt-Gespraech starten
2. `/ideation` — erste Story erstellen
3. CLAUDE.md um projektspezifische Architektur ergaenzen wenn System waechst

---

## Fehlerbehandlung

| Problem | Loesung |
|---------|---------|
| `git push` schlaegt fehl | SSH Key pruefen: `ssh -T git@github.com` |
| Linear API Fehler | LINEAR_API_KEY in .env pruefen |
| Self-Healing Mismatch beim ersten Lauf | Normal — doc-sync laeuft einmalig durch |
| Daemon startet nicht | Port belegt: anderen Port in .env setzen |
| Nested Session Fehler | CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC=1 in .env setzen |
| Andere Skills nicht vorhanden | Skills separat installieren (Symlinks oder Kopien) |

---

## Portabilitaet

Dieser Skill ist **vollstaendig portabel** — keine externen Dateisystem-Abhaengigkeiten:

| Was wird benoetigt | Wo es herkommt |
|--------------------|----------------|
| GOVERNANCE.md Inhalt | `references/governance-template.md` (eingebettet) |
| Self-Healing Script | `references/self-healing-template.js` (eingebettet) |
| Doc-Sync Script | `references/doc-sync-template.js` (eingebettet) |
| Issue Writing Guidelines | `references/issue-writing-guidelines-template.md` (eingebettet) |
| Datei-Templates | `references/file-templates.md` (eingebettet) |
| Skill-Referenzen (ideation etc.) | Separat installieren oder von GitHub |

Um diesen Skill auf einer neuen Maschine zu verwenden:
1. Kopiere den `bootstrap/` Ordner nach `/root/.claude/skills/bootstrap/`
2. Sage Claude: `/bootstrap`
3. Claude fuehrt den kompletten Setup-Workflow aus — ohne Zugriff auf externe Pfade
