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
- `references/file-templates.md` — config.js, CLAUDE.md, .env.example (mit Key-Erklaerungen), CHANGELOG, API_INVENTORY, INDEX, PROCESS_CATALOG, specs/TEMPLATE etc.
- `references/governance-template.md` — GOVERNANCE.md vollstaendig eingebettet (inkl. Spec-Driven Dev + ADR)
- `references/hooks-setup.md` — Git Hook Templates (spec-gate.sh, doc-version-sync.sh)
- `references/mcp-setup.md` — MCP-Server-Setup fuer Linear, Grafana, Supabase, Hostinger etc. + settings.json Template
- `references/telegram-setup.md` — Telegram Bot erstellen, Chat-ID ermitteln, Self-Healing-Integration, Linear-Webhook
- `references/grafana-monitoring.md` — Grafana Cloud + Alloy + /grafana Skill Nutzungsmuster
- `references/agent-patterns.md` — 4 Team-Patterns als .claude/rules/ Vorlage (Lazy Loading)
- `references/self-healing-template.js` — Self-Healing Agent Starter
- `references/doc-sync-template.js` — Doc-Sync Module Starter
- `references/issue-writing-guidelines-template.md` — Issue Writing Guidelines
- `references/skills-setup.md` — Symlinks vs. Kopie, Reihenfolge, generierte Skills
- `references/global-registry-update.md` — CLAUDE.md + MEMORY.md aktualisieren
- `references/breakfix-template.md` — Skeleton fuer /breakfix (generiert, nicht kopiert)
- `references/integration-test-template.md` — Skeleton fuer /integration-test (generiert)
- `references/status-template.md` — Skeleton fuer /status (generiert)
- `references/wrap-up-template.md` — Generischer /wrap-up Skill (Session-Abschluss + Memory)

---

## Pre-Flight: Voraussetzungen pruefen

Bevor Phase 0 startet: pruefen ob alle technischen Voraussetzungen erfuellt sind.
**Kein Setup starten wenn Pre-Flight fehlschlaegt** — erst beheben.

### SSH-Check (PFLICHT fuer git push)

```bash
ssh -T git@github.com 2>&1
```

**Erwartet:** `Hi <username>! You've successfully authenticated...`

| Ergebnis | Bedeutung | Massnahme |
|----------|-----------|-----------|
| `Hi username!...` | SSH OK | weiter mit Phase 0 |
| `Permission denied (publickey)` | Kein SSH Key in GitHub | Anleitung unten ausgeben |
| `Could not resolve hostname` | Netzwerk-Problem | Internetverbindung pruefen |

**Bei Fehler: Anleitung ausgeben und warten:**

```
SSH-Zugang zu GitHub fehlt. Bitte einrichten:

Schritt 1 — SSH Key generieren (falls noch keiner vorhanden):
  ssh-keygen -t ed25519 -C "deine@email.com"
  → speichert in: ~/.ssh/id_ed25519 (privat) + ~/.ssh/id_ed25519.pub (öffentlich)

Schritt 2 — Public Key in GitHub hinterlegen:
  cat ~/.ssh/id_ed25519.pub
  → diesen Text kopieren
  → GitHub → Settings → SSH and GPG Keys → New SSH Key → einfügen

Schritt 3 — Test:
  ssh -T git@github.com
  → Erwartet: "Hi <username>! You've successfully authenticated..."

Sag mir "SSH OK" wenn der Test funktioniert, dann geht es weiter.
```

**Auf "SSH OK" warten, dann erneut testen, dann weiter.**

### Node.js-Check (fuer Self-Healing + Doc-Sync)

```bash
node --version 2>&1
```

