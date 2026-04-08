# Datei-Templates für neues Projekt

Alle Templates mit {{PLATZHALTER}} müssen mit den gesammelten Projekt-Infos befüllt werden.

---

## config.js

```javascript
// lib/config.js — Single Source of Truth
'use strict';

const VERSION = '{{VERSION_START}}';

// Dokumentationsdateien — Self-Healing überwacht Versions-Sync
const DOC_FILES = {
  'CLAUDE.md': {
    path: 'CLAUDE.md',
    versionPattern: /\*\*Version:\*\*\s*([\d.]+)/
  },
  'SYSTEM_ARCHITECTURE.md': {
    path: 'SYSTEM_ARCHITECTURE.md',
    versionPattern: /\*\*Version:\*\*\s*([\d.]+)/
  },
  'COMPONENT_INVENTORY.md': {
    path: 'COMPONENT_INVENTORY.md',
    versionPattern: /\*\*Version:\*\*\s*([\d.]+)/
  },
  'DEVELOPMENT_PROCESS.md': {
    path: 'DEVELOPMENT_PROCESS.md',
    versionPattern: /\*\*Version:\*\*\s*([\d.]+)/
  },
  'GOVERNANCE.md': {
    path: 'GOVERNANCE.md',
    versionPattern: /\*\*Version:\*\*\s*([\d.]+)/
  }
};

// Projekt-spezifische Config (anpassen)
const CONFIG = {
  PROJECT_NAME: '{{PROJECT_NAME}}',
  ISSUE_PREFIX: '{{ISSUE_PREFIX}}',
  GITHUB_REPO: '{{GITHUB_REPO}}',
};

module.exports = { VERSION, DOC_FILES, CONFIG };
```

---

## CLAUDE.md

> **Hinweis Bootstrap:** Diese Datei wird in Phase 1 generiert. Alle `{{PLATZHALTER}}`
> werden mit den Angaben aus Phase 0 befüllt. Diese Vorlage entspricht dem bewährten
> 5-Abschnitt-Muster aus produktiven OpenCLAW-Projekten.

