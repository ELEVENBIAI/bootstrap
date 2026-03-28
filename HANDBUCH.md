# Governance für Vibe Coder — Das komplette Handbuch

> **Für wen ist dieses Handbuch?**
> Du bist Vibe Coder — du hast Ideen, du nutzt KI um Code zu bauen, und du willst schnell
> vorankommen. Governance klingt nach Bürokratie. Dieses Handbuch zeigt dir, warum Governance
> dein schnellstes Werkzeug ist — und wie du es in 30 Minuten aufgesetzt hast.

---

## Inhaltsverzeichnis

1. [Das Problem ohne Governance](#1-das-problem-ohne-governance)
2. [Was du bekommst](#2-was-du-bekommst)
3. [Voraussetzungen und Vorbereitung](#3-voraussetzungen-und-vorbereitung)
4. [Installation — Schritt für Schritt](#4-installation--schritt-für-schritt)
5. [Der Bootstrap-Prozess](#5-der-bootstrap-prozess)
6. [Die Skills — wann nutze ich was?](#6-die-skills--wann-nutze-ich-was)
7. [Die Guardrails — dein Sicherheitsnetz](#7-die-guardrails--dein-sicherheitsnetz)
8. [VS Code Setup](#8-vs-code-setup)
9. [Governance für dein Projekt anpassen](#9-governance-für-dein-projekt-anpassen)
10. [Tägliche Nutzung — ein typischer Workflow](#10-tägliche-nutzung--ein-typischer-workflow)
11. [Häufige Fragen](#11-häufige-fragen)

---

## 1. Das Problem ohne Governance

### Was passiert wenn du einfach drauf los baust

Stell dir vor: Du hast eine großartige Idee. Du öffnest Claude, sagst "Bau mir X" und in 10 Minuten läuft Code. Genial.

Drei Wochen später:

- Du weißt nicht mehr, warum du eine Entscheidung so getroffen hast
- Du fragst Claude nach einem Bug — Claude kennt den Kontext nicht mehr
- Du willst eine neue Funktion hinzufügen und zerstörst dabei was anderes
- Du weißt nicht, welche Version deines Projekts "stabil" ist
- Du hast 50 Dateien, 3 halbfertige Features und keinen Plan mehr

Das ist **nicht** das Problem von KI. Das ist das Problem von **fehlendem System**.

### Die versteckte Wahrheit über Vibe Coding

Vibe Coding ist mächtig — aber nur wenn die KI versteht **was du gebaut hast** und **warum**.
Ohne Dokumentation und Struktur gibt jede neue Session bei null an.

**Mit Governance** passiert folgendes:
- Du sagst in einer neuen Session: `/status` — Claude sieht sofort alles
- Du sagst: `/implement ISSUE-42` — Claude weiß genau was zu tun ist
- Du sagst: `/breakfix` — Claude diagnostiziert strukturiert
- Jede Änderung ist nachvollziehbar, jeder Fehler hat einen Audit-Trail

---

## 2. Was du bekommst

### Das OpenCLAW Governance Framework

Ein **vollständiges Betriebssystem für KI-gestützte Softwareentwicklung**:

```
GitHub Repository (vibercoder79/KI-Masterclass-Koerting-)
├── bootstrap/        ← Richtet alles automatisch ein
├── ideation/         ← Von der Idee zur Story
├── implement/        ← Von der Story zum Code
├── backlog/          ← Sprint Planning & Prioritäten
├── breakfix/         ← Wenn etwas kaputt ist
├── architecture-review/  ← Ist mein System gesund?
├── research/         ← Deep Research mit KI
├── sprint-review/    ← Regelmäßige Qualitätskontrolle
├── integration-test/ ← Automatische Tests nach jeder Änderung
└── status/           ← System-Überblick auf Knopfdruck
```

### Was das konkret bedeutet

| Ohne Governance | Mit Governance |
|----------------|----------------|
| Claude vergisst zwischen Sessions | Claude kennt das System immer |
| "Bau mir X" → irgendwas entsteht | `/ideation` → strukturierte Story → `/implement` |
| Bugs tauchen aus dem Nichts auf | Self-Healing Agent überwacht 24/7 |
| Keine Ahnung ob Version stabil | Jede Änderung ist versioniert + dokumentiert |
| Rollback? Welches Rollback? | Git + Changelog = jederzeit zurückrollbar |
| 3 Wochen später: komplettes Chaos | Sprint Review hält alles sauber |

---

## 3. Voraussetzungen und Vorbereitung

### Software die du brauchst

**Pflicht:**

| Software | Wozu | Download |
|----------|------|---------|
| **Claude Code CLI** | Das Herzstück — KI im Terminal | `npm install -g @anthropic-ai/claude-code` |
| **Node.js** (v18+) | Runtime für Claude Code | nodejs.org |
| **Git** | Versionskontrolle | git-scm.com |

**Empfohlen:**

| Software | Wozu |
|----------|------|
| **Visual Studio Code** | Editor mit Claude Code Integration |
| **GitHub Account** | Dein Code-Repository |

### Accounts die du brauchst

**Pflicht:**

1. **Anthropic Account** — für Claude Code
   - Gehe zu: claude.ai
   - Registrieren → Plan wählen (Pro reicht für den Start)
   - API Key unter: console.anthropic.com → API Keys

2. **GitHub Account** — für dein Repository
   - github.com/signup
   - Kostenlos reicht für den Anfang

**Empfohlen:**

3. **Linear Account** — für Issue Tracking (Backlog, Stories)
   - linear.app
   - Kostenlos für kleine Teams
   - Linear API Key: linear.app → Settings → API → Personal API Keys

**Optional aber wertvoll:**

4. **OpenRouter Account** — für günstigere LLM-Calls
   - openrouter.ai
   - Guthaben aufladen (~$10 reichen lange)
   - API Key unter: openrouter.ai/keys

### API Keys — Übersicht

Bevor du mit `/bootstrap` startest, halte diese Keys bereit:

| Key | Pflicht? | Woher | Variable |
|-----|---------|-------|----------|
| Anthropic API Key | JA | console.anthropic.com | `ANTHROPIC_API_KEY` |
| GitHub SSH Key | JA | `ssh-keygen` + GitHub Settings | — |
| Linear API Key | Empfohlen | linear.app → Settings → API | `LINEAR_API_KEY` |
| OpenRouter Key | Optional | openrouter.ai/keys | `OPENROUTER_API_KEY` |
| Telegram Bot Token | Optional | @BotFather auf Telegram | `TELEGRAM_BOT_TOKEN` |

> **Sicherheitsregel:** API Keys kommen NIEMALS in den Code. Sie landen in `.env` (diese Datei ist
> in `.gitignore` — wird nicht auf GitHub hochgeladen).

### SSH für GitHub einrichten

SSH ist die sichere Verbindung zwischen deinem Rechner und GitHub. Einmal einrichten, nie wieder denken.

```bash
# 1. SSH Key erstellen (falls noch nicht vorhanden)
ssh-keygen -t ed25519 -C "deine@email.com"
# → Einfach Enter drücken für alle Fragen

# 2. Public Key anzeigen
cat ~/.ssh/id_ed25519.pub
# → Diesen Text komplett kopieren

# 3. Auf GitHub eintragen
# github.com → Settings → SSH and GPG Keys → New SSH Key → Text einfügen

# 4. Verbindung testen
ssh -T git@github.com
# → "Hi username! You've successfully authenticated." = Erfolg
```

---

## 4. Installation — Schritt für Schritt

### Schritt 1: Claude Code installieren

```bash
# Node.js Version prüfen (muss 18+ sein)
node --version

# Claude Code installieren
npm install -g @anthropic-ai/claude-code

# Prüfen ob es funktioniert
claude --version
```

### Schritt 2: Claude Code einrichten

```bash
# Claude Code starten — beim ersten Start wirst du nach dem API Key gefragt
claude

# Alternativ: API Key als Environment Variable setzen
export ANTHROPIC_API_KEY="dein-api-key-hier"
```

> **Tipp:** Den `export` Befehl in deine `~/.bashrc` oder `~/.zshrc` eintragen, damit er
> bei jedem Terminal-Start aktiv ist.

### Schritt 3: Bootstrap Skill holen

Das ist der **einzige manuelle Schritt** — danach macht Claude alles automatisch.

```bash
# Bootstrap Skill vom GitHub Repository holen
mkdir -p /root/.claude/skills
cd /tmp
git clone --filter=blob:none --sparse git@github.com:vibercoder79/KI-Masterclass-Koerting-.git ki-skills
cd ki-skills
git sparse-checkout set bootstrap
cp -r bootstrap /root/.claude/skills/
cd /tmp && rm -rf ki-skills

# Prüfen ob der Skill da ist
ls /root/.claude/skills/bootstrap/
# → Sollte SKILL.md und einen references/ Ordner zeigen
```

> **Warum nur den Bootstrap Skill?** Der Bootstrap Skill installiert in Phase 2 automatisch
> alle weiteren Skills die du brauchst. Du musst nicht jeden einzeln holen.

### Schritt 4: Neues Projekt anlegen

```bash
# Verzeichnis für dein neues Projekt erstellen
mkdir ~/mein-projekt
cd ~/mein-projekt

# Claude Code im Projektverzeichnis starten
claude
```

### Schritt 5: Bootstrap ausführen

In der Claude Code Session:

```
/bootstrap
```

Claude führt dich jetzt durch 14 Fragen (ca. 5 Minuten) und baut danach alles automatisch auf.

---

## 5. Der Bootstrap-Prozess

### Phase 0: Fragen & Antworten (du + Claude)

Bootstrap stellt die Fragen in zwei Schritten.

#### Schritt 1: Stack-Frage — als allererstes

Bevor alles andere fragt Bootstrap:

```
Was möchtest du entwickeln?

a) Node.js / JavaScript Backend (API, CLI, Daemon)
b) Frontend (React, Vue, Vanilla JS)
c) Full-Stack (Node.js Backend + Frontend)
d) Python (KI/ML, Scripts, FastAPI, Django)
e) Anderes / Noch nicht klar
```

**Warum zuerst?** Die Antwort bestimmt welche Werkzeuge Bootstrap für dich einrichtet:

| Deine Wahl | Linter | Formatter | Wird automatisch angelegt |
|-----------|--------|-----------|--------------------------|
| Node.js | ESLint | — | `eslint.config.mjs` |
| Frontend | ESLint + Prettier | Prettier | `eslint.config.mjs` + `.prettierrc` |
| Full-Stack | ESLint + Prettier | Prettier | `eslint.config.mjs` + `.prettierrc` |
| Python | Ruff | Black | `pyproject.toml` |

Am Ende sagt Bootstrap dir auch welche VS Code Extensions du für deinen Stack brauchst.

#### Schritt 2: Restliche Fragen (14 Stück)

Danach kommen alle weiteren Infos auf einmal:

| Frage | Beispiel-Antwort | Warum wichtig |
|-------|-----------------|---------------|
| Projektname | `MeinShop` | Wird überall verwendet |
| Kurze Beschreibung | `E-Commerce für handgemachte Produkte` | Claude versteht was du baust |
| Projekt-Pfad | `/home/user/mein-projekt` | Wo der Code landet |
| GitHub Repository URL | `git@github.com:dein-user/mein-projekt.git` | Für Code-Backup und Versionierung |
| Linear Team Name | `MeinShop` | Für Issue-Tracking |
| Issue-Prefix | `SHOP` | Deine Stories heißen SHOP-1, SHOP-2... |
| Start-Version | `1.0.0` | Versionierung ab Tag 1 |

**Skills-Auswahl:**

```
Welche Skills installieren?
a) Minimum (ideation, implement, backlog)      ← Für den Start ideal
b) Standard (+ architecture-review, sprint-review, research, breakfix)  ← Empfohlen
c) Voll (alle Skills)                          ← Wenn du das volle Arsenal willst
d) Manuell auswählen
```

> **Empfehlung:** Starte mit **Standard (b)**. Du kannst jederzeit weitere Skills nachrüsten.

### Phase 1: Grundstruktur (automatisch, ~2 Minuten)

Claude erstellt alle Dateien — die Tooling-Dateien passen sich automatisch deinem Stack an:

```
dein-projekt/
├── lib/
│   └── config.js           ← Zentrale Konfiguration (SSoT)
├── agents/
│   └── self-healing.js     ← Überwacht dein System
├── journal/                ← Alle Logs
├── specs/
│   └── TEMPLATE.md         ← Vorlage für neue Features
├── docs/                   ← Technische Dokumentation
├── .claude/
│   ├── skills/             ← Deine installierten Skills
│   └── hooks/              ← Git-Governance-Regeln
├── CLAUDE.md               ← "Wer bin ich?" für die KI
├── SYSTEM_ARCHITECTURE.md
├── API_INVENTORY.md
├── INDEX.md
├── CHANGELOG.md
├── GOVERNANCE.md
├── SECURITY.md
├── .env.example            ← API Key Template
│
│   ── Stack-abhängig (automatisch) ──
├── eslint.config.mjs       ← Node.js / Frontend / Full-Stack
├── .prettierrc             ← nur Frontend / Full-Stack
└── pyproject.toml          ← nur Python (Black + Ruff)
```

**Die wichtigste Datei: `CLAUDE.md`**

Das ist der "Personalausweis" deines Projekts für die KI. Jedes Mal wenn du eine neue
Claude-Session startest, liest Claude diese Datei und kennt sofort:
- Was dein Projekt ist
- Welche Regeln gelten
- Wo welche Dateien sind
- Was zuletzt passiert ist

### Phase 2: Skills installieren (automatisch)

Claude lädt die gewählten Skills von GitHub:

```
Installing skills...
✓ ideation
✓ implement
✓ backlog
✓ architecture-review
✓ sprint-review
✓ research
✓ breakfix
```

### Phase 3: Self-Healing einrichten (automatisch)

Ein Agent der alle 15 Minuten automatisch prüft:
- Läuft alles wie geplant?
- Sind alle Docs auf dem neuesten Stand?
- Gibt es Inkonsistenzen?

Bei Problemen bekommst du einen Telegram-Alert (optional) oder einen Log-Eintrag.

### Phase 4: Automation Daemon (optional)

Wenn du Linear + GitHub nutzt: Ein Daemon der automatisch Code implementiert, sobald du
ein Issue auf "In Progress" setzt. Claude schreibt dann selbstständig Code, pusht ihn und
schließt das Issue.

### Phase 5: Fertig

```
✓ Projekt-Struktur angelegt
✓ 7 Skills installiert
✓ Git Hooks aktiv
✓ Self-Healing läuft
✓ Globale Registry aktualisiert

Dein Projekt ist bereit. Starte mit: /ideation
```

---

## 6. Die Skills — wann nutze ich was?

### Übersicht: Das Skill-System

Skills sind **wiederholbare Workflows** die Claude durch komplexe Aufgaben führen.
Du rufst sie mit `/skillname` auf und Claude folgt einem definierten Prozess.

```
Idee → /ideation → Story in Linear
Story → /implement → Code, Tests, Git Push
Problem → /breakfix → Diagnose, Fix, Prevention
Woche → /backlog → Was steht an?
Quartal → /sprint-review → System-Gesundheit
Jederzeit → /status → Was läuft gerade?
```

### `/ideation` — Von der Idee zur Story

**Wann:** Du hast eine Idee für ein neues Feature.

**Was passiert:**
1. Du beschreibst deine Idee in natürlicher Sprache
2. Claude recherchiert (optional: Deep Research mit Perplexity)
3. Claude prüft ob die Idee zur Architektur passt
4. Claude erstellt eine strukturierte User Story in Linear

**Beispiel:**
```
Du: /ideation

Claude: "Beschreibe deine Idee..."
Du: "Ich möchte dass Kunden ihre Bestellungen verfolgen können"

→ Claude erstellt SHOP-42 in Linear mit:
   - Was genau gebaut wird
   - Warum (Business Value)
   - Wie (technischer Ansatz)
   - Akzeptanzkriterien
   - Aufwandsschätzung
```

### `/implement` — Von der Story zum Code

**Wann:** Du willst eine Story umsetzen.

**Was passiert (10-Schritte-Prozess):**
1. Issue aus Linear laden
2. Spec-File erstellen (`specs/SHOP-42.md`)
3. **Operator-OK einholen** ← du bestätigst den Plan
4. Code schreiben
5. Tests
6. Git Commit + Push
7. Deploy Health Check
8. Linear Issue schließen
9. Changelog aktualisieren
10. Ergebnis präsentieren

**Beispiel:**
```
Du: /implement SHOP-42

Claude: [liest Issue, erstellt Plan, zeigt dir was er tun will]
Claude: "Soll ich loslegen? [Ja/Nein/Ändern]"
Du: "Ja"
Claude: [implementiert, testet, pusht, schließt Issue]
```

> **Wichtig:** `/implement` ändert NIEMALS Code ohne dein OK in Schritt 3.
> Du hast immer die Kontrolle.

### `/backlog` — Sprint Planning

**Wann:** Du weißt nicht was als nächstes wichtig ist.

**Was passiert:**
1. Claude lädt alle offenen Issues aus Linear
2. Analysiert Abhängigkeiten (was blockiert was?)
3. Schlägt priorisierte Reihenfolge vor
4. Zeigt Aufwand und Risiko pro Issue

**Beispiel:**
```
Du: /backlog

Claude zeigt:
┌─────────────┬──────────────────────────────────┬──────────┬──────────┐
│ Issue       │ Titel                            │ Prio     │ Aufwand  │
├─────────────┼──────────────────────────────────┼──────────┼──────────┤
│ SHOP-38     │ Zahlungsabwicklung reparieren    │ KRITISCH │ S        │
│ SHOP-42     │ Bestellverfolgung                │ HOCH     │ M        │
│ SHOP-51     │ Dashboard Redesign               │ MITTEL   │ L        │
└─────────────┴──────────────────────────────────┴──────────┴──────────┘
→ Empfehlung: SHOP-38 zuerst (blockiert SHOP-42)
```

### `/breakfix` — Wenn etwas kaputt ist

**Wann:** Das System hat ein Problem, einen Bug, oder verhält sich komisch.

**Was passiert (6-Schritte-Prozess):**
1. **Detect:** Was genau ist das Problem?
2. **Diagnose:** Warum passiert es?
3. **Fix:** Lösung implementieren
4. **Verify:** Ist es wirklich behoben?
5. **Document:** Incident in `journal/incidents/` archivieren
6. **Prevent:** Wie verhindern wir das in Zukunft?

**Beispiel:**
```
Du: /breakfix

Claude: "Beschreibe das Problem..."
Du: "Die API gibt seit heute 401 Fehler zurück"

→ Claude analysiert Logs, findet expired Session Token,
   implementiert automatischen Token-Refresh,
   schreibt Incident-Report, legt präventiven Test an
```

### `/architecture-review` — System-Gesundheit

**Wann:** Bevor du eine große Entscheidung triffst. Regelmäßig (monatlich).

**Was passiert:**
Claude prüft 8 Dimensionen deines Systems:
- Wird SSoT (Single Source of Truth) eingehalten?
- Gibt es zirkuläre Abhängigkeiten?
- Sind alle Sicherheitsregeln aktiv?
- Stimmt die Dokumentation mit dem Code überein?
- [und 4 weitere Dimensionen]

**Output:** Risiko-Tabelle mit konkreten Handlungsempfehlungen.

### `/research` — Deep Research

**Wann:** Du willst eine technische Entscheidung treffen und brauchst Fakten.

**Was passiert:**
- Automatisches Routing: Einfache Fragen → WebSearch, komplexe → Perplexity (tiefere KI-Analyse)
- Ergebnisse werden gegengeprüft
- Strukturierter Research-Report

**Beispiel:**
```
Du: /research

"Welche Payment-Provider funktionieren am besten mit Node.js
 und haben die niedrigsten Gebühren für Europa?"

→ Vergleichstabelle mit Stripe, Mollie, PayPal, Klarna
   inkl. Gebühren, Integrationsaufwand, Vor-/Nachteile
```

### `/sprint-review` — Quartals-Audit

**Wann:** Alle 4-6 Wochen.

**Was passiert:**
- Tech Debt Analyse: Was muss dringend aufgeräumt werden?
- Backlog-Hygiene: Welche Issues sind veraltet?
- Architektur-Check: Hat sich technische Schulden angesammelt?
- Empfehlungen für die nächsten Wochen

### `/status` — Auf einen Blick

**Wann:** Immer wenn du wissen willst was gerade los ist.

**Output:**
```
SYSTEM STATUS — MeinShop v1.3.2
─────────────────────────────────
✓ Alle Daemons laufen
✓ Letzte Änderung: vor 2h (SHOP-42 deployed)
⚠ 3 offene Issues in Backlog
✓ Win-Rate letzte 7 Tage: 87%
✓ Keine offenen Incidents
```

### `/integration-test` — Nach jeder Änderung

**Wann:** Automatisch nach `/implement`, aber auch manuell aufrufbar.

**Was passiert:**
Claude führt vordefinierte Checks durch und zeigt:
- Tier-1 Checks (KRITISCH — müssen grün sein)
- Tier-2 Checks (Warnungen — sollten geprüft werden)

---

## 7. Die Guardrails — dein Sicherheitsnetz

### Was sind Guardrails?

Guardrails sind **automatische Sicherheitsmechanismen** die verhindern dass du aus Versehen
Dinge tust die du bereust. Nicht als Strafe — als dein Fallschirm.

### Guardrail 1: Spec-Gate (Git Hook)

**Problem:** Du änderst Code ohne zu wissen warum — und in 3 Wochen erinnerst du dich nicht mehr.

**Lösung:** Bevor du Code committen kannst (der zu einem Issue gehört), muss ein Spec-File
(`specs/SHOP-42.md`) existieren das erklärt **was** und **warum**.

```bash
git commit -m "SHOP-42: Add order tracking"
# → Ohne specs/SHOP-42.md: BLOCKIERT
# → Mit specs/SHOP-42.md: erlaubt

# ⛔ spec-gate: specs/SHOP-42.md fehlt!
#    Erstelle zuerst specs/SHOP-42.md aus specs/TEMPLATE.md
#    Bypass: git commit --no-verify (nur wenn du bewusst drüber bist)
```

**Bypass vorhanden?** Ja: `--no-verify`. Aber du weißt dann bewusst dass du die Regel brichst.

### Guardrail 2: Doc-Version-Sync (Git Hook)

**Problem:** Du erhöhst die Version in `config.js` aber vergisst 5 Dokumentationsdateien.

**Lösung:** Wenn `config.js` mit einer neuen Version gestaged ist, prüft der Hook automatisch
ob alle Docs auf der gleichen Version sind.

```bash
git commit -m "v1.4.0 - neue Features"
# → config.js: VERSION = '1.4.0'
# → SYSTEM_ARCHITECTURE.md: Version: 1.3.2 → BLOCKIERT

# ⛔ doc-version-sync: SYSTEM_ARCHITECTURE.md noch auf v1.3.2!
#    Bitte auf v1.4.0 aktualisieren
```

### Guardrail 3: Self-Healing Agent

Ein Agent der alle 15 Minuten im Hintergrund prüft:

| Check | Was wird geprüft |
|-------|-----------------|
| Signal Freshness | Sind alle Daten aktuell? |
| Doc Sync | Stimmen alle Dokumentationsversionen überein? |
| Architecture Guard | Sind Kern-Regeln eingehalten? |
| API Health | Sind alle externen APIs erreichbar? |
| Security Events | Gab es verdächtige Aktivitäten? |

Bei Problemen: Telegram-Alert (wenn eingerichtet) oder Log-Eintrag in `journal/`.

### Guardrail 4: Spec-Driven Development

Die einfachste aber mächtigste Regel:

```
NIEMALS Code ändern ohne Linear-Issue
NIEMALS Code committen ohne Spec-File (specs/ISSUE-ID.md)
NIEMALS Operator (= du) übergehen — immer erst zeigen dann tun
```

Das klingt nach extra Arbeit. In der Praxis dauert ein Spec-File 2 Minuten — und verhindert
Stunden an Debug-Arbeit weil du weißt was du warum gebaut hast.

### Guardrail 5: Operator-in-the-Loop

Bei `/implement`: **Schritt 3 ist immer ein Pause-Punkt.**
Claude zeigt dir den Plan, du sagst OK, dann erst wird Code geschrieben.

Du kannst niemals aus Versehen etwas deployen das du nicht gesehen hast.

---

## 8. VS Code Setup

### Claude Code Extension

Die offizielle Claude Code Extension für VS Code integriert alles direkt in deinen Editor:

- Terminal mit Claude Code direkt in VS Code
- Datei-Kontext wird automatisch an Claude übergeben
- Inline Code-Vorschläge
- Direkt aus dem Editor `/implement` aufrufen

**Installation:**
```
VS Code → Extensions → "Claude Code" suchen → Install
```

### Die Basis-Plugins (immer, für jeden Stack)

Diese 3 Plugins installierst du **einmal** — sie funktionieren für alle Projekte:

**1. ESLint** — Coding-Regeln in Echtzeit
→ https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint
- Prüft deinen Code automatisch gegen die Regeln in `eslint.config.mjs`
- Zeigt Fehler und Warnungen direkt im Editor (rote/gelbe Unterkringelung)
- **Verbindung zur Governance:** Der `/implement` Skill ruft ESLint nach jeder Änderung
  automatisch auf — Fehler blockieren den Commit

**2. SonarQube for IDE (SonarLint)** — Tiefenanalyse
→ https://marketplace.visualstudio.com/items?itemName=SonarSource.sonarlint-vscode
- Analysiert tiefergehende Muster: Code Smells, potenzielle Bugs, Security Vulnerabilities
- Arbeitet passiv im Hintergrund — kein manuelles Starten
- Findet was ESLint nicht findet — SQL Injection, hardcoded Credentials, unsichere Crypto-Nutzung

**3. Error Lens** — Kein Verstecken mehr
→ https://marketplace.visualstudio.com/items?itemName=usernamehw.errorlens
- Zeigt ESLint- und SonarLint-Findings **direkt in der Zeile** — nicht erst beim Hover
- Rote Zeile = Fehler. Gelbe Zeile = Warnung. Sofort sichtbar, nicht ignorierbar.

### Stack-spezifische Plugins

Abhängig davon was du entwickelst, kommen diese dazu:

**Node.js / JavaScript Backend:**

→ REST Client (API-Endpunkte direkt aus VS Code testen)
  https://marketplace.visualstudio.com/items?itemName=humao.rest-client

**Frontend (React, Vue, Vanilla JS):**

→ Prettier — automatisches Formatieren beim Speichern
  https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode

→ Auto Rename Tag — HTML-Tags automatisch umbenennen
  https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag

→ CSS Peek — CSS-Klassen direkt aus HTML anspringen
  https://marketplace.visualstudio.com/items?itemName=pranaygp.vscode-css-peek

**Full-Stack:**

→ Prettier — automatisches Formatieren beim Speichern
  https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode

**Python:**

→ Python (Pflicht — Grundlage für alles)
  https://marketplace.visualstudio.com/items?itemName=ms-python.python

→ Black Formatter — automatisches Formatieren
  https://marketplace.visualstudio.com/items?itemName=ms-python.black-formatter

→ Ruff — Linter (moderner Ersatz für Flake8)
  https://marketplace.visualstudio.com/items?itemName=charliermarsh.ruff

→ Error Lens
  https://marketplace.visualstudio.com/items?itemName=usernamehw.errorlens

→ SonarLint
  https://marketplace.visualstudio.com/items?itemName=SonarSource.sonarlint-vscode

→ Pylance — bessere Autovervollständigung (optional)
  https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance

→ Jupyter — für Data Science / ML Notebooks (optional)
  https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter

> **Tipp:** Bootstrap gibt dir am Ende des Setups automatisch die passenden Links
> für deinen Stack aus — einfach klicken und installieren. Kein Suchen nötig.

**Das Zusammenspiel:**
```
Du tippst Code
  → Error Lens zeigt ESLint + SonarLint Findings inline (sofort)
  → Du fixst während du schreibst

/implement wird ausgeführt
  → ESLint CLI läuft automatisch: npx eslint --max-warnings=0
  → 0 Errors = Gate bestanden → weiter
  → Errors vorhanden = Gate blockiert → erst fixen
```

**Die Regeldatei: `eslint.config.mjs`**

Der Bootstrap legt diese Datei **automatisch im Projekt-Root an** — du musst nichts manuell tun.
Das VS Code ESLint Plugin erkennt sie beim Öffnen des Projekts sofort und aktiviert alle Regeln.

Sie enthält:
- Fehler-Prävention: `no-undef`, `no-unreachable`, `use-isnan`
- Security-Regeln: `no-eval`, `no-implied-eval`, `no-new-func`
- Qualitäts-Regeln: `eqeqeq`, `no-unused-vars`, `prefer-const`
- Async-Regeln: `no-async-promise-executor`, `no-await-in-loop`
- Lesbarkeit: `max-len` (120 Zeichen), `max-depth` (5 Ebenen)

Anpassen: Öffne `eslint.config.mjs` im Projekt-Root und füge/entferne Regeln nach Bedarf.

### ESLint bei einem neuen Projekt

**Szenario:** Du startest ein neues Projekt in Claude Code — die `eslint.config.mjs` fehlt noch.

**Mit Bootstrap (empfohlen):**
```
/bootstrap
```
Bootstrap erstellt die `eslint.config.mjs` automatisch in Phase 1 — du musst nichts weiter tun.
Danach erkennt das VS Code ESLint Plugin sie sofort.

**Ohne Bootstrap (manuell):**
1. Kopiere die `eslint.config.mjs` aus einem bestehenden Projekt in den Root des neuen Projekts
2. Alle Regeln sind generisch — keine Anpassung nötig für Node.js Projekte
3. VS Code ESLint Plugin aktiviert sich automatisch beim nächsten Öffnen der Datei

**Wo liegt die Datei?**
```
mein-projekt/           ← Projekt-Root (wo du claude startest)
├── eslint.config.mjs   ← HIER — direkt im Root, nicht in einem Unterordner
├── lib/
├── agents/
└── ...
```

> **Wichtig:** ESLint v9+ verwendet das neue Format (`eslint.config.mjs`). Das alte Format
> (`.eslintrc.js`) ist veraltet. Bootstrap erstellt immer das neue Format.

### Empfohlene VS Code Settings für Governance

Erstelle `.vscode/settings.json` in deinem Projekt:

```json
{
  // Auto-Formatierung beim Speichern
  "editor.formatOnSave": true,

  // Git-Blame in Statuszeile anzeigen (GitLens)
  "gitlens.statusBar.enabled": true,
  "gitlens.currentLine.enabled": true,

  // Trailing Whitespace entfernen
  "files.trimTrailingWhitespace": true,

  // Finale Newline hinzufügen
  "files.insertFinalNewline": true,

  // Terminal: Projektverzeichnis als Standard
  "terminal.integrated.cwd": "${workspaceFolder}",

  // Dateien die ignoriert werden sollen
  "files.exclude": {
    "**/.git": true,
    "**/node_modules": true,
    "**/.env": true
  },

  // .env Dateien NIEMALS in Source Control
  "git.ignoredRepositories": [],
  "dotenv.enableAutocloaking": true
}
```

### Empfohlene VS Code Coding Rules (`.editorconfig`)

Erstelle `.editorconfig` im Projektroot:

```ini
# EditorConfig hilft Entwicklern konsistenten Code zu schreiben
# https://editorconfig.org
root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.md]
trim_trailing_whitespace = false

[Makefile]
indent_style = tab
```

Diese Datei wird **automatisch von VS Code respektiert** (kein Plugin nötig) und stellt sicher
dass Code-Formatierung konsistent ist — egal wer am Projekt arbeitet.

---

## 9. Governance für dein Projekt anpassen

### Die zentrale Konfigurations-Datei: `lib/config.js`

Alles läuft über eine einzige Datei — das ist der **Single Source of Truth (SSoT)** Prinzip.

```javascript
// lib/config.js — Beispiel-Struktur nach Bootstrap

module.exports = {
  // Projekt-Identität
  PROJECT_NAME: 'MeinShop',
  VERSION: '1.0.0',           // ← Diese Zahl steuert ALLE Versions-Nummern

  // Linear Integration
  LINEAR_TEAM: 'MeinShop',
  LINEAR_PREFIX: 'SHOP',

  // Dokumentationsdateien (werden automatisch auf VERSION geprüft)
  DOC_FILES: [
    { path: 'SYSTEM_ARCHITECTURE.md', versionPattern: /\*\*Version:\*\*\s*([\d.]+)/ },
    { path: 'CHANGELOG.md', versionPattern: /## v([\d.]+)/ },
    // weitere Docs...
  ],

  // Deine eigenen Konfigurationen
  APP: {
    port: 3000,
    environment: 'development',
  }
};
```

**Wichtigste Regel:** Wenn du `VERSION` erhöhst, müssen alle `DOC_FILES` auf die neue Version
aktualisiert werden. Der Doc-Version-Sync Hook erzwingt das automatisch.

### CLAUDE.md anpassen — Claude kennenlernen

Die `CLAUDE.md` ist der Kern. Hier sagst du Claude wer er/sie ist:

```markdown
# Mein Projekt — Context File

## Wer bist du?

Du bist der Lead-Entwickler für MeinShop — einen E-Commerce für handgemachte Produkte.
[Beschreibe dein Projekt in 3-5 Sätzen]

## Deine Aufgabe

1. [Hauptaufgabe 1]
2. [Hauptaufgabe 2]
3. Dokumentation immer aktuell halten

## Das System

[Beschreibe die Architektur in groben Zügen]

## Regeln

- NIEMALS Code ändern ohne Linear-Issue
- NIEMALS Spec-File vergessen
- [Deine eigenen Regeln]
```

**Je besser du das befüllst, desto besser kennt Claude dein Projekt.**

### Issue-Prefix anpassen

Der Bootstrap erstellt alles mit deinem gewählten Prefix. Beispiele:

- E-Commerce Shop: `SHOP-`
- Mobile App: `APP-`
- API Service: `API-`
- Marketing Tool: `MKT-`

### Eigene Skills erstellen

Mit dem `/skill-creator` Skill kannst du eigene, projektspezifische Workflows erstellen:

```
/skill-creator

"Ich möchte einen Skill der täglich automatisch
 unsere Produktpreise mit der Konkurrenz vergleicht
 und einen Report erstellt."

→ Claude erstellt /price-monitor Skill mit passendem Workflow
```

---

## 10. Tägliche Nutzung — ein typischer Workflow

### Morgens: Was steht an?

```bash
# Terminal öffnen, ins Projektverzeichnis
cd ~/mein-projekt
claude

# Überblick verschaffen
/status
/backlog
```

Claude zeigt dir: offene Issues, System-Gesundheit, was zuletzt passiert ist.

### Feature entwickeln

```
Schritt 1 — Idee formalisieren:
/ideation
→ "Ich möchte X bauen weil..."
→ Claude erstellt SHOP-XX in Linear

Schritt 2 — Implementieren:
/implement SHOP-XX
→ Claude zeigt Plan → Du bestätigst → Code wird geschrieben
→ Automatisch: Tests, Git Push, Linear Issue geschlossen

Schritt 3 — Prüfen:
/integration-test
→ Alle Checks grün? Gut. Rotes Kreuz? Claude erklärt was zu tun ist.
```

### Bug aufgetaucht?

```
/breakfix
→ Problem beschreiben
→ Claude diagnostiziert
→ Fix implementieren
→ Incident dokumentiert
→ Präventivmaßnahme installiert
```

### Ende der Woche

```
/sprint-review
→ Was haben wir diese Woche gemacht?
→ Was ist Tech Debt?
→ Prioritäten für nächste Woche
```

### Beispiel: Ein vollständiger Tag

```
09:00  /status          → Alles grün, 3 offene Issues
09:05  /backlog         → SHOP-38 hat höchste Prio (Zahlungsfehler)
09:10  /implement SHOP-38
09:12  → Claude zeigt Plan: "Session Token Refresh implementieren"
09:13  → Du: "Ja, los"
09:25  → Code implementiert, getestet, gepusht, Issue geschlossen
09:30  /integration-test → Alle 12 Checks grün
10:00  /ideation        → Neue Idee: Newsletter-System
10:15  → SHOP-55 erstellt in Linear
11:00  /implement SHOP-55
...
17:00  /sprint-review   → Wochenrückblick
```

---

## 11. Häufige Fragen

### "Ich bin kein Entwickler. Funktioniert das trotzdem für mich?"

Ja. Die Skills sind bewusst so designed dass du kein tiefes technisches Wissen brauchst.
Du beschreibst was du willst in normaler Sprache — Claude übernimmt die technische Umsetzung.
Die Governance sorgt dafür dass dabei trotzdem strukturiert und sicher vorgegangen wird.

### "Was wenn ich einen Fehler mache und etwas kaputt geht?"

Dafür gibt es `/breakfix`. Und weil jede Änderung in Git ist, kann jeder Schritt rückgängig
gemacht werden:

```bash
# Letzte Änderung rückgängig machen
git revert HEAD

# Zu einem bestimmten Zeitpunkt zurückgehen
git log --oneline    # → zeigt alle Commits
git checkout <hash>  # → zu diesem Zustand zurück
```

### "Muss ich wirklich für jedes kleine Feature ein Issue anlegen?"

Für winzige Tippfehler: Nein. Für alles was mehr als 10 Minuten Arbeit ist: Ja.

Der Aufwand für ein Issue ist 2 Minuten mit `/ideation`. Der Aufwand für ein undokumentiertes
Feature das in 3 Monaten für Probleme sorgt: Stunden.

### "Kann ich mehrere Projekte haben?"

Ja. Der Bootstrap-Prozess richtet für jedes Projekt eine eigenständige Umgebung ein.
Claude Code merkt anhand des Arbeitsverzeichnisses welches Projekt gerade aktiv ist.

### "Was kostet das?"

| Service | Kosten |
|---------|--------|
| Claude Code CLI | Im Claude Pro Abo enthalten |
| GitHub | Kostenlos |
| Linear | Kostenlos (Hobby Plan) |
| OpenRouter | Pay-as-you-go (~$0.001 pro Anfrage) |
| Telegram Bot | Kostenlos |

Für ein kleines Projekt: **0 bis ~$5/Monat**.

### "Was wenn ich die Governance-Regeln lästig finde?"

Alle Guardrails haben einen `--no-verify` Bypass. Du kannst sie umgehen — aber bewusst.

Das Ziel ist nicht Kontrolle sondern **bewusstes Handeln**. Wenn du weißt "ich umgehe
gerade die Regel weil X" ist das gut. Wenn du aus Versehen Regeln brichst ohne es zu
merken — das ist das Problem das Governance verhindert.

### "Wie aktualisiere ich die Skills wenn neue Versionen kommen?"

```bash
# Nur Bootstrap aktualisieren (wie beim ersten Mal)
cd /tmp
git clone --filter=blob:none --sparse git@github.com:vibercoder79/KI-Masterclass-Koerting-.git ki-skills
cd ki-skills
git sparse-checkout set bootstrap
cp -r bootstrap /root/.claude/skills/
cd /tmp && rm -rf ki-skills

# In Claude Code: Bootstrap kann dann bestehende Skills updaten
/bootstrap --update
```

---

## Anhang A: Checkliste vor dem ersten Bootstrap

```
Vor /bootstrap:

SOFTWARE:
☐ Node.js v18+ installiert (node --version)
☐ Git installiert (git --version)
☐ Claude Code installiert (claude --version)

ACCOUNTS:
☐ Anthropic Account + API Key
☐ GitHub Account + SSH Key eingerichtet (ssh -T git@github.com)
☐ Linear Account + API Key (optional aber empfohlen)

INFORMATIONEN BEREIT:
☐ Projektname (z.B. "MeinShop")
☐ Kurze Projekt-Beschreibung (1-2 Sätze)
☐ Gewünschter Projektpfad
☐ GitHub Repository URL (neues leeres Repo angelegt)
☐ Linear Team Name (falls genutzt)
☐ Gewünschter Issue-Prefix (z.B. "SHOP")

BOOTSTRAP SKILL:
☐ /root/.claude/skills/bootstrap/ vorhanden
☐ SKILL.md in diesem Ordner sichtbar
```

## Anhang B: Wichtige Dateien Spickzettel

| Datei | Zweck | Wann anfassen |
|-------|-------|---------------|
| `CLAUDE.md` | KI-Persönlichkeit & Regeln | Beim Setup, bei großen Änderungen |
| `lib/config.js` | Alle Konfigurationen | Bei Versionsänderungen, neuen Einstellungen |
| `specs/TEMPLATE.md` | Story-Vorlage | Als Vorlage für neue Specs |
| `specs/ISSUE-XX.md` | Spec für eine Story | Vor jeder Implementierung |
| `CHANGELOG.md` | Was hat sich wann geändert | Automatisch durch /implement |
| `API_INVENTORY.md` | Alle externen APIs | Bei jeder neuen API-Integration |
| `.env` | API Keys & Secrets | Initial + bei neuen Keys |
| `journal/` | Alle Logs & Incidents | Nur lesen / durch Tools schreiben |

## Anhang C: Glossar

| Begriff | Bedeutung |
|---------|-----------|
| **SSoT** | Single Source of Truth — eine einzige Quelle für eine Information |
| **Governance** | Regeln und Prozesse die sicherstellen dass ein System gesund bleibt |
| **Spec** | Spec-File — kurzes Dokument das beschreibt was und warum gebaut wird |
| **Issue** | Eine Aufgabe/Story in Linear |
| **Git Hook** | Automatischer Check der bei Git-Befehlen ausgeführt wird |
| **Self-Healing** | System das Probleme selbst erkennt und (wenn möglich) behebt |
| **Daemon** | Ein Prozess der dauerhaft im Hintergrund läuft |
| **Vibe Coding** | KI-gestütztes Entwickeln wo die KI großteils den Code schreibt |

---

*Dieses Handbuch ist Teil des OpenCLAW Governance Frameworks.*
*GitHub: github.com/vibercoder79/KI-Masterclass-Koerting-*
*Letzte Aktualisierung: 2026-03-28*