Falls nicht vorhanden: Operator bitten Node.js zu installieren (https://nodejs.org).

---

### Obsidian Sync Setup (PFLICHT wenn Vault genutzt wird)

Stelle dem Operator diese eine Frage — alleine, auf Antwort warten:

```
Wo laeuft Claude Code?

a) Lokal (Mac / Windows PC) — Obsidian Desktop laeuft auf demselben Rechner
b) Remote (VPS / Docker / Server) — kein GUI, kein Obsidian Desktop
```

Speichere die Antwort als `{{OBSIDIAN_MODE}}`.

---

#### Szenario a) Lokal — Obsidian Desktop

Keine zusaetzliche Installation noetig.

**Wie es funktioniert:**
- `lib/doc-sync.js` schreibt `.md`-Dateien direkt in den lokalen Vault-Ordner
- Obsidian Desktop liest den Vault-Ordner nativ
- Obsidian Sync (cloud) laeuft innerhalb der Desktop-App — keine separaten Credentials noetig

**Voraussetzung pruefen:**

```
Ist Obsidian Desktop installiert und der Vault-Ordner bereits angelegt?
Wenn ja: Vault-Pfad in Phase 0 Frage 8 angeben — fertig.
Wenn nein: Obsidian installieren (https://obsidian.md) und Vault anlegen.
```

Warte auf Bestaetigung, dann weiter.

---

#### Szenario b) Remote / VPS — obsidian-headless

Auf einem Server ohne GUI muss `obsidian-headless` als Daemon eingerichtet werden.
Dieser haelt die Obsidian-Sync-Credentials und spiegelt den lokalen Vault-Ordner in die Obsidian Cloud.

**Schritt 1 — Obsidian Sync Account pruefen:**

```
Voraussetzung: Obsidian Sync Abo (nicht kostenlos — obsidian.md/pricing).
Du brauchst: E-Mail + Passwort des Obsidian-Accounts.
```

**Schritt 2 — obsidian-headless installieren:**

```bash
npm install -g obsidian-headless
```

**Schritt 3 — Login + auth_token holen:**

```bash
mkdir -p ~/.config/obsidian-headless
obsidian-headless login
# Gibt auth_token aus — wird automatisch in ~/.config/obsidian-headless/auth_token gespeichert
```

Bei Fehler: manuell Token anfordern:
```bash
curl -s -X POST https://sync.obsidian.md/login \
  -H "Content-Type: application/json" \
  -d '{"email":"deine@email.com","password":"deinPasswort"}' \
  | jq -r '.token' > ~/.config/obsidian-headless/auth_token
```

**Schritt 4 — Vault-Name in Obsidian ermitteln:**

```
In Obsidian Desktop (oder obsidian.md): Settings → Sync → Remote Vault → Name notieren.
Dieser Name wird als Parameter uebergeben.
```

**Schritt 5 — Daemon starten und testen:**

```bash
# Einmaliger Test (Vault-Name ersetzen):
obsidian-headless sync --path /path/to/vault --vault "Dein Vault Name"

# Erwartet: "Sync started", keine Fehler
```

**Schritt 6 — Daemon als Hintergrundprozess einrichten (systemd oder nohup):**

Option A — systemd (empfohlen fuer VPS):
```bash
cat > /etc/systemd/system/obsidian-sync.service << 'EOF'
[Unit]
Description=Obsidian Headless Sync
After=network.target

[Service]
ExecStart=/usr/bin/obsidian-headless sync --path /path/to/vault
Restart=always
RestartSec=10
User=root

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable obsidian-sync
systemctl start obsidian-sync
systemctl status obsidian-sync
```

Option B — nohup (schnell, kein systemd):
```bash
nohup obsidian-headless sync --path /path/to/vault >> /var/log/obsidian-sync.log 2>&1 &
echo $! > /var/run/obsidian-sync.pid
```

**Schritt 7 — Pruefen ob Daemon laeuft:**

```bash
ps aux | grep obsidian-headless | grep -v grep
# Erwartet: Prozess sichtbar
```

**Credentials-Speicherort:** `~/.config/obsidian-headless/auth_token`
**Log-Pfad (Option B):** `/var/log/obsidian-sync.log`

Warte auf Bestaetigung "Obsidian Sync OK", dann weiter.

---

**Zusammenfassung: Welches Szenario — was tun:**

| Szenario | Vault-Schreiben | Cloud-Sync | Credentials |
|----------|----------------|------------|-------------|
| a) Lokal | `doc-sync.js` → lokaler Ordner | Obsidian Desktop App | In Obsidian Desktop |
| b) Remote/VPS | `doc-sync.js` → Ordner auf Server | `obsidian-headless` Daemon | `~/.config/obsidian-headless/auth_token` |

Speichere `{{OBSIDIAN_VAULT_PATH}}` fuer Phase 3.2 (wird dort in `lib/doc-sync.js` eingetragen).

---

## Phase 0a: Global Claude Code Settings pruefen + einrichten

**Vor dem Projekt-Setup:** Die globale Claude Code Konfiguration bestimmt das Verhalten
in ALLEN Projekten auf dieser Maschine. Bootstrap prueft und richtet sie ein — einmalig.

---

### 0a.1 — ~/.claude/settings.json pruefen

```bash
cat ~/.claude/settings.json 2>/dev/null || echo "NICHT VORHANDEN"
```

**Pruefe folgende Felder:**

| Feld | Soll-Wert | Bedeutung |
|------|-----------|-----------|
| `autoMemoryEnabled` | `true` | Auto-Memory — Claude speichert Session-Erkenntnisse automatisch |
| `env.CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` | `"1"` | Agent-Teams aktiviert — sonst immer Solo |

**Wenn Datei fehlt oder Felder fehlen/falsch → anlegen/ergaenzen:**

```bash
mkdir -p ~/.claude
```

Schreibe oder merge in `~/.claude/settings.json`:

```json
{
  "autoMemoryEnabled": true,
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  },
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Bash(node *)",
      "Bash(npm *)"
    ],
    "deny": [
      "Bash(rm -rf /)"
    ]
  }
}
```

**Hinweis Merge:** Wenn die Datei bereits Eintraege hat, nur die fehlenden Felder ergaenzen —
bestehende Permissions und Hooks nicht ueberschreiben.

**Verify nach Anlegen:**
```bash
node -e "const s=JSON.parse(require('fs').readFileSync(require('os').homedir()+'/.claude/settings.json','utf8')); console.log('autoMemory:', s.autoMemoryEnabled, '| AgentTeams:', s.env?.CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS)"
```
Erwartet: `autoMemory: true | AgentTeams: 1`

---

### 0a.2 — ~/.claude/CLAUDE.md pruefen

```bash
wc -l ~/.claude/CLAUDE.md 2>/dev/null || echo "NICHT VORHANDEN"
```

**Pruefe ob folgende Abschnitte vorhanden sind:**

