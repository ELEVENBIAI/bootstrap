# Architektur-Dimensionen — Detail (fuer /architecture-review)

Detail-Fragen fuer jede Dimension, mit "Pruefen wenn"-Triggerbedingungen. Generisch formuliert — projekt-spezifische Beispiele als optionale Hinweise.

**Struktur:** 6 Standard-Dimensionen + 4 Optional-Add-ons (aktiviert im Bootstrap Block A.7, sichtbar in `ARCHITECTURE_DESIGN.md §3`).

## Standard-Dimensionen

### 1. Reliability

**Pruefen wenn:** Neue Komponente, externe API-Abhaengigkeit, Daemon / Long-Running-Prozess, kritischer Geschaeftspfad.

- Graceful Degradation: Laeuft das System weiter wenn dieses Feature ausfaellt?
- Kill-Switch / Feature-Flag: Kann das Feature per Config deaktiviert werden ohne Deployment?
- Retry-Strategie: Transiente Fehler werden mit Backoff retried?
- Timeout: Jeder externe Call hat einen sinnvollen Timeout?
- Restart-Verhalten (bei Daemons): Locking (flock / PID-File) gegen Doppelstart? Backoff bei Crash-Loop?

### 2. Data Integrity

**Pruefen wenn:** Schreibzugriff auf persistente Datenquellen (DB, Files, Config), State der ueber Restart hinweg lebt.

- SSoT respektiert? Ist die authoritative Datenquelle klar und dokumentiert?
- Atomic Writes wo noetig (write-then-rename, DB-Transaktionen)?
- Race Conditions bei parallelen Zugriffen bedacht?
- Idempotenz bei Retries sichergestellt (kein doppelter Side-Effect)?
- Backup / Recovery-Pfad fuer kritische Daten definiert?

### 3. Security

**Pruefen wenn:** Neue externe API, neuer Webhook-Endpoint, externer Input, neue `.env`-Variable, Aenderung an Auth-Logik.

- API-Keys / Secrets nur in `.env`, nie im Code, nie in Logs
- Input-Validation bei allen externen Eingaengen (User-Input, Webhooks, APIs)
- Token-/Key-Sanitization in Error-Logs
- Bei eingehenden Webhooks: HMAC-Signing, Replay-Schutz, Rate-Limit, Body-Size-Limit
- Principle of Least Privilege: Tool-/File-Zugriffe auf das Noetigste reduziert

### 4. Performance

**Pruefen wenn:** Neue API mit Rate-Limits, Long-Running-Connection (WebSocket, SSE), Memory-intensive Ops, enge Latenz-Anforderungen.

- Latenz-Budget fuer den Use-Case bekannt und getestet
- Rate-Limits externer APIs dokumentiert und eingehalten, Buffer eingeplant
- Memory-Verbrauch: kein unbegrenztes Puffern, Cleanup-Strategie
- Long-Running-Connections: Reconnect-Logik, Heartbeat, sauberer Shutdown
- Caching wo sinnvoll (semantisch, TTL-basiert, Cache-Invalidation definiert)

### 5. Observability

**Pruefen wenn:** Jedes Feature das stumm fehlschlagen koennte, neues externes System, neuer Daemon.

- Strukturiertes Logging mit sinnvollen Log-Levels
- Kein Raw-API-Response in Logs (Gefahr von Key-/Token-Leak)
- Alerts bei kritischen Fehlern (Telegram / E-Mail / Dashboard)
- Metriken fuer wichtige State-Aenderungen (Counter, Histogram)
- Self-Healing-Check noetig (wenn Self-Healing-Agent aktiv)?

### 6. Maintainability

**Pruefen bei:** Jeder Aenderung.

- Code-Duplikation: Gibt es schon eine aehnliche Funktion, die wiederverwendet werden kann?
- Config-SSoT: Alle relevanten Konstanten in `lib/config.js`? Keine Hardcodes?
- Doku muss aktualisiert werden — welche Files (aus `ARCHITECTURE_DESIGN.md §9`)?
- Verstaendlichkeit: Versteht man den Code ohne Zusatzkontext? Naming aussagekraeftig? Kommentare bei nicht-offensichtlichen Stellen?
- Tests fuer die kritischen Pfade?

