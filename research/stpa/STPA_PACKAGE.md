# STPA package (canonical) — ARE execution boundary

**Purpose:** STAMP/STPA-defensible **bounded** safety case for **Agent Responsibility Engineering (ARE)** at the **execution boundary**, aligned to **code and tests in this repository**. Strata (sibling repos) are **environment / upstream process model**, not the authority plane.

**Supersedes / consolidates** narrative from `research/track-a-stamp/stpa/*.md` with this file as the **single** appendix-ready package.

**Non-claims:** Global multi-agent safety, policy correctness, safe **outcomes**, full distributed systems proof, completeness of all S2–S6 paths.

### STPA completeness (within the defined boundary)

**Normative closure document:** `STPA_RESOLUTION.md` — hazards H1–H7 **mitigated**, **accepted** (residual), or **assumption**-bounded; UCA and interposition **resolved**; **residual risk** explicit (§5).

| If the reviewer asks | Point them to |
|------------------------|----------------|
| “Is the STPA analysis *complete* in scope?” | **`STPA_RESOLUTION.md`** (§1–6) |
| “Exhaustive UCA set?” | `UCA_ENUMERATION.md` |
| “Hazard → constraint → test?” | `HAZARD_UCA_CONSTRAINT_TEST_CLOSURE.md` |
| “Causal / scenario classes?” | `CAUSAL_SCENARIOS_SYSTEMATIC.md` |
| “Boundary, partition, out-of-scope?” | `BOUNDARY_AND_ASSUMPTIONS_STPA.md` |
| “What did ‘working package’ mean historically?” | `STPA_COMPLETENESS_STATUS.md` |

**Accurate one-liner:** *STPA **complete** *within* the **defined** execution boundary, per `STPA_RESOLUTION.md`—**not** universe-wide product closure.*

---

## 1. System boundary (EXACT)

**Inside boundary (where ARE guarantees are argued in this repo):**

1. A **consequential action** is requested only through mechanisms that ultimately invoke **`s4/atomic-execution`**’s `execute` with a registered `ActionExecutor`.
2. **Verdicts** for “may this run” at execution time are formed by:
   - **`s1/policy-coprocessor-core`** (scope + authority + policy composition), and
   - **`s4/pre-execution-validator`** (binds `DecisionID` to current passport/agent/action class).
3. **Evidence** before side effect: **`s4/action-receipt-generator`** as called from atomic execution (receipt before `executor.execute` in `service.rs`).

**Outside / deployment assumptions:**
- **API gateway** routes execution traffic to real S4 services (not stubbed permissive handlers).
- **Executors** are not invocable except via atomic execution.
- **Strata** either call ARE correctly or are out of the proof.

If any assumption fails, the **bounded** guarantee fails regardless of in-repo logic.

---

## 2. Losses (formal classes)

| ID | Loss class | Description |
|----|------------|--------------|
| L1 | Unauthorized consequential action | Action executes that should be blocked. |
| L2 | PII / unauthorized data exposure | Exfiltration or access outside authority. |
| L3 | Revoked / expired / forged authority still executes | Stale or invalid grant used. |
| L4 | Execution without attributable evidence | No durable, bound audit trail. |
| L5 | Fail-open under uncertainty | Degraded dependency yields permit. |
| L6 | Policy / controller / Ledger mismatch | Inconsistent state across layers. |
| L7 | Strata disagreement collapses to execution | Contradictory upstream evidence still allows flow. |

---

## 3. Hazards (system states)

| ID | Hazardous state |
|----|-----------------|
| H1 | Execution possible without current affirmative **verdict** for **this** `action_id` / `decision_id` binding. |
| H2 | Passport or authority **invalid**, **expired**, **revoked**, or **forged**, but treated as valid. |
| H3 | Revocation or narrowing not visible at validation time. |
| H4 | Evidence not bound or not durable before **executor** runs. |
| H5 | Controller uses **stale** or **incomplete** process model. |
| H6 | **Ledger** or **dependency** failure still leaves a **permit-capable** path. |
| H7 | **Strata** disagree; pipeline does not **fail safe**. |

---

## 4. Control structure

| STAMP role | ARE instantiation |
|------------|-------------------|
| **Controller** | Policy coprocessor + pre-execution validator + atomic execution orchestration |
| **Controlled process** | Agent + tool/executor that performs world-facing side effects |
| **Control actions** | Permit (Allow), Deny, Escalate, Modify (effect / rate limit), Block at PEV, Revoke (passport/authority) |
| **Feedback** | Immutable ledger entries, decision cache, receipt records, agent/passport registries |
| **Process model** | Distributed: see `research/process_model.md` |
| **Environment** | API gateway, Postgres, OPA, Kafka (optional), **governance-strata** HTTP |

---

## 5. Unsafe control actions (UCA)

**Systematic, per–control-action enumeration:** `UCA_ENUMERATION.md` (Permit, Block, Modify, Revoke, Escalate, Recover × six STPA flaw types, with hazard links and open/partial/closed status).

