---
name: bootstrap
version: 1.1.0
description: Richtet ein neues Projekt mit dem OpenCLAW Governance Framework ein. Interaktiver Prompt-gefuehrter Prozess in 5 Phasen. Verwenden wenn der Operator ein neues Projekt aufsetzen will oder "/bootstrap" sagt.
tools: [Read, Write, Edit, Bash, Glob, Grep]
portable: true
---

# Bootstrap — Neues Projekt aufsetzen

Interaktiver 5-Phasen-Workflow fuer ein neues Projekt mit OpenCLAW Governance.

**Vollstaendig portabel:** Alle Vorlagen sind in `references/` eingebettet — keine externen Abhaengigkeiten.

Referenzen:
- `references/info-gathering.md` — Pflicht-Infos vor dem Setup
- `references/file-templates.md` — config.js, CLAUDE.md, CHANGELOG etc.
- `references/governance-template.md` — GOVERNANCE.md vollstaendig eingebettet
- `references/self-healing-template.js` — Self-Healing Agent Starter
- `references/doc-sync-template.js` — Doc-Sync Module Starter
- `references/issue-writing-guidelines-template.md` — Issue Writing Guidelines
- `references/skills-setup.md` — Symlinks vs. Kopie, Reihenfolge
- `references/global-registry-update.md` — CLAUDE.md + MEMORY.md aktualisieren

---

## Phase 0: Info-Gathering — HUMAN-IN-THE-LOOP

**Lies zuerst** `references/info-gathering.md` fuer die vollstaendige Liste.

Dann stelle dem Operator diese Fragen — alle auf einmal, als nummerierten Block:

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
    b) Standard (+ architecture-review, sprint-review, research)
    c) Voll (alle 9 Skills)
    d) Manuell auswaehlen

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

| Datei | Template-Sektion |
|-------|-----------------|
| `lib/config.js` | config.js |
| `CLAUDE.md` | CLAUDE.md |
| `SYSTEM_ARCHITECTURE.md` | SYSTEM_ARCHITECTURE.md |
| `COMPONENT_INVENTORY.md` | COMPONENT_INVENTORY.md |
| `.env.example` | .env.example |
| `CHANGELOG.md` | CHANGELOG.md |

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

**Wenn dieser Bootstrap-Skill selbst unter `/root/.claude/skills/` liegt (Standard-Setup auf einer Maschine mit Claude Code):**

```bash
cd {PROJECT_PATH}
# Fuer jeden gewaehlten Skill:
ln -s /root/.claude/skills/{skill-name} .claude/skills/{skill-name}
```

**Wenn dieser Skill auf einer anderen Maschine laeuft (portabler Modus):**

Die Skills muessen separat installiert werden. Hinweis an den Operator:
```
Der Bootstrap-Skill ist portabel — aber die anderen Skills (ideation, implement, etc.)
sind NICHT in diesem Paket enthalten. Bitte besorge sie separat oder installiere
das vollstaendige OpenCLAW Skills-Paket von:
https://github.com/vibercoder79/openclaw_trading
```

Minimum (a): ideation, implement, backlog
Standard (b): + architecture-review, sprint-review, research
Voll (c): + cloud-system-engineer, visualize, skill-creator

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
