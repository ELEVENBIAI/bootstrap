# Change checklist

MANDATORY on every code change, no matter how small.

## 1. Update documentation — layer impact map

**Step:** Which layers/components were changed? → Review the corresponding docs.

| Changed area | Docs ALWAYS to check |
|--------------|----------------------|
| **L1 Signal Agent** (new/changed/removed) | `SIGNAL_SOURCES_INVENTORY.md`, `COMPONENT_INVENTORY.md`, `CLAUDE.md §9`, `SYSTEM_ARCHITECTURE.md` agent table |
| **L2 Scoring/Supervisor** (weights, scoring logic, veto) | `docs/SUPERVISOR_DECISION_LOGIC.md`, `TRADING_RULES.md`, `CLAUDE.md §7` |
| **L3 LLM/Arbiter** (debate, risk manager, LLM calls) | `docs/SUPERVISOR_DECISION_LOGIC.md`, `CLAUDE.md §3` |
| **L4 Execution** (trader.js, adapters, exec-router, pool) | `ARCHITECTURE_DESIGN.md §9`, `COMPONENT_INVENTORY.md` exchange adapters, `SECURITY.md §10.3`, `docs/TRADE_LIFECYCLE.md` |
| **L5 Monitoring/Self-Healing** (new check, threshold) | `CLAUDE.md §13`, `SYSTEM_ARCHITECTURE.md` self-healing table |
| **L6 Brain DB** (new table, schema version, writer) | Special checklist below: "Change Brain DB" |
| **L7 Presentation** (dashboard, Telegram, briefing) | `TELEGRAM_REPORTS.md`, `SYSTEM-DOKUMENTATION.md §Dashboard` |
| **Config / kill switch** | `docs/KILL_SWITCHES.md`, `CLAUDE.md §6` |
| **New ADR** | `ARCHITECTURE_DESIGN.md §3`, `INDEX.md`, consider guard-story candidate |
| **Exchange onboarding** | `docs/EXCHANGE-ONBOARDING.md`, `SECURITY.md §3.3 + §5.1 + §10.3`, `COMPONENT_INVENTORY.md` |
| **New API integration** | `API_INVENTORY.md`, `SECURITY.md §3.2 checklist`, special checklist below: "New API" |
| **New external data source** | `SIGNAL_SOURCES_INVENTORY.md`, `API_INVENTORY.md`, ADR-20 classification (composite vs. standalone) |
| **Mirror / account pool** | `docs/MIRROR-ACCOUNT-ONBOARDING.md`, `ARCHITECTURE_DESIGN.md §9`, `SECURITY.md §10.3` |
| **Governance / skill** | `GOVERNANCE.md`, affected `SKILL.md`, `RUNBOOK.md` (special below) |

**Always:**
- Bring every changed doc to the current `VERSION` in config.js
- Add a `CHANGELOG.md` entry with version + description
- Update `CLAUDE.md` if system behavior changes (new paths, new kill switches, new thresholds)

---

## 2. Git commit + push

- Commit both code AND doc changes
- `commitAndPush('T{N}: CLAW-XXX — [title]')`

---

## 3. Obsidian change log

- `linear.writeChangeLog()` with version + description

---

## Special checklists

### Add/remove agent:
- [ ] config.js → `AGENT_REGISTRY` (single SSoT — weights/signalFiles/daemon restart are derived)
- [ ] run-parallel.sh → tier list (FAST/MEDIUM/SLOW) if not via AGENT_REGISTRY
- [ ] CLAUDE.md → agent table §9
- [ ] Create signal file initially
- [ ] SIGNAL_SOURCES_INVENTORY.md entry
- [ ] COMPONENT_INVENTORY.md entry

### Change weights:
- [ ] Sum of all AGENT_WEIGHTS = 1.00 (exactly)
- [ ] Delete/reset optimized-weights.json
- [ ] Check weight-optimizer constraints
- [ ] Check ADR-20 classification (composite vs. standalone, no double-counting)

### Change trade logic:
- [ ] config.js thresholds (TEST_MODE vs PRODUCTION)
- [ ] trader.js imports from config.js (no hardcodes)
- [ ] SL/TP ranges in config.js
- [ ] Update **TRADE_FLOW.md** (end-to-end SSoT flow) on changes to:
  - Score thresholds (BUY_SCORE, SELL_SCORE), sizing tiers, vola adjust, LLM gates, scale-in, safety checks
  - `lib/config.js` RULES / TRADING.sizingTiers / volaAdaptiveSizing / scaleInProfitPct
  - `capital/trader.js` openTrade(), calcPositionSizePct(), applyVolaAdjust()