| Abschnitt | Pflicht | Warum |
|-----------|---------|-------|
| Modell-Routing (Opus/Sonnet/Haiku) | PFLICHT | Ohne Routing nutzt Claude immer dasselbe Modell — kostet 3-5x mehr |
| Agent-Strategie (wann Team, wann Sub-Agent) | PFLICHT | Ohne Direktive arbeitet Claude immer solo |
| Secrets-Policy | PFLICHT | Verhindert dass Claude .env-Dateien liest/ausgibt |
| Sprache + Kommunikationsstil | Empfohlen | Einheitliches Verhalten ueber alle Projekte |

**Wenn Datei fehlt oder Abschnitte fehlen → anlegen/ergaenzen:**

Schreibe fehlende Abschnitte in `~/.claude/CLAUDE.md` (bestehende behalten):

```markdown
# Globale Claude Code Regeln
# Gilt fuer ALLE Projekte auf dieser Maschine.
# Unter 200 Zeilen halten — jede Zeile kostet bei jeder Session Tokens.

## Modell-Routing (PFLICHT)

| Modell | Wann verwenden |
|--------|---------------|
| **Opus** | Architektur-Entscheidungen, komplexe Multi-Step-Aufgaben (>5 Tasks), schwierige Bugs mit unklarer Ursache |
| **Sonnet** | Standard-Implementierung, Code-Reviews, Ideation, normale Stories |
| **Haiku** | Dateisuche, Recherche, Batch-Jobs, repetitive Checks, Sub-Agent-Aufgaben |

## Agent-Strategie (PFLICHT)

| Pattern | Wann |
|---------|------|
| **Solo** | Einzelne, klar abgegrenzte Aufgabe, <5 Dateien |
| **Sub-Agent** | Exploration, Recherche, grosse Text-Mengen analysieren |
| **Parallel-Sub-Agents** | Mehrere unabhaengige Recherchen gleichzeitig |
| **Agent-Team** | >3 unabhaengige Komponenten die zusammenspielen muessen, Architektur-Vergleiche, Cross-Layer-Aenderungen |

Agent-Team nur wenn Agents wirklich auf Zwischenergebnisse der anderen warten muessen.
Im Zweifel: Sub-Agent statt Team (3-5x guenstiger).

## Secrets-Policy (PFLICHT)

// NIEMALS Secrets, API-Keys, Passwoerter oder Tokens in Code-Dateien schreiben
// NIEMALS .env-Dateien lesen, anzeigen oder in Outputs einfuegen
// Secrets liegen ausschliesslich in .env — niemals hardcoden oder committen
// .env ist in .claudeignore und .gitignore eingetragen — nie davon abweichen

## Arbeitsweise

// Vor Code-Aenderungen immer Rueckfrage beim Operator — nie eigenmaechtig refactoren
// Antworten kurz und direkt — keine Zusammenfassungen am Ende
// Bash-Outputs bei Bedarf mit | head -100 begrenzen
```

**Verify nach Anlegen:**
```bash
wc -l ~/.claude/CLAUDE.md
```
Erwartet: unter 200 Zeilen.

**Dem Operator mitteilen:**
```
~/.claude/CLAUDE.md wurde angelegt/ergaenzt. Bitte pruefe ob die Sprach-Praeferenz
(Deutsch/Englisch) korrekt eingetragen ist — und ergaenze deinen persoenlichen
Arbeitsstil wenn gewuenscht. Datei unter 200 Zeilen halten.
```

Warte auf Bestaetigung "Global Settings OK", dann weiter mit Phase 0.

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
f) Webflow (Visual Frontend via Webflow MCP — kein lokales Build-System)
```

**Warte auf Antwort.** Die Antwort bestimmt welche Tooling-Dateien Bootstrap anlegt:

| Stack | Linter | Formatter | Config-Dateien |
|-------|--------|-----------|----------------|
| a) Node.js/JS | **Biome** (empfohlen) oder ESLint | **Biome** (inkl.) | `biome.json`, `sonar-project.properties` |
| b) Frontend | ESLint + Prettier | Prettier | `eslint.config.mjs`, `.prettierrc`, `sonar-project.properties` |
| c) Full-Stack | ESLint + Prettier | Prettier | `eslint.config.mjs`, `.prettierrc`, `sonar-project.properties` |
| d) Python | Ruff / Flake8 | Black | `pyproject.toml`, `sonar-project.properties` |
| e) Anderes | Gemeinsam entscheiden | — | Je nach Sprache |
| f) Webflow | Biome (nur fuer Custom JS) | Biome (nur fuer Custom JS) | `biome.json` (optional), `.webflow/` |

> **Hinweis Node.js:** Biome ersetzt ESLint + Prettier in einem Tool (Rust-basiert, 10-100x schneller,
> kein `node_modules` noetig wenn als Binary installiert). Fuer bestehende Projekte mit ESLint:
> Migration ist optional — ESLint bleibt als Alternative gueltig.

> **Hinweis Webflow:** Primaerer Editor ist Claude Code + Webflow MCP (kein lokales Build-System).
> Git-Repo enthaelt nur Governance-Dateien + Custom JS/CSS Snippets. Webflow Cloud ist SSoT fuer
> alles Visuelle. Wenn f) gewaehlt: Schritt 0.2 enthaelt zusaetzliche Webflow-spezifische Fragen.

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
   (Setup-Anleitung folgt in Phase 3 — references/telegram-setup.md)
10. Perplexity / OpenRouter API Key fuer Deep Research?
11. Miro Board URL fuer /visualize?
12. Automation Daemon einrichten? (Ja/Nein, default: Nein)
13. Grafana Cloud fuer Monitoring? (Ja/Nein)
    (URL + API Key werden in Phase 3 abgefragt — references/grafana-monitoring.md)

SKILLS:
13. Welche Skills installieren?
    a) Minimum (ideation, implement, backlog, wrap-up) — empfohlen fuer Start
    b) Standard (+ architecture-review, sprint-review, research, breakfix*)
    c) Voll (alle: + cloud-system-engineer, excalidraw-diagram, visualize, skill-creator, integration-test*, status*)
    d) Manuell auswaehlen
    e) Optional-Stack angeben (notebooklm, grafana, supabase, vercel)

    * = wird vom Bootstrap individuell generiert (Fragen werden gestellt)
    wrap-up = immer empfohlen — Session-Abschluss + Auto-Memory

DOMAIN:
14. Welche Architektur-Dimensionen sind relevant?
    Standard: Reliability, Data Integrity, Security, Performance, Observability, Maintainability
    Optional: Cost Efficiency, Signal Quality, oder eigene?
```