```markdown
# {{PROJECT_NAME}} — Context File

> **Letzte Aktualisierung:** {{TODAY}} | **Version:** {{VERSION_START}}

---

## 1. WER BIST DU?

Du bist **Claude Code** — Entwickler und Orchestrator von **{{PROJECT_NAME}}**.
**Workspace:** `{{PROJECT_PATH}}`
**Besitzer:** {{OWNER_NAME}}

> **Naming:**
> | Name | Was |
> |------|-----|
> | **{{PROJECT_SHORTNAME}}** | Das System ({{TECH_STACK}}) |
> | **Claude Code** | Du — Entwickler/Orchestrator dieser Session |

**Du bist der Lead Agent.** Bei komplexen Aufgaben: Sub-Agents spawnen
(Explore/Plan/Research/Parallel).

---

## 2. DEINE AUFGABE

**Oberste Direktive:** "{{PROJECT_GOAL}}" — {{OWNER_NAME}}

**{{PROJECT_DESC}}**

**Quick-Referenz:**
→ **Konfiguration (SSoT):** `lib/config.js`
→ **System-Architektur:** `SYSTEM_ARCHITECTURE.md`
→ **Prozesse:** `PROCESS_CATALOG.md`
→ **Docs-Index:** `INDEX.md`

---

## 3. SELBST-BRIEFING — LIES DIESE DATEIEN

> **PFLICHT bei Session-Start:** Lies zuerst CLAUDE.md. Dann situativ weitere Docs.
> **PFLICHT bei Session-Ende:** Bei "Exit", "Tschüss", "Ende", "fertig" → `/wrap-up` IMMER zuerst.

| Thema | Datei |
|-------|-------|
| **System-Architektur** | `SYSTEM_ARCHITECTURE.md` |
| **Komponenten** | `COMPONENT_INVENTORY.md` |
| **Governance** | `GOVERNANCE.md` |
| **Konfiguration** | `lib/config.js` |
| **Prozess-Katalog** | `PROCESS_CATALOG.md` |
| **Docs-Index** | `INDEX.md` |

---

## 4. KERN-REGELN (gelten IMMER)

**NIEMALS einen Plan umsetzen ohne {{ISSUE_PREFIX}}-Issue.**
**NIEMALS Code ändern ohne Spec-File** (`specs/{{ISSUE_PREFIX}}-XXX.md`). Hook: `.claude/hooks/spec-gate.sh`
**NIEMALS config.js VERSION erhöhen ohne alle DOC_FILES zu bumpen.** Hook: `.claude/hooks/doc-version-sync.sh`
**NIEMALS ein Issue schliessen ohne Change-Log + Git Push.**
**NIEMALS Code ändern ohne vorherige Rückfrage beim Operator.**
**NIEMALS neue Skills in `/root/.claude/skills/` anlegen** — alle Skills nach `.claude/skills/`
**NIEMALS ein Issue ohne Labels anlegen.**
**NIEMALS einen Sub-Task direkt von Backlog → Done** — IMMER zuerst "In Progress".
**NIEMALS eine neue API-Integration ohne `API_INVENTORY.md` aktualisieren.**
**Jedes neue File MUSS sofort in `INDEX.md` eingetragen werden** — vor dem git commit.
**JEDE Story vor dem Schliessen:** Integration-Test-Skill prüfen ob neue Komponente abgedeckt ist.
**NACH JEDEM /breakfix:** "Welche CLAUDE.md-Regel hätte diesen Incident verhindert?" → Regel ergänzen.

---

## 5. ENTWICKLUNG & GOVERNANCE

### {{ISSUE_TRACKER}} + Git
- **Team:** {{LINEAR_TEAM}} | **GitHub:** `{{GITHUB_REPO}}` (main)
- **Issue-Prefix:** {{ISSUE_PREFIX}}

### Änderungs-Checkliste (PFLICHT)
0. Spec-File: `specs/{{ISSUE_PREFIX}}-XXX.md` aus `specs/TEMPLATE.md` → Issue verlinken
1. Dokumentation aktualisieren (alle Doku-Files auf aktuelle VERSION)
2. Bei neuen APIs: `API_INVENTORY.md` aktualisieren
3. Git Commit + Push (pro Task: `git commit -m "T{N}: {{ISSUE_PREFIX}}-XXX {Titel}"`)
4. Deploy-Health-Gate: Self-Healing Checks grün prüfen
5. Change-Log im Issue-Tracker aktualisieren

### Challenger-Modus (PFLICHT bei neuer Idee)
1. Was haben wir bereits? (`GOVERNANCE.md`, `CLAUDE.md`, `SYSTEM_ARCHITECTURE.md`)
2. Offene Story? (Issue-Tracker, `journal/STRATEGY_LOG.md` wenn vorhanden)
3. Research-Archiv? (`journal/archive/`, `docs/`)
4. Kritische Bewertung mit expliziten Ja/Nein-Urteilen

### Skills (.claude/skills/)
{{INSTALLED_SKILLS_LIST}}
```

---

## SYSTEM_ARCHITECTURE.md (Minimum)

```markdown
# {{PROJECT_NAME}} — System Architecture

**Version:** {{VERSION_START}} | **Stand:** {{TODAY}}

## Überblick

{{PROJECT_DESC}}

## Komponenten

[Hier Komponenten eintragen wenn sie entstehen]

## Datenfluss

[Hier Datenfluss beschreiben wenn er klar ist]

## Externe Abhängigkeiten

| Service | Zweck | Auth |
|---------|-------|------|
| Linear | Issue Tracking | API Key |
| GitHub | Code Repository | Git + SSH |
| [weitere] | | |
```

---

## COMPONENT_INVENTORY.md (Minimum)

