# 8 architecture dimensions

For every story, check the relevant dimensions. Not all of them always apply — only apply those that are relevant to the concrete change.

## 1. Reliability
- Can the feature fail without blocking the system?
- Graceful degradation implemented?
- Self-healing check needed?
- Kill-switch present for new features?

## 2. Data Integrity
- SSoT (authoritative file/table) correctly described?
- Dual-write (JSONL + Brain DB) accounted for?
- Race conditions with parallel agents?
- Atomic writes where needed?

## 3. Security
- API keys in .env, not in code?
- Inputs validated (webhooks, external APIs)?
- Tokens sanitized in logs?
- Webhook signing where relevant?

## 4. Performance
- Signal latency acceptable? (Fast: 5 min, Daemon: 30 s)
- Rate limits honored and documented?
- Memory consumption within limits? (Node.js heap)
- WebSocket stability for daemons?

## 5. Observability
- Logging implemented?
- Alert on error?
- Dashboard integration needed?
- Self-healing check needed?

## 6. Maintainability
- Code duplication avoided?
- Config in SSoT?
- Docs need updating?
- Understandable without extra context?

## 7. Cost Efficiency
- API costs calculated?
- LLM token consumption considered?
- Free alternative available?
- Rate-limit budget not exceeded?

## 8. Signal Quality
- Does it improve decision quality?
- Supervisor weighting sensible?
- Feedback loop present (attribution, learning)?
- Contrarian vs. consensus logic clear?