**Wenn Stack = f) Webflow: zusaetzlichen Block stellen (direkt nach den Pflicht-Fragen):**

```
WEBFLOW-SPEZIFISCH:
15. Webflow Site ID?
    (Webflow Designer → Settings → General → unten: "Site ID")
16. Welche Bereiche sind relevant?
    a) Nur statische Seiten / Landing Pages
    b) CMS (Blog, Produkte, dynamische Inhalte)
    c) E-Commerce
    d) Member Areas / Auth (z.B. Memberstack, Outseta)
    e) Custom Code (eigene JS-Logik via Script Injection)
    f) Mehrere davon → welche?
17. Gibt es Custom JS/CSS das lokal entwickelt werden soll?
    (Wenn ja: Biome + lokales Repo sinnvoll. Wenn nein: nur Governance-Dateien)
18. Webflow Hosting oder externe Domain?
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

| Datei | Template-Sektion | Hinweis |
|-------|-----------------|---------|
| `lib/config.js` | config.js | |
| `CLAUDE.md` | CLAUDE.md | Projekt-Regeln — unter 200 Zeilen halten |
| `CLAUDE.local.md` | — | Persoenliche Overrides, in .gitignore (1.4c) |
| `.claudeignore` | .claudeignore | Pflicht — Kontextschutz (1.4b) |
| `ARCHITECTURE_DESIGN.md` | architecture-design-template.md | |
| `SYSTEM_ARCHITECTURE.md` | SYSTEM_ARCHITECTURE.md | |
| `COMPONENT_INVENTORY.md` | COMPONENT_INVENTORY.md | |
| `.env.example` | .env.example | Mit Format-Erklaerungen |
| `CHANGELOG.md` | CHANGELOG.md | |
| `API_INVENTORY.md` | API_INVENTORY.md | |
| `INDEX.md` | INDEX.md | |
| `PROCESS_CATALOG.md` | PROCESS_CATALOG.md | |
| `specs/TEMPLATE.md` | specs-template | |
| `sonar-project.properties` | sonar-project.properties | |
| `.claude/rules/agent-patterns.md` | agent-patterns.md | Lazy Loading — nur bei Bedarf geladen |

**Stack-abhaengige Tooling-Dateien (basierend auf {{STACK}} aus Phase 0.1):**

| Stack | Anlegen |
|-------|---------|
| a) Node.js/JS | `biome.json` (Template: biome.json) — empfohlen. Alternativ: `eslint.config.mjs` |
| b) Frontend | `eslint.config.mjs` + `.prettierrc` (Template: .prettierrc) |
| c) Full-Stack | `eslint.config.mjs` + `.prettierrc` (Template: .prettierrc) |
| d) Python | `pyproject.toml` (Template: pyproject.toml) — kein ESLint |
| e) Anderes | Gemeinsam mit Operator entscheiden |
| f) Webflow | `.webflow/config.json` (Webflow Site Config) + `biome.json` wenn Custom JS (Frage 17) |

**Wenn f) Webflow: zusaetzlich anlegen:**
- `.webflow/config.json` — Webflow Site ID + Bereich-Config (aus Antworten 15-18)
- `custom-code/` — Verzeichnis fuer Custom JS/CSS Snippets (nur wenn Frage 17 = Ja)
- `.gitignore` anpassen: Webflow-Export-Ordner ausschliessen wenn relevant
- `WEBFLOW_WORKFLOW.md` — Kurz-Doku des MCP-Workflows fuer dieses Projekt

**Webflow Dev-Chain dem Operator erklaeren (nur bei f)):**
```
Workflow fuer dieses Projekt:
  Claude Code + Webflow MCP → Webflow Cloud (SSoT fuer visuellen Content)
  Git-Repo → enthaelt: Governance + Custom JS/CSS + Webflow-Config
  Kein lokales Build-System, kein npm fuer Webflow-Content

  Fuer Custom JS (wenn vorhanden):
    Mac lokal → VS Code + Biome → git commit → GitHub
    Claude liest JS und injiziert via data_scripts_tool in Webflow
