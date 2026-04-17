# 8 architecture dimensions — detail

## 1. Reliability
**Check when:** new agent, new daemon, new trade path, external API dependency.
- Graceful degradation: does the system keep running if this feature fails?
- Kill-switch: can the feature be disabled via config?
- Self-healing: does it need a new check?
- Restart behavior: flock-guarded? PID tracking? Backoff?

## 2. Data Integrity
**Check when:** write access to journal files, Brain DB, signal files, config.
- SSoT respected? Journal (JSONL) is authoritative, cache files are derived
- Dual-write: JSONL + Brain DB consistent?
- Atomic writes: write-then-rename for critical files?
- Race conditions: parallel agents writing the same file?

## 3. Security
**Check when:** new API, new webhook endpoint, external input, new .env variable.
- API keys only in .env, never in code or logs
- Input validation on webhooks (HMAC, replay protection, size limit)
- Token sanitization in error logs (no keys, tokens, session IDs)
- Rate limiting on incoming requests

## 4. Performance
**Check when:** new API with rate limits, WebSocket connection, memory-intensive ops.
- Rate limits: documented and honored? Buffer planned?
- Signal latency: fast tier (5 min), daemon (30 s) — does the feature fit?
- Memory: Node.js heap for long-running daemons (MAX_TRADES, buffer limits)
- WebSocket: reconnect logic? Heartbeat? Cleanup on shutdown?

## 5. Observability
**Check when:** any feature that could fail silently.
- Logging: sensible log levels? No raw API response logging (key leak)?
- Telegram alert: on errors that concern the operator?
- Dashboard: new API endpoint needed?
- Self-healing: new check needed?

## 6. Maintainability
**Check on:** every change.
- Code duplication: is there already a similar function?
- Config SSoT: all constants in config.js? No hardcoded values?
- Documentation: which docs need updating?
- Clarity: can the code be understood without extra context?

## 7. Cost Efficiency
**Check when:** new API with cost, LLM calls, new dependencies.
- API costs: free tier sufficient? Costs under production load?
- LLM tokens: is an LLM necessary or is rule-based enough?
- Alternatives: is there a free alternative?
- Daily limit: max calls per day defined?

## 8. Signal Quality
**Check when:** new signal agent, changed weighting, new data source.
- Trade improvement: does it measurably improve decision quality?
- Weighting: does the supervisor weight make sense? (AGENT_WEIGHTS sum = 1.0)
- Feedback loop: can attribution + learning agent measure the impact?
- Correlation: does the new agent correlate strongly with existing ones? (redundancy)
