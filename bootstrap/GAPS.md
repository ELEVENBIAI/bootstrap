# Bootstrap v2.1 — Gap-Analyse

> **Erstellt:** 2026-04-07 | **Methode:** 3 parallele Audit-Agents (CLAUDE.md-Regeln,
> Skills-Coverage, Artefakte/Hooks) gegen produktiven CLAW-Stand (v5.17.22)

Dieses Dokument listet alle Lücken, die verhinden, dass ein neues Projekt aus dem Bootstrap
heraus "Production Ready" ist — im Sinne der CLAW-Governance-Qualität.

---

## Legende

| Priorität | Bedeutung |
|-----------|-----------|
| 🔴 KRITISCH | Blockiert korrekte Nutzung — muss zuerst geschlossen werden |
| 🟠 HOCH | Wesentliche Governance fehlt — fehlt im täglichen Betrieb |
| 🟡 MITTEL | Vervollständigung — verbessert Qualität, aber kein Blocker |
| 🟢 NIEDRIG | Nice to have |

---

## BLOCK 1: CLAUDE.md Template (file-templates.md)

### G-01 🔴 Fehlende KERN-REGELN (9 von 25)

**Was fehlt:** Das Bootstrap CLAUDE.md §4 hat nur 9 generische Regeln. CLAW hat 25.
Die folgenden sind generisch genug für jedes Projekt:

| Fehlende Regel | Generische Formulierung für Bootstrap |
|---------------|--------------------------------------|
| NIEMALS Spec-File ohne Hook prüfen | "Hook `.claude/hooks/spec-gate.sh` blockiert Commits automatisch" |
| NIEMALS `async` ohne `await` | "Async-Calls (HTTP, Telegram, Webhooks) immer mit `await` — silent failures vermeiden" |
| NIEMALS `fs.readFileSync` auf wachsende Dateien | "Log-/JSONL-Dateien niemals komplett einlesen — Chunks oder Streams nutzen" |
| NIEMALS neue Komponente ohne Inventar-Eintrag | "Neue Komponente sofort in COMPONENT_INVENTORY.md + INDEX.md vor git commit" |
| NIEMALS CLI-Exit 0 bei API-Fehler | "CLI-Prozesse mit externen API-Calls müssen bei Fehler mit `exit 1` enden" |
| NIEMALS Git Hook ignorieren/bypassen | "`.claude/hooks/` sind Governance-Gates — Bypass (`--no-verify`) nur mit explizitem Operator-OK" |
| NIEMALS ADR-Blockade nicht eskalieren | "Wenn Architecture Review eine Story blockiert → Operator SOFORT informieren, nicht still umbauen" |
| NIEMALS Cron-Variablen ($VAR) in Crontabs | "In Crontabs/supercronic absolute Pfade statt `$VARIABLE` — Shell-Expansion läuft nicht in Cron" |
| NIEMALS LLM mit kurzen Timeouts für Batch-Tasks | "Komplexe LLM-Calls nicht für repetitive Cron-Batch-Tasks — Haiku/kleinere Modelle nutzen" |

**Wo im Bootstrap:** `references/file-templates.md` → CLAUDE.md §4 erweitern.
**Wie generisch:** 100% generisch — kein CLAW-spezifischer Kontext nötig.

---

### G-02 🟠 Fehlende Proaktive Pflicht in §2

**Was fehlt:** CLAW CLAUDE.md §2 enthält einen Absatz der Claude anweist, bei jeder Session
aktiv nach Blockern zu fragen — nicht nur auf Anfragen zu reagieren.

**Fehlender Text (Bootstrap-Version):**

```markdown
**Proaktive Pflicht:** Bei jeder Session aktiv fragen: "Verbessert das die
Kernziele — oder blockiert ein strukturelles Problem das gerade?"
Architektonische Blocker (fehlende Tests, kaputte Gates, inaktive Komponenten)
**ohne Aufforderung** melden.
```

**Wo im Bootstrap:** `references/file-templates.md` → CLAUDE.md §2 nach der Direktive.

---

### G-03 🟠 Rollback-Pläne-Struktur fehlt (§6)

**Was fehlt:** CLAW CLAUDE.md hat §6 "Rollback-Pläne" mit trigger-basierter Tabelle.
Bootstrap hat keinen entsprechenden Abschnitt.

**Template das hinzukommen soll:**