---

## Optional-Add-ons (wenn im Bootstrap aktiviert)

### 7. Privacy / DSGVO

**Pruefen wenn:** Neue externe Datenweitergabe, personenbezogene Daten im Flow, Aenderung an Redaktions-Pipeline.

- Datenflussgrenzen explizit (Tier 0/1/2 oder analoges Modell)
- Vor jedem Cloud-Call: Redaktion von PII (Emails, Tokens, IBANs, Telefonnummern)
- Audit-Log bei Tier-Wechsel / Datenweitergabe
- Offline-Fallback wenn Privacy-Tier 0 erzwungen
- Betroffenenrechte: Loeschung / Auskunft implementierbar

### 8. Cost Efficiency

**Pruefen wenn:** Neue API mit Kosten, LLM-Aufrufe, neue SaaS-Abhaengigkeit.

- API-/Token-Kosten pro Call abgeschaetzt; Daily-Limit definiert
- Free-Tier ausreichend fuer Produktionslast?
- Cache-Strategie fuer wiederholte Queries
- Alternativen (kostenlos, Open-Source) geprueft
- Rate-Limit-Budget realistisch geplant

### 9. Signal Quality

**Pruefen wenn:** Neuer Signal-/Prediction-Agent, geaenderte Gewichtung, neue Datenquelle, ML-Modell-Aenderung.

- Verbessert das Feature die Entscheidungsqualitaet messbar?
- Evaluation-Metrik definiert (Precision/Recall/F1/custom)?
- Feedback-Loop vorhanden (Attribution, Active Learning)?
- Korrelation mit bestehenden Signalen (Vermeidung von Redundanz / Double-Counting)?
- Backtesting / Validation-Strategie vor Production?

### 10. Compliance

**Pruefen wenn:** Regulierte Branche, neue externe Datenverarbeitung, neue gespeicherte PII-Kategorie.

- Gesetzliche Anforderungen identifiziert (DSGVO, HIPAA, SOX, etc.)?
- Audit-Trail fuer kritische Aktionen vorhanden?
- Data-Retention-Policy eingehalten?
- Verantwortliche Rolle (DPO, Compliance Officer) involviert?
- Dokumentation fuer Auditoren in `compliance/` aktuell?

---

## Verwendung im `/architecture-review`

Bei jedem Review:
1. Aktive Dimensionen aus `ARCHITECTURE_DESIGN.md §3 Quality Attributes` lesen
2. Fuer jede aktive Dimension: Pruef-Fragen durchgehen, Status je Bereich vermerken (OK / Warnung / Kritisch)
3. Story-spezifischer Scope: nur Dimensionen pruefen die durch die Aenderung beruehrt sind
4. System-weiter Scope (`/architecture-review --system`): alle aktiven Dimensionen durchgehen

---

## Domain-Beispiele (nur als Referenz, nicht Default)

Die obigen "Pruefen wenn"-Trigger sind generisch. Konkrete Projekt-Auspraegungen:

- **Voice-Assistant:** "flock", "Daemon-Restart" → gilt fuer den Wake-Word-Listener. "Journal" → SQLite FTS5 Memory.
- **Trading-System:** "flock", "PID" → gilt fuer Agent-Daemons. "Journal" → JSONL + Brain-DB. Dual-Write ist Pflicht.
- **Backend-Service:** "Long-Running-Connection" → WebSocket / SSE. "Rate-Limit" → Upstream-API-Quota.
- **Research-Projekt:** "Reproduzierbarkeit" → Prompt-Versioning, Seed-Fixierung.

Projekt-spezifische Details gehoeren in das jeweilige `ARCHITECTURE_DESIGN.md` + `SYSTEM_ARCHITECTURE.md` — nicht in diese generische Checkliste.