```

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
- `journal/LEARNINGS.md` — **Learning-Loop Baseline** (aus Metrik-Angaben Phase 0):

```markdown
# {{PROJECT_NAME}} — Learning Log

> Outcome-Checks nach Issue-Close. Schließt den Feedback-Loop zwischen Implementierung und Effekt.

## Metrik-Definition

| Feld | Wert |
|------|------|
| **Primäre Metrik** | {{METRIC_PRIMARY}} |
| **Baseline (Setup-Tag)** | {{METRIC_BASELINE}} |
| **Ziel** | {{METRIC_TARGET}} |
| **Gemessen am** | {{TODAY}} |

---

## Outcome-Checks

<!-- Nach jedem Issue-Close: Eintrag hier wenn Check-Datum erreicht -->

| Datum | Issue | Was wurde erwartet | Was tatsächlich eingetreten | Δ Metrik |
|-------|-------|-------------------|--------------------------|---------|
| _(leer — wird gefüllt)_ | | | | |

---

## Strategische Learnings

<!-- Was hat generell funktioniert / nicht funktioniert — aus mehreren Issues -->
```

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

**Hooks in `.claude/settings.json` registrieren** (vollstaendige settings.json — ersetzt leeres Skelett):

```json
{
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Bash(node *)",
      "Bash(npm *)",
      "mcp__claude_ai_Linear__save_issue",
      "mcp__claude_ai_Linear__list_issues",
      "mcp__claude_ai_Linear__save_comment",
      "mcp__claude_ai_Linear__get_issue",
      "mcp__claude_ai_Linear__get_team",
      "mcp__claude_ai_Linear__list_projects"
    ]
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          { "type": "command", "command": "bash .claude/hooks/spec-gate.sh" },
          { "type": "command", "command": "bash .claude/hooks/doc-version-sync.sh" },
          { "type": "command", "command": "bash .claude/hooks/guard.sh" }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          { "type": "command", "command": "bash .claude/hooks/format.sh" }
        ]
      }
    ],
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          { "type": "command", "command": "echo 'Session beendet — /wrap-up nicht vergessen!'" }
        ]
      }
    ]
  }
}
```

**guard.sh anlegen** — verhindert dass Claude .env-Dateien liest oder loescht:

```bash
cat > {PROJECT_PATH}/.claude/hooks/guard.sh << 'EOF'
#!/bin/bash
# guard.sh — Schutzt sensitive Dateien vor Claude-Zugriff
INPUT="$TOOL_INPUT"
BLOCKED_PATTERNS=(".env" "*.pem" "*.key" "id_rsa" "id_ed25519")
for pattern in "${BLOCKED_PATTERNS[@]}"; do
  if echo "$INPUT" | grep -q "$pattern"; then
    echo "BLOCKED: Zugriff auf sensitive Datei ($pattern) verweigert." >&2
    exit 1
  fi
done
exit 0
EOF
chmod +x {PROJECT_PATH}/.claude/hooks/guard.sh
```

**format.sh anlegen** — Auto-Format nach Edit/Write (stack-abhaengig):

```bash
cat > {PROJECT_PATH}/.claude/hooks/format.sh << 'EOF'
#!/bin/bash
# format.sh — Auto-Formatter nach Edit/Write
# Anpassen je nach Stack (Biome, Prettier, Black)
FILEPATH="$TOOL_RESULT_FILE"
if [ -z "$FILEPATH" ]; then exit 0; fi
# Node.js/JS: Biome
if command -v biome &>/dev/null; then
  biome format --write "$FILEPATH" 2>/dev/null
# Python: Black
elif command -v black &>/dev/null && [[ "$FILEPATH" == *.py ]]; then
  black "$FILEPATH" 2>/dev/null
fi
exit 0
EOF
chmod +x {PROJECT_PATH}/.claude/hooks/format.sh
```

Dem Operator mitteilen: "Hooks sind angelegt und in settings.json aktiv."

### 1.4b .claudeignore anlegen

**Pflicht — ohne .claudeignore liest Claude node_modules (100'000+ Dateien = 30-40% Kontextfenster).**

```bash
cat > {PROJECT_PATH}/.claudeignore << 'EOF'
# Build-Artefakte
node_modules/
dist/
build/
.next/
__pycache__/
*.pyc
.venv/

# Secrets (Claude darf diese nie lesen)
.env
.env.*
*.pem
*.key

