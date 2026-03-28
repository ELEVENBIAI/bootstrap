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

## CLAUDE.md (Minimum)

```markdown
# {{PROJECT_NAME}} — AI System Reference

**Version:** {{VERSION_START}} | **Stand:** {{TODAY}}
**Repository:** {{GITHUB_REPO}}

## Identität

{{PROJECT_DESC}}

## Meine Fähigkeiten

[Hier eintragen was das System kann — nach und nach erweitern]

## Regeln (NIEMALS)

1. **NIEMALS** Code ändern ohne Linear Issue
2. **NIEMALS** Issue schließen ohne Git Push + Changelog
3. **NIEMALS** API Keys im Chat — User trägt direkt in .env ein
4. **NIEMALS** Issue ohne Labels anlegen
5. [Projektspezifische Regeln ergänzen]

## System-Architektur

[Kurze Übersicht der wichtigsten Komponenten — nach und nach ergänzen]

## Config-Werte

Alle Config-Werte kommen aus `lib/config.js`. VERSION ist dort SSoT.

## Handoff-Prozess

Nach Feature-Entwicklung:
1. Code committen + pushen
2. CLAUDE.md updaten
3. Operator informieren: "Feature X fertig"
4. Operator weist AI-Operator an: "Lies CLAUDE.md neu"
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

## settings.json Ergänzungen

In `/root/.claude/settings.json` unter `permissions.allow` ergänzen:
```json
"Bash(git:*)",
"Bash(node:*)",
"Bash(npm:*)"
```

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

Beim Abschluss von Phase 1 dem Operator diese Extensions empfehlen:

### Stack a) Node.js/JS
```
Pflicht:  ESLint (dbaeumer.vscode-eslint)
          Error Lens (usernamehw.errorlens)
          SonarLint (sonarsource.sonarlint-vscode)
Optional: REST Client (humao.rest-client) — fuer API-Tests
```

### Stack b) Frontend
```
Pflicht:  ESLint (dbaeumer.vscode-eslint)
          Prettier (esbenp.prettier-vscode)
          Error Lens (usernamehw.errorlens)
          SonarLint (sonarsource.sonarlint-vscode)
Optional: Auto Rename Tag (formulahendry.auto-rename-tag)
          CSS Peek (pranaygp.vscode-css-peek)
```

### Stack c) Full-Stack
```
Pflicht:  ESLint (dbaeumer.vscode-eslint)
          Prettier (esbenp.prettier-vscode)
          Error Lens (usernamehw.errorlens)
          SonarLint (sonarsource.sonarlint-vscode)
```

### Stack d) Python
```
Pflicht:  Python (ms-python.python)
          Black Formatter (ms-python.black-formatter)
          Ruff (charliermarsh.ruff)
          Error Lens (usernamehw.errorlens)
          SonarLint (sonarsource.sonarlint-vscode)
Optional: Pylance (ms-python.vscode-pylance) — bessere Autovervollstaendigung
          Jupyter (ms-toolsai.jupyter) — falls Data Science
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