```markdown
# {{PROJECT_NAME}} — Component Inventory

**Version:** {{VERSION_START}} | **Stand:** {{TODAY}}

## Verzeichnisstruktur

```
{{PROJECT_PATH}}
├── lib/
│   ├── config.js          ← VERSION + DOC_FILES + Config
│   └── doc-sync.js        ← Obsidian Vault Sync
├── agents/
│   └── self-healing.js    ← Self-Healing Agent
├── CLAUDE.md              ← AI-Operator Identität + Regeln
├── SYSTEM_ARCHITECTURE.md ← System-Architektur
├── COMPONENT_INVENTORY.md ← Diese Datei
├── DEVELOPMENT_PROCESS.md ← Entwicklungsprozesse
├── GOVERNANCE.md          ← Governance Framework
├── CHANGELOG.md           ← Änderungshistorie
├── .env                   ← API Keys (nicht committen!)
├── .env.example           ← Vorlage ohne echte Keys
└── .claude/
    ├── ISSUE_WRITING_GUIDELINES.md
    └── skills/            ← Installierte Skills
```
```

---

## .env.example

```
# {{PROJECT_NAME}} — Umgebungsvariablen
# NIEMALS echte Keys committen — nur in .env eintragen

# Linear
LINEAR_API_KEY=your_linear_api_key_here
LINEAR_WEBHOOK_SECRET=your_webhook_secret_here

# GitHub
# SSH Key wird empfohlen statt Token

# Optional: Telegram Alerts
# TELEGRAM_BOT_TOKEN=
# TELEGRAM_CHAT_ID=

# Optional: Research
# OPENROUTER_API_KEY=

# Optional: Miro
# MIRO_ACCESS_TOKEN=
```

---

## .gitignore (Minimum)

```
node_modules/
.env
*.log
.DS_Store
```

---

## CHANGELOG.md (Minimum)

```markdown
# {{PROJECT_NAME}} — Changelog

## v{{VERSION_START}} — {{TODAY}}

### Initial Setup
- Governance Framework eingerichtet
- Basis-Dokumentation erstellt
- Skills installiert
```

---

## settings.json (Hooks + Permissions)

> **PFLICHT:** Hooks laufen NICHT automatisch — sie müssen in `settings.json` registriert werden.
> Ohne diesen Eintrag sind `spec-gate.sh` und `doc-version-sync.sh` wirkungslos.

Erstelle `.claude/settings.json` im Projekt-Root:

```json
{
  "permissions": {
    "allow": [
      "Bash(git:*)",
      "Bash(node:*)",
      "Bash(npm:*)",
      "mcp__claude_ai_Linear__save_comment",
      "mcp__claude_ai_Linear__list_issues"
    ]
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          { "type": "command", "command": "bash .claude/hooks/spec-gate.sh" },
          { "type": "command", "command": "bash .claude/hooks/doc-version-sync.sh" }
        ]
      }
    ]
  }
}
```

**Was diese Einträge bewirken:**
| Eintrag | Wirkung |
|---------|---------|
| `Bash(git:*)` | Claude darf git-Kommandos ausführen |
| `Bash(node:*)` | Claude darf Node.js-Skripte ausführen |
| `hooks.PreToolUse` | Hooks werden VOR jedem Bash-Aufruf ausgeführt |
| `spec-gate.sh` | Blockiert `git commit` wenn kein Spec-File existiert |
| `doc-version-sync.sh` | Blockiert `git commit` wenn Doku nicht auf aktueller VERSION |

**Hinweis:** Weitere `permissions.allow`-Einträge nach Bedarf ergänzen (API-Calls, MCP-Tools, etc.).

Nested-Session-Fix in `.env` des Projekts:
```
# Verhindert Claude Code nested session Probleme beim Daemon
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC=1
```

---

## eslint.config.mjs (Code Quality Gate)

Die Haupt-Regeldatei fuer ESLint. Liegt im **Projekt-Root** und wird vom VS Code ESLint Plugin
automatisch erkannt. Wird vom `/implement` Skill als Quality Gate in Schritt 6a ausgefuehrt.
SonarLint liest diese Regeln ebenfalls.