```markdown
## 6. ROLLBACK-PLÄNE

### [Feature-Name oder ISSUE-PREFIX-XXX]

| Trigger | Massnahme |
|---------|-----------|
| [Bedingung 1] | [Rücknahme-Aktion] |
| [Bedingung 2] | [Config-Anpassung] |

**Restart-Befehl (falls Daemon):**
```bash
kill $(cat .pid-file) && sleep 2 && bash start-script.sh &
```
```

**Wo im Bootstrap:** `references/file-templates.md` → CLAUDE.md nach §5 ergänzen.

---

## BLOCK 2: governance-template.md

### G-04 🔴 settings.json Hook-Aktivierung fehlt

**Was fehlt:** Bootstrap erzeugt Hook-Dateien (`.claude/hooks/*.sh`), aber erklärt nirgends
wie sie in Claude Code aktiviert werden. Ohne Eintrag in `settings.json` laufen die Hooks
niemals.

**Konsequenz:** Hooks sind vorhanden aber wirkungslos — Governance-Enforcement funktioniert nicht.

**Was hinzukommen soll** in `references/file-templates.md` als eigener Abschnitt:

```markdown
## settings.json (Hooks + Permissions)

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

**Hinweis:** Weitere Permissions nach Bedarf ergänzen (API-Calls, MCP-Tools).
```

---

### G-05 🟠 Agent Team Setup Pflicht-Sektion in Story-Template

**Was fehlt:** CLAW Governance erzwingt in jeder Story eine "Agent Team Setup"-Sektion,
die entscheidet ob solo oder im Team gearbeitet wird.

**Entscheidungslogik fehlt in Bootstrap:**

```markdown
## Agent Team Setup

**Solo oder Team?**

| Kriterium | Empfehlung |
|-----------|-----------|
| Mehrere Dateien/Layer betroffen | Team (+ Architect) |
| Blockiert andere Stories | Team |
| Sicherheits-/Compliance-relevant | Team + Security Review |
| Infra-Änderungen | Team + Cloud Engineer |
| Einzelne Komponente, klares Template | Solo |
| Docs / Reviews | Solo |

**Gewählt:** [ ] Solo  [ ] Team — [Begründung]
```

**Wo im Bootstrap:** `references/file-templates.md` → specs/TEMPLATE.md ergänzen.

---

### G-06 🟠 Fehlende Abschnitte in governance-template.md

**Was fehlt (3 Abschnitte):**

1. **§4.7 Maschinelle Enforcement-Hooks** — erklärt dass Governance maschinell erzwungen wird:
   ```
   spec-gate.sh: blockiert git commit wenn Spec-File fehlt
   doc-version-sync.sh: blockiert git commit wenn Docs nicht auf aktueller VERSION
   ```

2. **§5.3 Obsidian Vault Sync** — detaillierter Ablauf von doc-sync.js:
   ```
   Wann: bei VERSION-Bump in config.js
   Was: alle DOC_FILES + Changelog + Obsidian-Frontmatter
   Wie: lib/doc-sync.js → syncAllDocs(newVersion)
   ```

3. **§7 Rollback-Pläne** — generische Rollback-Tabellen-Struktur (s. G-03).

**Wo:** `references/governance-template.md` — nach §4.6 und nach §5.

---

## BLOCK 3: specs/TEMPLATE.md

### G-07 🔴 specs/TEMPLATE.md zu knapp — fehlende Sektionen

**Was fehlt:** CLAW specs/TEMPLATE.md ist 170 Zeilen. Bootstrap-Äquivalent hat ~40 Zeilen.
Fehlende Sektionen mit Governance-Wirkung:

| Sektion | CLAW | Bootstrap | Warum wichtig |
|---------|------|-----------|---------------|
| **DB Schema Impact** | ✅ | ❌ | Migrations müssen explizit geplant werden |
| **Current State** | ✅ | ❌ | Verhindert blinde Implementierungen |
| **Dokumentations-Impact** | ✅ | ❌ | Kein Doc ohne Code-Änderung |
| **Task Design Rules** | ✅ | ❌ | Max 3 Dateien/Task — verhindert überkomplexe Tasks |
| **Sizing Guide** | ✅ | ❌ | S/M/L Klassifizierung für Sprint-Planung |
| **Done-Kriterien** | ✅ | ❌ | End-to-End-Validierung explizit |
| **Rollback Plan** | ✅ | ❌ | Recovery bei fehlgeschlagenem Deployment |