**Summary table (illustrative only — the enumeration file is normative for review):**

| UCA (example) | Control | Flaw | ARE-relevant note | Code / status |
|---------------|---------|------|-------------------|---------------|
| U1 | Permit | When unsafe | Forged / bypassed path | PEV + tests on path; **A** if route wrong |
| U5 | Block | Not when required (route) | PEV bypass | **Deployment assumption** |
| U7 | Modify | Inappropriate | effect matrix | **P** |
| U8 | Revoke | Not when required (lag) | H3 | **O** / **P** |
| U12 | Ledger + Allow | fail-open | H6 | **C** (Deny) |
| UCA-E+Ledger | Escalate + write fail | evidence gap | H6 | **O** (documented) |

---

## 6. Causal scenarios (hazard → loss)

| Hazard | Failure chain | Control failure | Loss |
|--------|---------------|-----------------|------|
| H1 | Client skips PEV; stub gateway returns 200 | No validator call | L1 |
| H2 | Passport fake; registry not actually consulted | PEV `dependency_unavailable:passport` not distinct from block in all clients | L3 |
| H6 | (Historic) Allow + `ledger_written=false` | Mitigated: coprocessor now **Deny** on failed write for policy **Allow** | *If* clients ignored `Effect`, loss was possible — **reduced** |
| H4 | Receipt service down | Atomic returns `receipt_unavailable` (blocked) | *Avoids* L4 at execution **if** no bypass |
| H7 | Strata disagree; HTTP still calls ARE with merged payload | ARE unaware | L7 — **open** |

---

## 7. Safety constraints (enforceable)

| ID | Constraint |
|----|------------|
| SC1 | **No** `Success` execution at atomic boundary **without** PEV `Valid` for the request. |
| SC2 | **No** execution **without** receipt **generate** success before executor (or explicit blocked path). |
| SC3 | **No** PEV `Valid` with **expired** passport, **inactive** agent, or **action_class_unknown** (see `service.go`). |
| SC4 | **No** PEV `Valid` when `DecisionID` does not match `ActionID` / stale decision TS. |
| SC5 | Dependency **unavailable** at PEV → **not** `Valid` (fail-closed). |
| SC6 | **No** coprocessor **`Effect::Allow`** without successful Ledger write for that evaluation — on failure, response is **Deny** with `deny_reason: ledger_write_failed`. **Status: implemented** for the Allow case (see `s1/policy-coprocessor-core/src/service.rs`). *Separate obligation:* **Escalate** / other effects with `ledger_written=false`. |
| SC7 | Strata disagreement must not appear as “silent allow” in ARE — must surface as **deny/escalate** upstream. **Status: open / strata** |

*Detailed SC list also in* `research/track-a-stamp/stpa/safety-constraints.md` *— reconcile SC6 there with this file.*

---

## 8. Traceability matrix (abbreviated; full in `TRACEABILITY_TABLE` below)

**Status legend:** `implemented` | `partially implemented` | `deployment assumption` | `open proof obligation`

| Loss | Hazard | UCA / scenario | Safety constraint | ARE mechanism | Code | Test / evidence | Status |
|------|--------|----------------|------------------|---------------|------|-----------------|--------|
| L1 | H1 | Direct executor call | SC1, SC2 | Atomic execution | `s4/atomic-execution/src/service.rs` | `service_tests.rs` | **deployment assumption** (no other executor path) |
| L2 | H2 | Stale PII access | SC3, SC4, SC5 | PEV + passport | `s4/pre-execution-validator/.../service.go` | `go test ./...` | **implemented** (checks present) |
| L3 | H3 | Revoked passport | SC3 | Passport + PEV | `s0/passport-issuance-engine`, PEV | `TestVerifyPassportRevoked*`, PEV tests | **implemented** / **partial** (depends on all paths using PEV) |
| L4 | H4 | No receipt | SC2 | Receipt + atomic | `s4/action-receipt-generator`, `atomic-execution` | receipt-before-executor tests | **implemented** |
| L5 | H5, H6 | Fail-open | SC5, SC6 | PEV + coprocessor Allow+Ledger | PEV; `policy-coprocessor-core` | dependency + `parallel_evaluation_and_ledger_paths` | **implemented** for core Allow path |
| L6 | H6 | Ledger / Allow | SC6 | Coprocessor | `s1/policy-coprocessor-core/src/service.rs` | same | **implemented** (Allow → Deny on write fail) |
| L7 | H7 | Strata | SC7 | Strata + ARE | `governance-strata-execution`, `docs/governance-strata-integration.md` | integration tests in sibling | **open proof obligation** |

**Full working matrix (legacy):** `research/track-a-stamp/TRACEABILITY_MATRIX.md`

---

*Version: 2026-04-25. **Core claim (paper):** This package supports a **bounded** STPA-informed safety case at the ARE **execution** boundary, with explicit **gaps** (Ledger at coprocessor, deployment routing, strata) **not** hidden.*