**Wichtig:** ESLint v9+ verwendet das neue "Flat Config" Format (`eslint.config.mjs`).
Das alte Format (`.eslintrc.js`) ist veraltet und wird nicht mehr unterstuetzt.

```javascript
// eslint.config.mjs — ESLint v9 Flat Config
// Liegt im Projekt-Root — VS Code ESLint Plugin erkennt diese Datei automatisch.
// Wird von /implement Schritt 6a ausgefuehrt: npx eslint <geaenderte-dateien>
// SonarQube for IDE (SonarLint) und Error Lens zeigen Findings inline in VS Code.

export default [
  {
    // Globale ignores — als eigenes Top-Level-Objekt ohne "files"-Key
    ignores: [
      "node_modules/**",
      "data/**",
      "signals/**",
      "journal/**",
      "*.log"
    ]
  },
  {
    files: ["**/*.js"],

    languageOptions: {
      ecmaVersion: 2022,
      sourceType: "commonjs",
      globals: {
        // Node.js Globals
        require: "readonly",
        module: "writable",
        exports: "writable",
        __dirname: "readonly",
        __filename: "readonly",
        process: "readonly",
        console: "readonly",
        Buffer: "readonly",
        setTimeout: "readonly",
        setInterval: "readonly",
        clearTimeout: "readonly",
        clearInterval: "readonly",
        Promise: "readonly",
        URL: "readonly",
        URLSearchParams: "readonly"
      }
    },

    rules: {

      // === FEHLER-PRAEVENTION ===
      "no-unused-vars": ["warn", { "argsIgnorePattern": "^_" }],
      "no-undef": "error",
      "no-unreachable": "error",
      "no-duplicate-case": "error",
      "no-dupe-keys": "error",
      "no-self-assign": "error",
      "no-constant-condition": "warn",
      "use-isnan": "error",

      // === REDUNDANZ + CODE-QUALITAET ===
      "no-redeclare": "error",
      "no-shadow": "warn",
      "no-var": "error",
      "prefer-const": "warn",
      "no-else-return": "warn",
      "no-useless-return": "warn",

      // === ASYNC / PROMISES (kritisch fuer alle Event-Loop Systeme) ===
      "no-async-promise-executor": "error",
      "no-await-in-loop": "warn",
      "no-promise-executor-return": "error",
      "require-await": "warn",

      // === SICHERHEIT (Security by Design) ===
      "no-eval": "error",           // Kein eval() — Remote Code Execution Risiko
      "no-new-func": "error",       // Kein new Function() — gleiches Risiko
      "no-implied-eval": "error",   // Kein setTimeout('string') — gleiches Risiko

      // === STIL / LESBARKEIT ===
      "eqeqeq": ["error", "always"],
      "no-console": "off",
      "no-trailing-spaces": "warn",
      "no-multiple-empty-lines": ["warn", { "max": 2 }],
      "max-len": ["warn", { "code": 120, "ignoreComments": true, "ignoreStrings": true }],
      "max-depth": ["warn", { "max": 5 }]
    }
  }
];
```

**Warum diese Regeln?**

| Regel | Schutzziel |
|-------|-----------|
| `no-eval` | Verhindert Code-Injection (OWASP Top 10) |
| `eqeqeq` | Verhindert subtile Typ-Vergleichsfehler |
| `no-unused-vars` | Verhindert toten Code der versteckte Bugs kaschiert |
| `prefer-const` | Verhindert unbeabsichtigte Variable-Mutation |
| `no-async-promise-executor` | Verhindert versteckte Fehler in async Promise-Chains |

**Neues Projekt ohne Bootstrap?** Datei einfach aus einem bestehenden Projekt kopieren —
alle Regeln sind generisch und funktionieren fuer jedes Node.js Projekt ohne Anpassung.

---

## biome.json (Node.js — Linter + Formatter in einem)

Nur anlegen wenn Stack = a) Node.js/JS und Operator Biome gewaehlt hat.
Biome ersetzt ESLint + Prettier in einer einzigen Rust-Binary — kein `node_modules` noetig.
97% Prettier-kompatible Formatierung + ESLint-aequivalente Linting-Regeln.

