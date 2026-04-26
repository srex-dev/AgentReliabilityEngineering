# Unsafe Control Actions (UCA) — systematic enumeration (ARE)

**Purpose:** STPA expects **systematic** enumeration of UCAs, not a short example list. This document enumerates UCAs by **control action** and **STPA flaw class** (Leveson): *provided when unsafe, not provided when required, too early, too late, applied too long, stopped too soon*.

**Scope:** Execution-boundary and coprocessor vocabulary: **Permit** = `Effect::Allow`; **Block** = deny path / PEV `Valid=false` / coprocessor `Deny` / atomic `Blocked`; **Modify** = effect resolution, rate limit, policy mutation of outcome; **Revoke** = authority/passport revocation (state outside a single request); **Escalate** = `Effect::Escalate`; **Recover** = governance-strata recovery (primarily out-of-repo).

**Status column:** *closed* = mitigated in-repo with cited tests or code; *partial* = some mitigation; *open* = proof obligation or deployment assumption; *n/a* = flaw class does not apply to the control in a meaningful way.

**IDs:** `UCA-<action>-<01–06>` by flaw class order.

---

## Flaw class reference

| # | Flaw (STPA) |
|---|----------------|
| 01 | Control action **provided** when it should not be (unsafe actuation) |
| 02 | **Not provided** when required (inadequate control) |
| 03 | **Too early** — before process model / preconditions are valid |
| 04 | **Too late** — after unsafe state is already inevitable |
| 05 | **Applied too long** — control persists after conditions changed |
| 06 | **Stopped too soon** — control withdrawn while still required |

---

## Permit (`Effect::Allow` from coprocessor, prior to bound execution)

| ID | Flaw | Hazard (primary) | Loss | Mitigation / constraint | Status |
|----|------|--------------------|------|------------------------|--------|
| UCA-P-01 | Provided when unsafe | H2, H3 | L1, L3 | SC2, SC4; PEV passport + decision binding | *partial* (revocation timing H3) |
| UCA-P-02 | Not provided when required | (ops) | — | Product/availability; not primary STPA loss | *n/a* (as safety loss) |
| UCA-P-03 | Too early | H1, H5 | L1, L4 | PEV re-validates; decision TTL | *closed* on path |
| UCA-P-04 | Too late | H1 | L1 | Pre-exec order in atomic engine | *closed* on path |
| UCA-P-05 | Too long (stale permit) | H3, H5 | L3 | PEV + passport expiry; cache TTL | *partial* |
| UCA-P-06 | Stopped too soon | — | — | Uncommon for Permit; rate limit may truncate allow | *open* (rate-limit semantics) |

**Ledger coupling (additional Permit UCAs for evidence path):**

| ID | Flaw | Hazard | Mitigation | Status |
|----|------|--------|------------|--------|
| UCA-P-L01 | Allow without durable coprocessor Ledger | H6, L4 | Deny on failed write; SC7 | *closed* (Allow+Ledger) |
| UCA-P-L02 | Non-Allow effect + failed Ledger (e.g. Escalate) | H6 | document / tighten | *open* |

---

## Block (deny, PEV block, execution blocked)

| ID | Flaw | Hazard | Loss | Mitigation | Status |
|----|------|--------|------|------------|--------|
| UCA-B-01 | Block not issued when required (permit when must deny) | H1, H2, H5 | L1, L2 | PEV + coprocessor | *closed* (design path) |
| UCA-B-02 | Block when should permit | (availability) | — | Runbooks; not L1 if safe default deny | *n/a* / ops |
| UCA-B-03 | Block too early | — | false deny | policy tuning | *open* (policy) |
| UCA-B-04 | Block too late | H1 | L1 | order of checks | *partial* |
| UCA-B-05 | Block persists too long | H5 | L5 | cache TTL, redeploy | *partial* |
| UCA-B-06 | Block stops too soon | H2 | L3 | re-validation on next action | *partial* |

---

## Modify (rate limit, effect resolution, OPA/scope/authority merge)

| ID | Flaw | Hazard | Loss | Mitigation | Status |
|----|------|--------|------|------------|--------|
| UCA-M-01 | Wrong modification (unsafe effect) | H5, H6 | L5, L6 | `resolve_effect`, tests | *partial* |
| UCA-M-02 | No modification when required | H6 | L5 | circuit breakers | *partial* |
| UCA-M-03 | Modify before inputs stable | H5 | L5 | parallel join + timeouts | *partial* |
| UCA-M-04 | Modify after side effect (too late) | H4 | L4 | receipt order | *closed* (atomic path) |
| UCA-M-05 | Rate limit / effect persists inappropriately | H5 | L5 | TTL, cache | *open* |
| UCA-M-06 | Stopped too soon (e.g. rate check skipped) | H6 | L5 | optional rate limiter | *open* |

---

## Revoke (passport/authority off-box or async)

| ID | Flaw | Hazard | Loss | Mitigation | Status |
|----|------|--------|------|------------|--------|
| UCA-R-01 | Revoke not applied when required | H3 | L3 | PEV re-fetch passport | *partial* (lag) |
| UCA-R-02 | Revoke when should not | — | availability | admin process | *open* |
| UCA-R-03 | Revoke “too early” / ordering | H3 | L3 | registry ordering | *open* |
| UCA-R-04 | Revoke too late | H3 | L3 | propagation delay | *open* (dist.) |
| UCA-R-05 | Stale revoked still readable | H3, H5 | L3 | TTL + live read | *partial* |
| UCA-R-06 | Revocation stopped too soon | H3 | L3 | — | *open* |

---

## Escalate (`Effect::Escalate`)

| ID | Flaw | Hazard | Loss | Mitigation | Status |
|----|------|--------|------|------------|--------|
| UCA-E-01 | Escalate when should Permit/Deny | H5 | L5 | policy | *open* |
| UCA-E-02 | No escalate when required | H7 | L7, L1 | HITL / workflow | *open* |
| UCA-E-03 / E-04 / E-05 / E-06 | timing / duration | H4, H6 | L4, L6 | human process | *open* |

**Ledger + Escalate:** failure to append may still return Escalate with `ledger_written=false` — **documented** as separate obligation in `STPA_COMPLETENESS_STATUS.md` and `interposition_audit.md`.

---

## Recover (strata / recovery; primarily outside ARE monolith)

| ID | Flaw | Hazard | Status |
|----|------|--------|--------|
| UCA-RC-01 … RC-06 | all six | H7, H1, H4 | *open* — sibling repo + assumptions |

---

## Cross-reference

Full **hazard → UCA → constraint → test** grid: `HAZARD_UCA_CONSTRAINT_TEST_CLOSURE.md`.

*This table is the enumeration backbone; “closure” for strict STPA requires every *open* row to be either remediated, accepted as out-of-scope for this safety case, or listed in `BOUNDARY_AND_ASSUMPTIONS_STPA.md`.*