- [ ] Update docs/TRADE_LIFECYCLE.md (SL/TP, BE, trailing, multi-TP, sizing table)
- [ ] Update TRADING_RULES.md
- [ ] Update CLAUDE.md §6 rulebook

### Change exchange layer (trader.js, adapters, exec-router, account-pool):
- [ ] Apply ADR-13? No exchange-API call outside the adapter (self-healing L11 checks)
- [ ] Apply ADR-17? All dynamics run through the account pool manager (never directly via adapter)
- [ ] Update ARCHITECTURE_DESIGN.md §9 (diagram, checklist, exchange comparison)
- [ ] Update COMPONENT_INVENTORY.md exchange adapters table
- [ ] Update SECURITY.md §10.3 exchange failover table
- [ ] Update docs/TRADE_LIFECYCLE.md when new dynamics (BE, trailing, multi-TP, etc.) are added
- [ ] For a new exchange type: follow `docs/EXCHANGE-ONBOARDING.md` runbook

### Integrate a new API:
- [ ] Implement rate limiting
- [ ] **Create secret:** `echo -n "key" > {PROJECT_PATH}/secrets/<name>.txt && chmod 600`
- [ ] **docker-compose.yml:** add secret in `secrets:` block (top: service ref + bottom: file path)
- [ ] **config.js:** use `readSecret('<name>')` (NOT `env.KEY` or `process.env.KEY`)
- [ ] **Update encrypted backup:** `sops encrypt --input-type dotenv --output-type yaml .env > secrets/prod.enc.yaml`
- [ ] Error logging: sanitize keys (no secrets in logs/Telegram) — `lib/logger.js sanitize()`
- [ ] Set timeout (max 15s trading-critical, 60s research)
- [ ] Fallback on API outage (graceful degradation, ADR-10)
- [ ] Update API_INVENTORY.md
- [ ] Update SECURITY.md §3.3 (new credentials), §4.2 (input validation), §5.1 (auth)

### Add new ADR:
- [ ] ARCHITECTURE_DESIGN.md §3 new ADR block
- [ ] Check INDEX.md reference
- [ ] Ask the enforcement question: "Is this decision machine-enforced or only documented?"
  - Only documented → consider guard-story candidate (self-healing check? commit hook?)
- [ ] CHANGELOG.md entry

### Change Brain DB (new table, new writer, new reader):
- [ ] Extend writer table in SYSTEM_ARCHITECTURE.md §9.2.1
- [ ] Extend reader table in SYSTEM_ARCHITECTURE.md §9.2.2
- [ ] Check/update impact matrix in SYSTEM_ARCHITECTURE.md §9.2.3
- [ ] For a new table: migration in `claw-db.js` (next schema version)
- [ ] For a new table: extend `getHealth()` tables array
- [ ] For a new table: extend `DOCUMENTED_TABLES` in `self-healing.js`
- [ ] For a new writer: dedup strategy (INSERT OR IGNORE / UNIQUE INDEX)
- [ ] Update schema version in SYSTEM_ARCHITECTURE.md §9.2

### Change security feature:
- [ ] Update relevant section of SECURITY.md (§2 threat model, §4 input validation, §5 auth, §10 trading-specific)
- [ ] Check the threat-response matrix in §2.2 — is an existing threat mitigated?
- [ ] Update audit status in §2.3
- [ ] Self-healing check AA (security events) — register new event type?
- [ ] Extend security-events.js on new security events
- [ ] For new inbound webhook: HMAC-SHA256 mandatory, replay protection, rate limit, body limit

### Change governance, skills, or checklists:
- [ ] Update RUNBOOK.md (sync affected section)
- [ ] Affects: GOVERNANCE.md, .claude/skills/*/SKILL.md, .claude/skills/*/references/*.md
- [ ] The runbook contains the complete skill definitions + reference content — on changes, pull the corresponding sections across into the runbook

### Crossing system boundaries (OpenClaw ↔ trading system):
- [ ] Update INTEGRATION_MAP.md (data flow, files, frequency, kill switch)
- [ ] Provide kill switch in config.js
- [ ] Update AGENTS.md if OpenClaw gets new tools/commands
- [ ] Ensure idempotent + graceful degradation
