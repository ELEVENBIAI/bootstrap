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
