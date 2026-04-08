# OpenCLAW Bootstrap Skill

> A **portable Claude Code skill** that sets up a complete AI-driven development governance framework for any new project — in 5 guided phases, with no external dependencies.

**From:** [OpenCLAW Trading System](https://github.com/vibercoder79/openclaw_trading) — battle-tested in production since 2025.

---

## What This Skill Does

When you say `/bootstrap` to Claude Code, it guides you through setting up:

| What | Why |
|------|-----|
| **GOVERNANCE.md** | Blueprint for AI-driven dev lifecycle — rules, workflows, quality gates |
| **CLAUDE.md** | Identity & rules file for Claude as AI operator |
| **Self-Healing Agent** | Monitors doc versions + daemon health every 15 min (cron) |
| **Doc-Sync Module** | Keeps all docs at the same version, optionally mirrors to Obsidian |
| **Issue Writing Guidelines** | Structured story format for Claude + Human collaboration |
| **Skills installation** | Links ideation, implement, backlog, architecture-review and more |
| **Linear + GitHub + Obsidian** | Connects your toolchain into one coherent lifecycle |

---

## The Core Idea

```
Idea → /ideation → Linear Issue → /backlog → /implement → Code + Docs → Git Push → Done
```

Every change is:
1. **Authorized** by a Linear issue (no code without a ticket)
2. **Documented** in the same commit (no code without a doc update)
3. **Monitored** by the self-healing agent (version drift detected in 15 min)
4. **Reproducible** because every workflow is a skill

```mermaid
flowchart LR
    A["💡 Idea"] --> B["/ideation\nResearch + Story"]
    B --> C["📋 Linear Issue"]
    C --> D["/implement\n8-step SDLC"]
    D --> E{"🔒 spec-gate.sh\nSpec exists?"}
    E -->|"No Spec"| F["Create\nspecs/ISSUE-XX.md"]
    F --> E
    E -->|"Spec OK"| G{"🔒 doc-version-sync.sh\nVersions in sync?"}
    G -->|"Drift"| H["Sync all DOC_FILES"]
    H --> G
    G -->|"In Sync"| I["📦 git push\ncode + docs"]
    I --> J["✅ Done + Changelog"]
    K["🔄 Self-Healing\nevery 15 min"] -.-> J
```

---

## Installation

### On an existing Claude Code setup (same machine)

```bash
# Copy the bootstrap skill to your Claude Code skills directory
cp -r bootstrap/ /root/.claude/skills/bootstrap/

# Start Claude Code in any directory and say:
# /bootstrap
```

### On a fresh machine (portable mode)

```bash
# 1. Install Claude Code
# 2. Copy this folder to the skills directory
mkdir -p /root/.claude/skills/
cp -r bootstrap/ /root/.claude/skills/bootstrap/

# 3. Open Claude Code
claude

# 4. Say: /bootstrap
```

No dependencies on any other files. All templates are embedded in `references/`.

---

## What You Need Before Running

Claude will ask you for these during Phase 0:

**Required:**
- Project name & one-sentence description
- Absolute path to your project directory
- GitHub repository URL
- Linear team name (slug) + issue prefix (e.g. `PROJ-`)
- Start version (e.g. `1.0.0`)
- Obsidian Vault path (for doc sync)

**Optional:**
- Telegram Bot Token (for self-healing alerts)
- OpenRouter/Perplexity API key (for `/research` skill)
- Miro Board URL (for `/visualize` skill)

---

## File Structure

```
bootstrap/
├── SKILL.md                                    ← Skill definition (Claude reads this)
├── README.md                                   ← This file
├── docs/
│   └── diagrams/                               ← Visual diagrams (Mermaid in README + high-res exports)
│       ├── 01-development-lifecycle.excalidraw ← Source (open at excalidraw.com)
│       ├── 01-development-lifecycle.png        ← High-res PNG export
│       ├── 02-governance-enforcement.excalidraw
│       ├── 02-governance-enforcement.png
│       ├── 03-bootstrap-phases.excalidraw
│       └── 03-bootstrap-phases.png
└── references/
    ├── info-gathering.md                       ← Checklist of infos to collect
    ├── file-templates.md                       ← config.js, CLAUDE.md, etc. templates
    ├── governance-template.md                  ← Full GOVERNANCE.md (embedded, portable)
    ├── self-healing-template.js                ← Self-healing agent starter code
    ├── doc-sync-template.js                    ← Doc-sync module starter code
    ├── issue-writing-guidelines-template.md    ← Issue format guidelines
    ├── skills-setup.md                         ← Symlinks vs. copies, ordering
    └── global-registry-update.md              ← How to register project in CLAUDE.md
```

---

## The 5 Phases

| Phase | What happens | Human input needed? |
|-------|-------------|-------------------|
| **0 — Info Gathering** | Claude asks 14 questions | Yes — answer once |
| **1 — Foundation** | Creates dirs, git, all core files from embedded templates | Confirm .env done |
| **2 — Skills** | Installs/links ideation, implement, backlog, etc. | Choose skill tier |
| **3 — Self-Healing** | Writes + tests self-healing + doc-sync | None |
| **4 — Daemon** (optional) | Linear automation daemon skeleton | Yes if enabled |
| **5 — Registry** | Updates global CLAUDE.md + memory | None |

```mermaid
flowchart LR
    P0["Phase 0\n📋 Info-Gathering\n14 questions"] --> P1["Phase 1\n🏗️ Foundation\nFiles + Git + Hooks"]
    P1 --> P2["Phase 2\n🛠️ Skills\nideation + implement\n+ breakfix + ..."]
    P2 --> P3["Phase 3\n🔄 Self-Healing\nversion monitor + sync"]
    P3 --> P4["Phase 4\n⚡ Daemon\noptional"]
    P4 --> P5["Phase 5\n🌐 Registry\nGlobal CLAUDE.md"]
    P5 --> DONE["🚀 Ready for\n/ideation"]
```

---

## What Gets Created

After running `/bootstrap`, your project will have:

```
my-project/
├── lib/
│   ├── config.js          ← VERSION + DOC_FILES — single source of truth
│   └── doc-sync.js        ← Syncs versions to all docs + Obsidian
├── agents/
│   └── self-healing.js    ← Cron-ready health monitor (every 15 min)
├── CLAUDE.md              ← Claude's identity, capabilities, rules
├── SYSTEM_ARCHITECTURE.md ← Architecture doc (fill as system grows)
├── COMPONENT_INVENTORY.md ← File inventory (self-healing checks this)
├── DEVELOPMENT_PROCESS.md ← How to develop in this project
├── GOVERNANCE.md          ← The complete governance blueprint
├── SECURITY.md            ← API key policy, threat model
├── CHANGELOG.md           ← Auto-updated by doc-sync
├── .env                   ← Your API keys (gitignored)
├── .env.example           ← Variable names without values
└── .claude/
    ├── ISSUE_WRITING_GUIDELINES.md
    └── skills/
        ├── ideation/      → symlink or copy
        ├── implement/     → symlink or copy
        ├── backlog/       → symlink or copy
        └── ...
```

---

## The Governance Principles

Eight unbreakable rules Claude follows in this framework:

1. **Never implement without a Linear issue** — every change must be tracked
2. **Never close an issue without a changelog** — history must be complete
3. **Never change code without asking first** — human-in-the-loop for risk control
4. **Never claim done without a git push** — code must always be in remote
5. **Never shorten an operator briefing in Linear** — original text is truth
6. **Never create an issue without labels** — labels are essential for filtering
7. **Never move sub-tasks directly to Done** — always go through "In Progress" first
8. **Never add an API integration without updating the API inventory**

```mermaid
sequenceDiagram
    participant Dev as Claude Code
    participant SG as 🔒 spec-gate.sh
    participant DS as 🔒 doc-version-sync.sh
    participant GH as 📦 GitHub

    Dev->>SG: git commit attempt
    alt No Spec File found
        SG-->>Dev: BLOCKED — create specs/ISSUE-XX.md first
    else Spec File exists
        SG->>DS: ✅ Spec OK — check versions
        alt VERSION mismatch in DOC_FILES
            DS-->>Dev: BLOCKED — sync all docs to current VERSION
        else All DOC_FILES in sync
            DS->>GH: ✅ Commit + Push
            GH-->>Dev: Done — Issue can be closed
        end
    end
```

---

## The Self-Healing Mechanism

```
cron (every 15 min)
    └── node agents/self-healing.js
            ├── Check M: All DOC_FILES at same VERSION as config.js?
            │   → No: alert + auto-sync via doc-sync.js
            ├── Check U: All documented components exist on filesystem?
            │   → No: warn
            └── Check P: All daemon processes running (lock files)?
                → No: restart via start script + backoff
```

The version number in `config.js` is the **single source of truth**. When you bump it, self-healing will update all doc files automatically on the next cron run.

---

## Other Skills in the OpenCLAW Framework

This bootstrap skill sets up your project to use these skills:

| Skill | Trigger | Purpose |
|-------|---------|---------|
| `/ideation` | "I have an idea" | Research → Architecture Design → Linear Issue |
| `/implement` | "go", "start ISSUE-XX" | 8-step SDLC workflow with quality gates |
| `/backlog` | "what's next" | Sprint planning + dependency analysis |
| `/architecture-review` | "review architecture" | 8-dimension quality report |
| `/sprint-review` | "sprint review" | Quarterly audit + tech debt |
| `/research` | "research X" | 2-tier: WebSearch + Perplexity deep research |
| `/breakfix` | "system broken" | Incident response: Detect → Fix → Document |

All skills are from the same OpenCLAW framework and work together.

---

## Portability

This skill has **zero external dependencies**:

| Needed | Source |
|--------|--------|
| GOVERNANCE.md content | `references/governance-template.md` (embedded) |
| Self-healing script | `references/self-healing-template.js` (embedded) |
| Doc-sync script | `references/doc-sync-template.js` (embedded) |
| Issue guidelines | `references/issue-writing-guidelines-template.md` (embedded) |
| File templates | `references/file-templates.md` (embedded) |

Copy the `bootstrap/` folder anywhere → it works.

---

## Requirements

### Pflicht

| Was | Warum |
|-----|-------|
| **Claude Code** | claude.ai/claude-code — der AI-Operator |
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
> Wenn der Test fehlschlägt, hält Bootstrap an und zeigt Einrichtungsanleitung.

### Optional

| Was | Wofür |
|-----|-------|
| **Obsidian** | Doc-Sync in Vault |
| **Telegram Bot** | Self-Healing Alerts |
| **OpenRouter API Key** | `/research` Deep-Tier via Perplexity |
| **Hostinger API Key** | `/cloud-system-engineer` Skill |
| **Miro Access Token** | `/visualize` Skill |
| **notebooklm-py** CLI | `/notebooklm` Skill |

---

## License

MIT — use freely, adapt for your project.

Part of the **OpenCLAW Governance Framework**.
Source: [github.com/vibercoder79/openclaw_trading](https://github.com/vibercoder79/openclaw_trading)