# Logs + Daten
*.log
*.jsonl
data/*.db
data/*.sqlite

# IDE + System
.DS_Store
.idea/
.vscode/settings.json
*.lock
coverage/

# Grosse generierte Dateien
package-lock.json
yarn.lock
EOF
```

**Verify:**
```bash
cat {PROJECT_PATH}/.claudeignore | grep ".env"
# Erwartet: .env und .env.* sind eingetragen
```

### 1.4c CLAUDE.local.md anlegen

Persoenliche Overrides die nicht ins Repo gehoeren (in `.gitignore` eintragen):

```bash
cat > {PROJECT_PATH}/CLAUDE.local.md << 'EOF'
# Meine lokalen Einstellungen — wird NICHT ins Git committet

## Meine Rolle
// [Bitte ausfuellen: z.B. "Ich bin der alleinige Entwickler"]

## Lokale Umgebung
// [Bitte ausfuellen: z.B. "API-Server laeuft auf http://localhost:3000"]

## Persoenlicher Stil
// [Optional: z.B. "Erklaere Backend-Konzepte ausfuehrlicher"]
EOF
```

Dem Operator mitteilen:
```
CLAUDE.local.md wurde angelegt. Bitte fuell sie mit deinen persoenlichen
Einstellungen — sie wird nie ins Git committet und gilt nur fuer dich.
```

In `.gitignore` eintragen:
```bash
echo "CLAUDE.local.md" >> {PROJECT_PATH}/.gitignore
```

### 1.4d Agent-Patterns als .claude/rules/ auslagern

```bash
mkdir -p {PROJECT_PATH}/.claude/rules
cp {bootstrap-path}/references/agent-patterns.md {PROJECT_PATH}/.claude/rules/agent-patterns.md
```

Eintrag in CLAUDE.md ergaenzen (nach Agent-Routing Abschnitt):
```markdown
**Konkrete Team-Patterns:** `.claude/rules/agent-patterns.md` — wird nur bei Bedarf geladen.
```

**Warum auslagern:** Die Patterns belasten das Kontextfenster nicht dauerhaft — Claude
zieht sie nur wenn Agent-Koordination ansteht (Lazy Loading).

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
git add journal/LEARNINGS.md
git commit -m "v{VERSION_START} — Initial Governance Setup"
git push -u origin main
```

Phase 1 Checkpoint: Kurze Zusammenfassung ausgeben was angelegt wurde.

---

## Phase 2: MCP-Setup + Skills installieren

### 2.0 MCP-Server konfigurieren (vor Skills-Installation)

Lies `references/mcp-setup.md` komplett.

**Schritt 1:** Frage dem Operator welche MCP-Server benoetigt werden (Auswahlliste in mcp-setup.md §1).
**Schritt 2:** `.claude/settings.json` anlegen mit Minimal-Template (mcp-setup.md §2).
**Schritt 3:** Gewaehlte MCP-Server einrichten (mcp-setup.md §3 — je Dienst).
**Schritt 4:** API-Keys verifizieren (mcp-setup.md §4 — Verify-Calls ausfuehren).
**Schritt 5:** MCP-Server-Start pruefen (mcp-setup.md §5).

**Warte auf Bestaetigung dass MCP funktioniert** bevor Skills installiert werden —
Skills setzen funktionierende MCP-Tools voraus.

---

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

# Gewaehlte Skills holen (Minimum: ideation implement backlog wrap-up)
git sparse-checkout set ideation implement backlog wrap-up architecture-review sprint-review research excalidraw-diagram skill-creator visualize notebooklm

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

Minimum (a): ideation, implement, backlog, wrap-up
Standard (b): + architecture-review, sprint-review, research, breakfix
Voll (c): + cloud-system-engineer, excalidraw-diagram, visualize, skill-creator, integration-test, status
Optional: notebooklm, grafana (wenn Grafana Stack), supabase (wenn Supabase DB), vercel (wenn Vercel Deployment)

**wrap-up** ist in allen Paketen enthalten — es gehört immer dazu:
Lies `references/wrap-up-template.md` und verlinke/kopiere den Skill.
Wenn wrap-up kopiert wurde: `{{MEMORY_PATH}}` mit dem tatsächlichen Memory-Pfad befüllen.
(Standard: `{project-slug}` — abgeleitet aus PROJECT_PATH)

Danach domain-spezifische Anpassung (wenn Skills kopiert wurden):
- `ideation/references/story-template-feature.md` — Domain-Sektionen anpassen
- `ideation/references/architecture-dimensions.md` — Dimensionen aus Antwort 14
- `implement/references/change-checklist.md` — Spezial-Checklisten anpassen

### Schritt 2.2: Projekt-spezifische Skills generieren

Diese Skills werden NICHT verlinkt/kopiert sondern individuell für das Projekt generiert.
Lies das jeweilige Template, stelle die Fragen, schreibe das generierte SKILL.md.

**Für jeden gewählten projekt-spezifischen Skill in dieser Reihenfolge:**

#### /breakfix (wenn gewählt — Standard-Paket oder manuell)

Lies `references/breakfix-template.md` Sektion "Bootstrap: Fragen an den Operator".
Stelle die 4 Fragen. Warte auf Antworten.
Dann: generiere `{PROJECT_PATH}/.claude/skills/breakfix/SKILL.md` aus dem Skeleton
mit den Platzhaltern befüllt.

#### /integration-test (wenn gewählt — Voll-Paket oder manuell)

Lies `references/integration-test-template.md` Sektion "Bootstrap: Fragen an den Operator".
Stelle die 3 Fragen. Warte auf Antworten.
Dann: generiere `{PROJECT_PATH}/.claude/skills/integration-test/SKILL.md`.
Wenn Post-Implement = Ja: Ergänze am Ende von `{PROJECT_PATH}/.claude/skills/implement/SKILL.md`:
```
## Nach /implement immer ausführen
/integration-test starten und Tier-1 Ergebnis prüfen bevor Issue geschlossen wird.
```

#### /status (wenn gewählt — Voll-Paket oder manuell)

Lies `references/status-template.md` Sektion "Bootstrap: Fragen an den Operator".
Stelle die 4 Fragen. Warte auf Antworten.
Dann: generiere `{PROJECT_PATH}/.claude/skills/status/SKILL.md`.

#### /wrap-up (in allen Paketen)

Lies `references/wrap-up-template.md`.
Der Skill braucht keine Fragen — einfach verlinken oder kopieren.
Wenn kopiert: `{{MEMORY_PATH}}` in SKILL.md ersetzen mit:
```
{project-slug}  (abgeleitet aus PROJECT_PATH letztes Segment in Kleinbuchstaben)
Beispiel: /my/projects/analytics → analytics
```
Eintrag in CLAUDE.md §3 ergänzen (Bootstrap erledigt das automatisch):
```
> **PFLICHT bei Session-Ende:** Bei "Exit", "Tschüss", "Ende" → `/wrap-up` IMMER zuerst.
```

**Hinweis /calibrate:** calibrate ist nicht im Bootstrap enthalten — zu domain-spezifisch
(Scoring/Gewichtungs-Kalibrierung für ML-Systeme). Bei Bedarf: `/skill-creator` verwenden.

Phase 2 Checkpoint: Liste der installierten + generierten Skills ausgeben.

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

### 3.4 Cron-Job — Umgebung bestimmen

Stelle dem Operator diese Frage:

```
Laeuft das System in Docker oder direkt auf dem Host?

a) Docker / supercronic — Crontab-Datei im Container
b) Host-Systemd — systemd.timer
c) Standard-Cron (crontab -e) — lokale Entwicklung oder einfache VPS
```

---

#### Option a) Docker + supercronic (empfohlen fuer VPS/Container)

In Docker-Umgebungen ist Standard-`cron` oft nicht zuverlaessig.
`supercronic` ist ein Drop-in-Ersatz der direkt als Prozess laeuft.

**Crontab-Datei anlegen** (`{PROJECT_PATH}/crontab`):

```
# WICHTIG: Keine Variablen ($VAR) — nur absolute Pfade
*/15 * * * * node /data/.openclaw/workspace/{slug}/agents/self-healing.js >> /data/.openclaw/workspace/{slug}/logs/self-healing.log 2>&1
0 6 * * * node /data/.openclaw/workspace/{slug}/lib/doc-sync.js >> /data/.openclaw/workspace/{slug}/logs/doc-sync.log 2>&1
```

**In Dockerfile/docker-compose.yml eintragen:**

```yaml
# docker-compose.yml
services:
  myproject:
    command: supercronic /data/.openclaw/workspace/{slug}/crontab
    # oder als Hintergrundprozess neben dem Haupt-Prozess:
    # entrypoint: ["sh", "-c", "supercronic /path/to/crontab & node main.js"]