**Installation (einmalig, als dev-binary):**
```bash
npm install --save-dev --save-exact @biomejs/biome
# oder als globales Binary (kein node_modules im Projekt):
npm install -g @biomejs/biome
```

```json
{
  "$schema": "https://biomejs.dev/schemas/1.9.0/schema.json",
  "organizeImports": {
    "enabled": true
  },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true,
      "security": {
        "noEval": "error"
      },
      "suspicious": {
        "noConsoleLog": "off"
      },
      "correctness": {
        "noUnusedVariables": "warn"
      }
    }
  },
  "formatter": {
    "enabled": true,
    "indentStyle": "space",
    "indentWidth": 2,
    "lineWidth": 120
  },
  "javascript": {
    "formatter": {
      "quoteStyle": "single",
      "trailingCommas": "es5",
      "semicolons": "always"
    }
  },
  "files": {
    "ignore": [
      "node_modules",
      "data",
      "signals",
      "journal",
      "*.min.js"
    ]
  }
}
```

**VS Code Setting ergaenzen** (in `.vscode/settings.json`):
```json
{
  "editor.defaultFormatter": "biomejs.biome",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "quickfix.biome": "explicit"
  },
  "[javascript]": { "editor.defaultFormatter": "biomejs.biome" },
  "[json]": { "editor.defaultFormatter": "biomejs.biome" }
}
```

**Warum Biome statt ESLint fuer Node.js?**

| Kriterium | ESLint | Biome |
|-----------|--------|-------|
| Speed | ~2-5s | ~100ms (Rust) |
| Tools | Linter only | Linter + Formatter |
| Konfiguration | `.eslintrc` + `.prettierrc` | 1 Datei (`biome.json`) |
| npm-Deps | `eslint` + Plugins | 1 Binary (`@biomejs/biome`) |
| Prettier-Kompatibel | via Plugin | 97% nativ |

> **Fuer bestehende Projekte mit ESLint:** Migration ist optional — ESLint bleibt gueltig.
> Biome empfohlen fuer alle **neuen** Node.js-Projekte.

---

## .prettierrc (Formatter — Frontend / Full-Stack)

Nur anlegen wenn Stack = b) Frontend oder c) Full-Stack.
Prettier formatiert automatisch beim Speichern (Format-on-Save in VS Code).

```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 120,
  "bracketSpacing": true,
  "arrowParens": "avoid"
}
```

**VS Code Setting ergaenzen** (in `.vscode/settings.json`):
```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "[javascript]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
  "[typescript]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
  "[html]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
  "[css]": { "editor.defaultFormatter": "esbenp.prettier-vscode" }
}
```

---

## pyproject.toml (Python — Linter + Formatter)

Nur anlegen wenn Stack = d) Python.
Enthaelt Konfiguration fuer Black (Formatter) und Ruff (Linter, moderner Ersatz fuer Flake8).

```toml
[tool.black]
line-length = 120
target-version = ["py311"]
exclude = '''
/(
  \.git
  | \.venv
  | __pycache__
  | node_modules
)/
'''

[tool.ruff]
line-length = 120
target-version = "py311"

select = [
  "E",   # pycodestyle errors
  "W",   # pycodestyle warnings
  "F",   # pyflakes (unused imports, undefined names)
  "S",   # bandit security rules
  "B",   # bugbear (common bugs)
]

ignore = [
  "S101",  # assert erlaubt (Tests)
]

exclude = [
  ".git",
  ".venv",
  "__pycache__",
  "node_modules",
]

[tool.ruff.per-file-ignores]
"tests/**" = ["S"]  # Security-Regeln in Tests lockerer
```

**VS Code Extensions fuer Python:**
- `ms-python.python` — Python Extension (Pflicht)
- `ms-python.black-formatter` — Black Formatter
- `charliermarsh.ruff` — Ruff Linter

