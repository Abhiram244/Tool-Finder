# Backend Critique (Brutal, Specific, Actionable)

This backend works as a prototype, but from an engineering perspective it is fragile, insecure in key places, and difficult to scale or maintain. The current implementation is tightly coupled, under-tested, and leaves too much correctness to luck.

## 1) Architecture: Everything Is Smeared Together

- **`server.js` is doing too much**: security config, session setup, static serving, route wiring, page routing, error handling, and startup logic all in one file.
- There is **no service layer**. Route handlers call DB and external API code directly, so business logic is duplicated and hard to test.
- `routes/search.js` mixes HTTP concerns, prompt building, network calls, parsing strategy, and fallback logic in one module.

**Impact:** Hard to reason about, hard to test in isolation, high risk for regressions.

**Fix direction:** Split into modules:
- `app.js` (express setup only)
- `server.js` (startup only)
- `controllers/`, `services/`, `repositories/`, `clients/geminiClient.js`
- shared `errors/` and `validation/`

---

## 2) Security Posture Is Not Production-Grade

- `express-session` uses the **default memory store**, which is explicitly not for production.
- Session secret has a weak fallback (`tool-finder-secret-key-change-in-production`) that could accidentally ship.
- Cookies are only `secure` in production, but **`sameSite` is not set**, leaving CSRF surface larger than needed.
- CSP is disabled globally (`contentSecurityPolicy: false`) because of inline scripts. That's a major downgrade.
- API keys are stored **in plaintext** in `users.gemini_api_key`.

**Impact:** Session reliability and security degrade quickly in real deployments; secrets are exposed if DB leaks.

**Fix direction:**
- move sessions to Redis (or another external store)
- require `SESSION_SECRET` at startup; fail fast if missing
- set cookie `sameSite` explicitly (`lax` or `strict`)
- replace inline scripts and re-enable CSP
- encrypt API keys at rest (KMS/crypto envelope pattern)

---

## 3) Data Layer Quality Is Weak

- The DB wrapper is hand-rolled and repetitive with many `new Promise` wrappers, manual `prepare/finalize`, and inconsistent connection checks.
- No migration system; schema creation is embedded in runtime code.
- Search payloads and recommendations are dumped as JSON blobs in SQLite text columns with little structural validation.
- `getUserSearches(userId, limit)` accepts arbitrary `limit` from query with no cap, enabling expensive reads.
- Stats endpoint reads up to 1000 rows and computes week/month counts in JS every request.

**Impact:** Data integrity and query efficiency will degrade; schema evolution will be painful.

**Fix direction:**
- adopt a query layer/migrations (Knex/Prisma/Drizzle or at least SQL migration files)
- enforce max limits and pagination strategy
- compute time-window stats in SQL
- add indexes (`searches(user_id, created_at)`, `user_stats(user_id)` unique)

---

## 4) Error Handling Is Primitive and Leaky

- Error handling is ad hoc; each route does its own `try/catch`, with inconsistent status codes and message quality.
- Global error middleware logs `err.stack` directly and returns generic JSON, but it is not integrated with structured logging or correlation IDs.
- In Gemini parsing, invalid model output silently degrades to empty recommendations instead of a robust retry/repair strategy.

**Impact:** Debugging incidents becomes painful; users get inconsistent failures.

**Fix direction:**
- centralized error classes (validation/auth/upstream/database)
- structured logger (pino/winston) with request IDs
- consistent error response contract
- upstream retry/backoff and parse-repair flow

---

## 5) Input Validation Is Incomplete and Sometimes Misleading

- Validation exists, but it is shallow for complex payloads (`queryData` just checked as object).
- `apiKey` validation only checks min length 10, not format or whitespace handling.
- Route params/query values are weakly validated (`limit` can be huge, negative, NaN-ish edge cases).
- Username normalization and password policy are simplistic and may conflict with UX/security expectations.

**Impact:** Garbage data enters the system; unexpected edge-case bugs persist.

**Fix direction:**
- schema-driven validation (Zod/Joi)
- normalize and sanitize input consistently
- enforce bounded numeric inputs with hard caps
- define and version API contracts

---

## 6) External API Integration Is Brittle

- Gemini endpoint is hard-coded to `gemini-2.0-flash-exp`, which is an unstable/experimental naming convention.
- No timeout/abort controller for outgoing fetch.
- No retry policy, no circuit breaker, and no quota handling strategy.
- The prompt assumes the model will return strict JSON; parser strips code fences and hopes for the best.

**Impact:** Reliability under real network/API volatility is poor.

**Fix direction:**
- versioned model config via env
- explicit timeout + retries with jitter
- validate model output against schema and reject invalid payloads
- add telemetry around latency/error classes

---

## 7) Auth Design Is Bare Minimum

- Session-only auth may be fine, but there is no CSRF mitigation for state-changing routes.
- Duplicate logout routes (`/logout` in server + `/api/auth/logout`) increase maintenance confusion.
- `isAuthenticated` middleware returns boolean instead of Express middleware behavior; it's not idiomatic and easy to misuse.

**Impact:** Increased attack surface and maintenance confusion.

**Fix direction:**
- CSRF tokens for browser session routes
- single canonical logout endpoint
- clean auth middleware API

---

## 8) Operational Readiness Is Low

- No health/readiness endpoints.
- No graceful shutdown logic for server + DB in long-running environments.
- No config validation at startup (missing envs are tolerated until runtime failures).
- `npm test` intentionally fails; there are zero meaningful automated checks.

**Impact:** Poor deploy confidence, weak incident response, and fragile runtime behavior.

**Fix direction:**
- `/health` and `/ready` endpoints
- SIGTERM/SIGINT handling with close hooks
- env schema validation at boot
- add unit/integration tests and CI pipeline

---

## 9) Performance and Scalability Constraints

- SQLite + in-process sessions + memory store means this backend is effectively single-node and low-concurrency.
- Heavy stringified JSON storage prevents efficient analytics/search over recommendations.
- Global rate limit is coarse and not scoped per sensitive route.

**Impact:** You hit ceilings fast as usage grows.

**Fix direction:**
- introduce external session store
- tune route-specific rate limits (auth/recommend stricter)
- plan DB evolution path (Postgres)

---

## 10) Code Hygiene and Maintainability Gaps

- Mixed concerns and long files (especially `routes/search.js`, `config/database.js`) reduce readability.
- Inconsistent response shapes (`error`, `success`, differing payload structures).
- Comments like “same as original” imply copy-paste debt rather than modular ownership.

**Impact:** New contributors will ship bugs faster than features.

**Fix direction:**
- enforce lint + formatting + complexity limits
- shared response helpers
- refactor by bounded context and add module-level tests

---

# Priority Fix Plan (Do This in Order)

1. **Security baseline:** external session store, cookie hardening, mandatory secrets, CSP re-enable plan.
2. **Stability baseline:** timeout/retry/schema validation for Gemini client.
3. **Data baseline:** add migrations and SQL indexes; cap pagination.
4. **Maintainability baseline:** controller/service/repository split.
5. **Reliability baseline:** tests (auth/search/user/db) + CI + health endpoints.

If this were a production review, this backend would be classified as **prototype-grade only** and blocked from high-trust deployment until the first three priority items are complete.