**Konkrete Task Design Rules die fehlen:**
- Max 3 Dateien pro Task
- Jeder Task hat einen konkreten Verify-Step
- Letzter Task ist immer: Doku + Config

**Wo:** `references/file-templates.md` → Sektion `specs/TEMPLATE.md` komplett ersetzen.

---

## BLOCK 4: Fehlende Artefakte (neue reference-Dateien)

### G-08 🟠 ARCHITECTURE_DESIGN.md Template fehlt

**Was fehlt:** Bootstrap hat nur `SYSTEM_ARCHITECTURE.md` (Komponenten-Liste). CLAW hat
zusätzlich `ARCHITECTURE_DESIGN.md` — das lebende Dokument für Design-Rationale, ADRs und
das "Warum" hinter Architekturentscheidungen.

Im Bootstrap definiert CLAUDE.md es als Einstiegsdokument (`§4: ARCHITECTURE_DESIGN.md ist das Einstiegsdokument`).
Aber ein Template dafür existiert nicht.

**Template das hinzukommen soll** (`references/architecture-design-template.md`):

```markdown
# {{PROJECT_NAME}} — Architecture Design

**Version:** {{VERSION_START}} | **Stand:** {{TODAY}}

> **Einstiegsdokument.** Jede neue Komponente und jedes neue File wird hier zuerst
> eingetragen — vor dem git commit.

## Big Picture

[Systemkarte — Übersicht aller Komponenten und ihrer Verbindungen]

## Design-Rationale ("Das Warum")

[Begründung der wesentlichen Architekturentscheidungen]

## ADR — Architecture Decision Records

| ADR | Titel | Status | Datum |
|-----|-------|--------|-------|
| ADR-01 | [Erste Entscheidung] | Active | {{TODAY}} |

## Referenzen

[Links zu allen verknüpften Architecture-Dokumenten]
```

**Wo:** Neue Datei `references/architecture-design-template.md` + in SKILL.md Phase 1 erwähnen.

---

### G-09 🟡 journal/STRATEGY_LOG.md Konzept fehlt

**Was fehlt:** CLAW nutzt `journal/STRATEGY_LOG.md` als persistentes Log aller strategischen
Entscheidungen und evaluierten Alternativen. Wird als Pflichtlektüre vor `/ideation` genutzt
um Wiederholungen zu vermeiden.

**Bootstrap hat:** Kein `journal/`-Konzept.

**Template das hinzukommen soll** (in `references/file-templates.md`):

```markdown
## journal/STRATEGY_LOG.md

```markdown
# {{PROJECT_NAME}} — Strategy Log

> Pflichtlektüre vor /ideation. Dokumentiert strategische Entscheidungen und
> evaluierte Alternativen — damit sie nicht wieder diskutiert werden.

## [{{TODAY}}] — Initiales Setup

**Kontext:** Projekt aufgesetzt mit Bootstrap v2.1
**Entscheidungen:** [Liste erste Architektur-Entscheidungen]
**Verworfen:** — (noch keine Alternativen evaluiert)
```
```

---

### G-10 🟡 Dokumentations-Kategorien nicht definiert

**Was fehlt:** CLAW hat eine bewährte Taxonomie von Docs-Typen in `docs/`. Bootstrap
gibt keine Orientierung welche Kategorien sinnvoll sind.

**Sinnvolle Kategorien für neue Projekte:**

```markdown
## Empfohlene Docs-Kategorien

docs/
├── OPERATOR_CHEATSHEET.md   ← Quick-Reference für häufige Ops-Aufgaben
├── RUNBOOK.md               ← Schritt-für-Schritt bei bekannten Problemen
├── MONITORING.md            ← Was wird gemessen, welche Alerts gibt es
├── DEPLOYMENT_ARCHITECTURE.md ← Container/Server-Setup
├── SELF_HEALING.md          ← Welche Checks laufen automatisch
```

**Wo:** Neuer Abschnitt in `references/file-templates.md` oder `references/doc-categories.md`.

---

## BLOCK 5: Generierte Skills — Templates zu generisch

### G-11 ~~🟠~~ ❌ AUSGESCHLOSSEN — integration-test Architektur-Karte