**VS Code Setting ergaenzen** (in `.vscode/settings.json`):
```json
{
  "editor.defaultFormatter": "ms-python.black-formatter",
  "editor.formatOnSave": true,
  "[python]": {
    "editor.defaultFormatter": "ms-python.black-formatter",
    "editor.codeActionsOnSave": {
      "source.fixAll.ruff": true
    }
  }
}
```

---

## VS Code Extensions je Stack

Am Ende von Phase 5 (Abschluss) dem Operator die passenden Links ausgeben.
Einfach anklicken → direkt im Browser installieren.

### Basis-Extensions (IMMER ausgeben — fuer alle Stacks)

```
✓ Basis-Extensions (fuer alle Projekte):

→ ESLint
  https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint

→ Error Lens (zeigt Fehler direkt in der Codezeile)
  https://marketplace.visualstudio.com/items?itemName=usernamehw.errorlens

→ SonarLint (tiefergehende Code-Analyse)
  https://marketplace.visualstudio.com/items?itemName=SonarSource.sonarlint-vscode
```

### Stack a) Node.js/JS — zusaetzlich ausgeben

```
✓ Stack-spezifisch (Node.js):

→ Biome (Linter + Formatter in einem — empfohlen fuer neue Projekte)
  https://marketplace.visualstudio.com/items?itemName=biomejs.biome
  [Ersetzt ESLint + Prettier, 97% Prettier-kompatibel, Rust-based = sehr schnell]

→ REST Client (API-Endpunkte direkt aus VS Code testen)
  https://marketplace.visualstudio.com/items?itemName=humao.rest-client

Hinweis: Wenn Biome verwendet wird, kein ESLint-Plugin noetig.
Wenn ESLint bevorzugt wird (bestehendes Projekt): ESLint aus Basis-Extensions reicht.
```

### Stack b) Frontend — zusaetzlich ausgeben

```
✓ Stack-spezifisch (Frontend):

→ Prettier (automatisches Formatieren beim Speichern)
  https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode

→ Auto Rename Tag (HTML-Tags automatisch umbenennen)
  https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag

→ CSS Peek (CSS-Klassen direkt aus HTML anspringen)
  https://marketplace.visualstudio.com/items?itemName=pranaygp.vscode-css-peek
```

### Stack c) Full-Stack — zusaetzlich ausgeben

```
✓ Stack-spezifisch (Full-Stack):

→ Prettier (automatisches Formatieren beim Speichern)
  https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode
```

### Stack f) Webflow — zusaetzlich ausgeben

```
✓ Stack-spezifisch (Webflow + MCP):

→ Webflow hat kein lokales VS Code Plugin — Entwicklung laeuft via Claude Code + Webflow MCP.

  Workflow:
  Claude Code → Webflow MCP Tools → Webflow Cloud (SSoT)
  (kein lokales Build-System, kein npm fuer Webflow-Content)

Wenn Custom JS vorhanden (Frage 17 = Ja):

→ Biome (Linter + Formatter fuer Custom JS)
  https://marketplace.visualstudio.com/items?itemName=biomejs.biome

→ REST Client (Custom JS via Script Injection testen)
  https://marketplace.visualstudio.com/items?itemName=humao.rest-client

Verfuegbare Webflow MCP Tools (bereits registriert):
  - de_page_tool / de_component_tool  → Seiten + Komponenten
  - element_tool / element_builder    → DOM-Elemente
  - style_tool / variable_tool        → CSS + Variablen
  - data_cms_tool                     → CMS Collections
  - data_scripts_tool                 → Custom Code Injection
  - asset_tool                        → Bilder + Assets
  - ask_webflow_ai                    → Webflow-spezifische KI-Hilfe
```

### Stack d) Python — STATT Basis-Extensions ausgeben