```

**supercronic installieren (im Dockerfile):**

```dockerfile
RUN curl -fsSL "https://github.com/aptible/supercronic/releases/latest/download/supercronic-linux-amd64" \
    -o /usr/local/bin/supercronic && chmod +x /usr/local/bin/supercronic
```

---

#### Option b) systemd Timer (empfohlen fuer Host-VPS)

```bash
# /etc/systemd/system/self-healing-{slug}.service
cat > /etc/systemd/system/self-healing-{slug}.service << 'EOF'
[Unit]
Description=Self-Healing {PROJECT_NAME}
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/bin/node {PROJECT_PATH}/agents/self-healing.js
WorkingDirectory={PROJECT_PATH}
StandardOutput=append:{PROJECT_PATH}/logs/self-healing.log
StandardError=append:{PROJECT_PATH}/logs/self-healing.log
EOF

# /etc/systemd/system/self-healing-{slug}.timer
cat > /etc/systemd/system/self-healing-{slug}.timer << 'EOF'
[Unit]
Description=Self-Healing Timer {PROJECT_NAME}

[Timer]
OnBootSec=2min
OnUnitActiveSec=15min
Persistent=true

[Install]
WantedBy=timers.target
EOF

systemctl daemon-reload
systemctl enable self-healing-{slug}.timer
systemctl start self-healing-{slug}.timer