**Entscheidung (2026-04-08):** Ausgeschlossen — CLAW-spezifisch.
Die 27 Checks und die Orchestrierungslogik sind zu eng an das Trading-System gekoppelt.
Das Bootstrap-Template bleibt generisch: Der Operator definiert eigene Tier-Checks beim
Setup. Kein Mehrwert für andere Projekte durch CLAW-Karten-Konzept.

---

### G-12 ~~🟠~~ ❌ AUSGESCHLOSSEN — status Architektur-Radar

**Entscheidung (2026-04-08):** Ausgeschlossen — CLAW-spezifisch.
Brain-DB-Queries, Asset-Registry, Coverage-Gate sind Trading-Platform-Konzepte.
Das generische Konzept "strukturelle Metriken" ist zu abstrakt um sinnvoll vorzuschreiben.
Operator definiert domain-spezifische Radar-Checks selbst nach System-Aufbau.

---

### G-13 🟡 breakfix Template: CLAUDE.md-Regel-Learning fehlt

**Was fehlt:** CLAW `/breakfix` Schritt 7 ist einer der wertvollsten Steps:
"Welche CLAUDE.md-Regel hätte diesen Incident verhindert?" — leitet eine neue
Governance-Regel ab und ergänzt sie in CLAUDE.md.

Bootstrap-Template erwähnt das nur am Rand.

**Ergänzung für `references/breakfix-template.md`:**

```markdown
## Schritt 7: CLAUDE.md-REGEL (PFLICHT)

Nach jedem Incident:
1. "Welche CLAUDE.md-Regel hätte diesen Incident verhindert?"
2. Regel formulieren: "NIEMALS [X] ohne [Y]"
3. In CLAUDE.md §4 eintragen
4. In Incident-File dokumentieren: `CLAUDE.md-Regel: NIEMALS...`

**Ziel:** Jeder Incident macht das System dauerhaft resilienter.
Ohne diesen Schritt wiederholt sich der Incident.
```

---

## PRIORISIERTE UMSETZUNGSREIHENFOLGE

| Prio | Gap | Aufwand | Datei |
|------|-----|---------|-------|
| 1 | G-04 settings.json Hook-Aktivierung | 30 Min | file-templates.md |
| 2 | G-07 specs/TEMPLATE.md Sektionen | 60 Min | file-templates.md |
| 3 | G-01 Fehlende NIEMALS-Regeln (9) | 30 Min | file-templates.md |
| 4 | G-08 ARCHITECTURE_DESIGN.md Template | 45 Min | neues references/ File |
| 5 | G-05 Agent Team Setup in Story-Template | 20 Min | file-templates.md |
| 6 | G-06 governance-template.md Abschnitte | 45 Min | governance-template.md |
| 7 | G-02 Proaktive Pflicht §2 | 10 Min | file-templates.md |
| 8 | G-03 Rollback-Pläne §6 | 15 Min | file-templates.md |
| 9 | G-09 journal/STRATEGY_LOG.md | 20 Min | file-templates.md |
| 10 | G-13 breakfix CLAUDE.md-Regel-Learning | 15 Min | breakfix-template.md |
| 11 | G-10 Docs-Kategorien | 20 Min | file-templates.md |
| — | ~~G-11 integration-test Architektur-Karte~~ | ❌ ausgeschlossen | CLAW-spezifisch |
| — | ~~G-12 status Architektur-Radar~~ | ❌ ausgeschlossen | CLAW-spezifisch |

**Geschätzter Gesamtaufwand:** ~5 Stunden für Prio 1-11

---

## STATUS

- [x] G-01 Fehlende NIEMALS-Regeln
- [x] G-02 Proaktive Pflicht §2
- [x] G-03 Rollback-Pläne §6
- [x] G-04 settings.json Hook-Aktivierung
- [x] G-05 Agent Team Setup (via G-07 abgedeckt)
- [ ] G-06 governance-template.md Abschnitte
- [x] G-07 specs/TEMPLATE.md Sektionen
- [x] G-08 ARCHITECTURE_DESIGN.md Template
- [x] G-09 journal/STRATEGY_LOG.md
- [x] G-10 Docs-Kategorien
- ❌ G-11 integration-test Architektur-Karte — ausgeschlossen (CLAW-spezifisch)
- ❌ G-12 status Architektur-Radar — ausgeschlossen (CLAW-spezifisch)
- [x] G-13 breakfix CLAUDE.md-Regel-Learning