```
✓ Extensions fuer Python:

→ Python (Pflicht — Grundlage fuer alles)
  https://marketplace.visualstudio.com/items?itemName=ms-python.python

→ Black Formatter (automatisches Formatieren)
  https://marketplace.visualstudio.com/items?itemName=ms-python.black-formatter

→ Ruff (Linter — schneller Ersatz fuer Flake8)
  https://marketplace.visualstudio.com/items?itemName=charliermarsh.ruff

→ Error Lens (zeigt Fehler direkt in der Codezeile)
  https://marketplace.visualstudio.com/items?itemName=usernamehw.errorlens

→ SonarLint (tiefergehende Code-Analyse)
  https://marketplace.visualstudio.com/items?itemName=SonarSource.sonarlint-vscode

Optional:
→ Pylance (bessere Autovervollstaendigung)
  https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance

→ Jupyter (fuer Data Science / ML Notebooks)
  https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter
```

---

## sonar-project.properties (SonarLint Konfiguration)

Konfiguriert SonarQube for IDE (SonarLint Plugin in VS Code) fuer das Projekt.
SonarLint zeigt tiefergehende Security- und Quality-Issues direkt im Editor.

```properties
# sonar-project.properties
# SonarQube for IDE (SonarLint) liest diese Datei automatisch

sonar.projectKey={{PROJECT_SLUG}}-project
sonar.projectName={{PROJECT_NAME}}
sonar.projectVersion={{VERSION_START}}

# Quellcode-Verzeichnisse
sonar.sources=.
sonar.sourceEncoding=UTF-8

# Ausschlüsse — diese Verzeichnisse nicht analysieren
sonar.exclusions=\
  node_modules/**,\
  journal/**,\
  data/**,\
  signals/**,\
  *.min.js,\
  .claude/**

# JavaScript/Node.js Einstellungen
sonar.javascript.environments=node

# Test-Verzeichnisse (falls vorhanden)
# sonar.tests=tests/
```

---

## API_INVENTORY.md (Minimum)

```markdown
# {{PROJECT_NAME}} — API Inventory

**Version:** {{VERSION_START}} | **Stand:** {{TODAY}}
**Zweck:** Vollstaendiges Inventar aller externen API-Verbindungen.
**Update-Pflicht:** Bei JEDER neuen API-Integration aktualisieren.

---

## Uebersicht

| # | Service | Zweck | Auth-Methode | ENV-Variable | Rate Limit | Tier |
|---|---------|-------|--------------|--------------|------------|------|
| 1 | Linear | Issue Tracking | API Key | `LINEAR_API_KEY` | 1500 req/h | Pflicht |
| 2 | GitHub | Code Repository | SSH / PAT | — | 5000 req/h | Pflicht |
| 3 | [weitere APIs hier eintragen] | | | | | |

---

## Details

### 1. Linear API
- **Endpoint:** `https://api.linear.app/graphql`
- **Auth:** `Authorization: Bearer {LINEAR_API_KEY}`
- **Genutzt in:** `lib/linear-client.js`
- **Webhook:** `POST /webhook/linear` (HMAC-SHA256, `LINEAR_WEBHOOK_SECRET`)

### 2. GitHub
- **Genutzt in:** `git push` via SSH
- **Key:** `~/.ssh/id_rsa` (oder PAT in CI)

[Weitere APIs hier dokumentieren wenn sie hinzukommen]
```

---

## INDEX.md (Minimum)

```markdown
# {{PROJECT_NAME}} — Dokumentations-Index

**Version:** {{VERSION_START}} | **Stand:** {{TODAY}}
**Zweck:** Zentrale Uebersicht aller Dokumentationsdateien — Einstiegspunkt fuer neue Entwickler und KI-Assistenten.

---

## Kern-Dokumente (Pflicht-Lektuere)

| Dokument | Zweck | Zielgruppe |
|----------|-------|-----------|
| `CLAUDE.md` | AI-Operator Identitaet, Regeln, System-Referenz | KI-Assistent |
| `SYSTEM_ARCHITECTURE.md` | Komponenten, Datenfluesse, Abhaengigkeiten | Alle |
| `COMPONENT_INVENTORY.md` | Detaillierte Komponentenliste | Entwickler |
| `DEVELOPMENT_PROCESS.md` | Workflows, Prozesse, Checklisten | Entwickler |
| `GOVERNANCE.md` | Framework-Regeln, ADRs, Lifecycle | Alle |
| `CHANGELOG.md` | Chronologische Aenderungshistorie | Alle |
| `SECURITY.md` | Security-Policies, Threat Model | Entwickler |
| `API_INVENTORY.md` | Alle externen API-Verbindungen | Entwickler |