# Pruefen:
systemctl status self-healing-{slug}.timer
```

**Vorteil systemd Timer:** Startet automatisch nach Server-Reboot (B-06 geloest).

---

#### Option c) Standard-Cron (lokale Entwicklung)

```bash
crontab -e
```

Eintrag:
```
*/15 * * * * cd {PROJECT_PATH} && node agents/self-healing.js >> {PROJECT_PATH}/logs/self-healing.log 2>&1
```

**Achtung:** Auf Servern nach Reboot pruefen ob cron-Daemon laeuft: `systemctl status cron`.

---

### 3.5 Log-Rotation einrichten

Ohne Log-Rotation fuellen Self-Healing und Doc-Sync-Logs den Speicher.

**logrotate konfigurieren:**

```bash
cat > /etc/logrotate.d/{slug} << 'EOF'
{PROJECT_PATH}/logs/*.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
    create 0644 root root
}
EOF

# Sofort testen (Dry-Run):
logrotate --debug /etc/logrotate.d/{slug}
```

**Ergebnis:** Logs werden taeglich rotiert, 7 Tage aufbewahrt, aeltere komprimiert.

**Falls kein logrotate verfuegbar** (Docker-Container):
In der Crontab einen Aufraeum-Job ergaenzen:

```
# Logs aelter als 7 Tage loeschen
0 3 * * * find {PROJECT_PATH}/logs -name "*.log" -mtime +7 -delete
```

---

### 3.6 Prozess-Persistenz nach Reboot pruefen

Alle Daemons die auf dem Host laufen (Self-Healing, obsidian-headless, linear-webhook)
muessen nach einem Server-Reboot automatisch wieder starten.

**Checkliste:**

```bash
# Fuer jeden systemd-Service:
systemctl is-enabled obsidian-sync.service   # Erwartet: "enabled"
systemctl is-enabled self-healing-{slug}.timer # Erwartet: "enabled"

# Reboot-Test (nur wenn moeglich):
# sudo reboot
# Nach Neustart: ps aux | grep "obsidian-headless\|self-healing"
```

**Fuer nohup-Prozesse:** @reboot-Cron-Eintrag:

```bash
crontab -e
# Eintrag:
@reboot cd {PROJECT_PATH} && nohup node agents/linear-webhook.js >> logs/webhook.log 2>&1 &
```

Phase 3 Checkpoint: Self-Healing Test-Output zeigen + Cron/Timer-Status bestätigen.

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

### 5.5 Abschluss-Ausgabe

**Schritt 1:** Abschluss-Tabelle ausgeben:

| Phase | Was | Status |
|-------|-----|--------|
| Phase 0 | Info-Gathering + Stack-Wahl | done |
| Phase 1 | Grundstruktur (Dateien, Git, Linear-Labels) | done |
| Phase 2 | Skills installiert + angepasst | done |
| Phase 3 | Self-Healing + Doc-Sync (aus eingebetteten Templates) | done |
| Phase 4 | Automation Daemon | done / skipped |
| Phase 5 | Global Registry aktualisiert | done |

**Schritt 2:** VS Code Extensions ausgeben — passend zum gewaelten Stack.
Lies die Ausgabe-Texte aus `references/file-templates.md` Sektion "VS Code Extensions je Stack":
- Basis-Extensions IMMER ausgeben (alle Stacks ausser Python)
- Stack-spezifische Extensions zusaetzlich ausgeben
- Bei Python: eigene Liste (ersetzt Basis)

**Schritt 3:** Smoke-Test ausfuehren — Go/No-Go vor dem ersten /ideation:

```
Bootstrap Smoke-Test — bitte jeden Punkt pruefen:

GOVERNANCE:
[ ] spec-gate.sh blockiert: git commit ohne Spec-Datei schlaegt fehl
    Test: touch test.js && git add test.js && git commit -m "PROJ-99 test"
    Erwartet: Fehler "Kein Spec-File fuer PROJ-99 gefunden"

[ ] Linear API erreichbar: /ideation kann Issues anlegen
    Test: In Claude Code — "Zeige mir die offenen Issues in Linear"
    Erwartet: Issue-Liste (keine Auth-Fehler)

[ ] MCP-Tools verfuegbar: mcp__claude_ai_Linear__* Tools aktiv
    Test: In Claude Code — "Welche Linear-Teams gibt es?"
    Erwartet: Team-Liste (keine "Tool not available"-Fehler)

SELF-HEALING:
[ ] Self-Healing laeuft durch ohne Fehler
    Test: node {PROJECT_PATH}/agents/self-healing.js
    Erwartet: "All X docs at version {VERSION_START}" — kein ERROR

[ ] Cron/Timer aktiv und wird ausgefuehrt
    Test (systemd): systemctl status self-healing-{slug}.timer
    Test (cron): crontab -l | grep self-healing
    Erwartet: Timer/Cron sichtbar und "enabled"

OBSIDIAN (wenn konfiguriert):
[ ] doc-sync schreibt in Vault
    Test: node -e "require('./lib/doc-sync').syncAllDocs().then(() => console.log('OK'))"
    Erwartet: "OK" — Dateien in {OBSIDIAN_VAULT_PATH} aktualisiert

[ ] obsidian-headless laeuft (VPS-Szenario)
    Test: ps aux | grep obsidian-headless | grep -v grep
    Erwartet: Prozess sichtbar

TELEGRAM (wenn konfiguriert):
[ ] Test-Nachricht wird zugestellt
    Test: siehe references/telegram-setup.md — curl-Befehl ausfuehren
    Erwartet: Nachricht erscheint im Telegram-Chat

Wenn alle relevanten Punkte gruen: Bootstrap erfolgreich abgeschlossen.
Wenn ein Punkt rot: Nicht weitermachen — erst beheben.
```

**Schritt 4:** Naechste Schritte ausgeben:
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
| Datei-Templates + .env.example | `references/file-templates.md` (eingebettet) |
| MCP-Setup Anleitung | `references/mcp-setup.md` (eingebettet) |
| Telegram Bot Setup + Linear-Webhook | `references/telegram-setup.md` (eingebettet) |
| Grafana Monitoring Pattern | `references/grafana-monitoring.md` (eingebettet) |
| Agent-Patterns (4 Team-Patterns) | `references/agent-patterns.md` (eingebettet) |
| Skill-Referenzen (ideation etc.) | Separat installieren oder von GitHub |

Um diesen Skill auf einer neuen Maschine zu verwenden:
1. Kopiere den `bootstrap/` Ordner nach `/root/.claude/skills/bootstrap/`
2. Sage Claude: `/bootstrap`
3. Claude fuehrt den kompletten Setup-Workflow aus — ohne Zugriff auf externe Pfade
