# Architecture Dimensions — Detail (for /architecture-review)

Detail questions per dimension, with "check when" trigger conditions. Generically phrased — project-specific examples as optional hints.

**Structure:** 6 standard dimensions + 4 optional add-ons (activated in bootstrap Block A.7, visible in `ARCHITECTURE_DESIGN.md §3`).

## Standard dimensions

### 1. Reliability

**Check when:** New component, external API dependency, daemon / long-running process, critical business path.

- Graceful degradation: does the system continue if this feature fails?
- Kill switch / feature flag: can the feature be disabled via config without deployment?
- Retry strategy: transient errors retried with backoff?
- Timeout: every external call has a sensible timeout?
- Restart behavior (for daemons): locking (flock / PID file) against double-start? Backoff on crash loop?

### 2. Data Integrity

**Check when:** Write access to persistent data sources (DB, files, config), state that survives restart.

- SSoT respected? Is the authoritative data source clear and documented?
- Atomic writes where needed (write-then-rename, DB transactions)?
- Race conditions on parallel access considered?
- Idempotency on retries ensured (no duplicate side effect)?
- Backup / recovery path for critical data defined?

### 3. Security

**Check when:** New external API, new webhook endpoint, external input, new `.env` variable, change to auth logic.

- API keys / secrets only in `.env`, never in code, never in logs
- Input validation on all external entries (user input, webhooks, APIs)
- Token/key sanitization in error logs
- On inbound webhooks: HMAC signing, replay protection, rate limit, body size limit
- Principle of least privilege: tool/file access reduced to the minimum

### 4. Performance

**Check when:** New API with rate limits, long-running connection (WebSocket, SSE), memory-intensive ops, tight latency requirements.

- Latency budget for the use case known and tested
- Rate limits of external APIs documented and respected, buffer planned
- Memory usage: no unbounded buffering, cleanup strategy
- Long-running connections: reconnect logic, heartbeat, clean shutdown
- Caching where useful (semantic, TTL-based, cache invalidation defined)

### 5. Observability

**Check when:** Any feature that could silently fail, new external system, new daemon.

- Structured logging with sensible log levels
- No raw API response in logs (risk of key/token leak)
- Alerts on critical errors (Telegram / email / dashboard)
- Metrics for important state changes (counter, histogram)
- Self-healing check needed (if self-healing agent active)?

### 6. Maintainability

**Check on:** Every change.

- Code duplication: is there already a similar function that can be reused?
- Config SSoT: all relevant constants in `lib/config.js`? No hardcodes?
- Docs to update — which files (from `ARCHITECTURE_DESIGN.md §9`)?
- Understandable without extra context? Naming expressive? Comments at non-obvious spots?
- Tests for critical paths?

---

## Optional add-ons (when activated in bootstrap)

### 7. Privacy / GDPR

**Check when:** New external data transfer, personal data in flow, change to redaction pipeline.

- Data-flow boundaries explicit (Tier 0/1/2 or analog model)
- Before every cloud call: redaction of PII (emails, tokens, IBANs, phone numbers)
- Audit log on tier change / data transfer
- Offline fallback when privacy tier 0 is enforced
- Data subject rights: deletion / access implementable

### 8. Cost Efficiency

**Check when:** New API with costs, LLM calls, new SaaS dependency.

- API/token costs per call estimated; daily limit defined
- Free tier sufficient for production load?
- Cache strategy for repeated queries
- Alternatives (free, open source) evaluated
- Rate-limit budget realistically planned

### 9. Signal Quality

**Check when:** New signal/prediction agent, changed weighting, new data source, ML model change.

- Does the feature measurably improve decision quality?
- Evaluation metric defined (precision/recall/F1/custom)?
- Feedback loop present (attribution, active learning)?
- Correlation with existing signals (avoid redundancy / double counting)?
- Backtesting / validation strategy before production?

### 10. Compliance

**Check when:** Regulated industry, new external data processing, new stored PII category.

- Legal requirements identified (GDPR, HIPAA, SOX, etc.)?
- Audit trail for critical actions present?
- Data retention policy respected?
- Responsible role (DPO, compliance officer) involved?
- Documentation for auditors in `compliance/` up to date?

---

## Usage in `/architecture-review`

For every review:
1. Read active dimensions from `ARCHITECTURE_DESIGN.md §3 Quality Attributes`
2. For each active dimension: go through check questions, note status per area (OK / Warning / Critical)
3. Story-specific scope: only check dimensions touched by the change
4. System-wide scope (`/architecture-review --system`): go through all active dimensions

---

## Domain examples (reference only, not default)

The "check when" triggers above are generic. Concrete project expressions:

- **Voice assistant:** "flock", "daemon restart" → applies to wake-word listener. "Journal" → SQLite FTS5 memory.
- **Trading system:** "flock", "PID" → applies to agent daemons. "Journal" → JSONL + brain DB. Dual-write is mandatory.
- **Backend service:** "long-running connection" → WebSocket / SSE. "Rate limit" → upstream API quota.
- **Research project:** "reproducibility" → prompt versioning, seed fixing.

Project-specific details belong in the project's `ARCHITECTURE_DESIGN.md` + `SYSTEM_ARCHITECTURE.md` — not in this generic checklist.