## Prozess-Dokumente

| Dokument | Zweck |
|----------|-------|
| `PROCESS_CATALOG.md` | WIE das System arbeitet (Prozesse, Ablaeufe) |
| `specs/TEMPLATE.md` | Story Spec Template fuer `/implement` |

## Skills

| Skill | Trigger | Beschreibung |
|-------|---------|-------------|
| `/ideation` | Neue Idee, neues Feature | Story erstellen, Research |
| `/implement` | Story umsetzen | 10-Schritte SDLC-Workflow |
| `/backlog` | Sprint Planning | Backlog, Prioritaeten |
| `/breakfix` | System-Problem | Incident Response |
| [weitere Skills hier ergaenzen] | | |
```

---

## PROCESS_CATALOG.md (Minimum)

```markdown
# {{PROJECT_NAME}} — Process Catalog

**Version:** {{VERSION_START}} | **Stand:** {{TODAY}}
**Zweck:** Beschreibt WIE das System arbeitet — alle Kern-Prozesse, Ablaeufe und Verantwortlichkeiten.

---

## 1. Development Process

**Trigger:** Neues Feature / Bug Fix
**Workflow:**
1. Linear Issue erstellen (per `/ideation` oder manuell)
2. Spec-File anlegen: `specs/{ISSUE-ID}.md`
3. Operator-OK einholen
4. `/implement` ausfuehren
5. Git Push + Linear Close
6. Changelog-Eintrag

**Constraint:** Kein Code ohne Issue. Kein Commit ohne Spec-File.

---

## 2. Incident Response

**Trigger:** System-Fehler, Produktion-Problem
**Workflow:** `/breakfix` Skill — Detect → Diagnose → Fix → Verify → Document → Prevent
**Archiv:** `journal/incidents/`

---

## 3. Release Process

**Trigger:** VERSION-Bump in config.js
**Pflicht:**
- Alle DOC_FILES auf neue VERSION aktualisieren
- CHANGELOG.md ergaenzen
- Git Tag: `git tag v{VERSION}`
- `git push --tags`

---

[Weitere Prozesse hier erganzen wenn das System waechst]
```

---

## specs/TEMPLATE.md

```markdown
# {{ISSUE_PREFIX}}XXX — [Story-Titel]

**Status:** Draft | **Erstellt:** {{TODAY}} | **Letzte Aenderung:** {{TODAY}}
**Geschaetzte Komplexitaet:** XS / S / M / L / XL
**Workflow-Type:** Feature / Bug Fix / Refactor / Infra / Docs

---

## Warum? (Problem / Motivation)

[Was ist das Problem oder die Luecke die diese Story loest?]

## Was? (Solution / Scope)

[Was wird implementiert? Was explizit NICHT?]

## Kontext

[Welche Dateien, Komponenten, oder Systeme sind betroffen?]

## Akzeptanzkriterien

- [ ] [Messbares Kriterium 1]
- [ ] [Messbares Kriterium 2]
- [ ] [Messbares Kriterium 3]

## Tasks

- [ ] T1: [Erster konkreter Schritt]
  - Verify: [Wie pruefe ich ob T1 erledigt ist?]
- [ ] T2: [Zweiter Schritt]
  - Verify: [Pruef-Schritt]

## Abhaengigkeiten

- **Blockiert durch:** [Issues oder externe Abhaengigkeiten]
- **Blockiert:** [Welche anderen Issues warten auf diese Story?]

## Sicherheits-Check

- [ ] Keine Credentials im Code
- [ ] Input-Validierung an Systemgrenzen
- [ ] [Domainspezifische Checks ergaenzen]

---

*Spec erstellt vor Code. Operator-OK vor Umsetzung.*
```
