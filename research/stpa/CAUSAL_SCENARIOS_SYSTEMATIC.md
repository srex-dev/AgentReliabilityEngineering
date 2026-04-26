# Causal scenarios — systematic coverage (STPA)

**Purpose:** Checklist of scenario **classes** for coverage review: **T** timing, **D** distributed delay, **L** partial ledger, **E** escalation, **P** process drift, **N** partition (abstract), **V** inconsistent views, **S** strata. Status: **C**=evidence in repo, **P**=partial, **O**=open.

---

## T — Timing and ordering

| ID | Description | Chain | H / L | Evidence | Status |
|----|-------------|-------|------|------------|--------|
| CS-T-01 | PEV / decision TTL exceeded | stale `DecisionID` + request | H1, L1 | PEV `decision_expired` | **C** |
| CS-T-02 | PEV dependency slow or flapping | dependency timeout vs fail | H5, L5 | PEV `dependency_unavailable:*` | **C** |
| CS-T-03 | Atomic executor timeout | timeout outcome + receipt update | L4? | `executor_timeout` in engine | **C** |
| CS-T-04 | Passport revoked after coprocessor Allow, before PEV | race across services | H3, L3 | PEV re-fetches passport | **P** (no max propagation bound) |
| CS-T-05 | Coprocessor cache TTL vs live passport | cached Allow, expired passport at PEV | H5, L3 | PEV checks passport at execution | **C** (path); **P** (global proof) |

---

## D — Distributed delay / skew

| ID | Description | H / L | Status |
|----|-------------|--------|--------|
| CS-D-01 | Revocation visible at authority engine before at passport service | H3, L3 | **O** |
| CS-D-02 | Stratum service lag vs ARE clock | H7, L7 | **O** |
| CS-D-03 | Outbox / async write to ledger after response | H6, L6 | *Not* current Allow path; **O** for async designs |

---

## L — Partial Ledger (beyond Allow+fail)

| ID | Description | H / L | Status |
|----|-------------|--------|--------|
| CS-L-01 | **Allow** + Ledger write fail → Deny | H6, L1 | **C** (coprocessor) |
| CS-L-02 | **Escalate** + Ledger write fail | H6, L4 | **O** (documented) |
| CS-L-03 | Receipt path Ledger / append fail | H4, L4 | **C** / **P** (receipt+atomic) |

---

## E — Escalation

| ID | Description | H / L | Status |
|----|-------------|--------|--------|
| CS-E-01 | Should escalate, policy returns Deny | H7, L1 | **O** (policy) |
| CS-E-02 | Escalation workflow never completes | L1 | **O** (human path) |
| CS-E-03 | HITL signal lost | L1 | **O** |

---

## P — Process model drift (decision time vs execution time)

| ID | Description | H / L | Status |
|----|-------------|--------|--------|
| CS-P-01 | `decided_ts` vs `PEV` “now” window | H5, L5 | PEV + TTL: **C** (partial) |
| CS-P-02 | `policy_version` at coprocessor ≠ at policy bundle in prod | H5, L5 | **O** (deploy discipline) |
| CS-P-03 | Agent registry `ACTIVE` flips between coprocessor and PEV | H5, L1 | **P** |

---

## N — Partition (abstract)

| ID | Description | H / L | Status |
|----|-------------|--------|--------|
| CS-N-01 | PEV cannot reach passport / registry; fail-closed | L5, L1 | PEV: **C** (fail) |
| CS-N-02 | Network split: two writers to Ledger / inconsistent append | L6, L4 | **O** (HA semantics not in monolith scope) |
| CS-N-03 | “Split brain” on authority source | L3, L1 | **O** |

---

## V — Inconsistent views

| ID | Description | H / L | Status |
|----|-------------|--------|--------|
| CS-V-01 | Read-your-writes: decision visible to PEV after coprocessor | H1, L1 | *Assumption* read path: **A** |
| CS-V-02 | Stale `GetDecision` cache vs on-disk ledger | H5, L6 | **O** (cache layer) |

---

## S — Strata

| ID | Description | H / L | Status |
|----|-------------|--------|--------|
| CS-S-01 | Formation says “no” / execution still calls ARE | H7, L1 | **P** (integration) |
| CS-S-02 | Disagreeing metrics merged into one request | H7, L7 | **O** |
| CS-S-03 | Recovery re-opens execution without re-validation | L1, L4 | **P** (SC11) |

---

## Coverage note

A **strict** STPA exercise requires **all** relevant scenario classes in the defined boundary to be either **C** (with test IDs) or explicitly **A** (assumption) or **O** (tracked gap). This table is a **skeleton** for that closure, not a claim that every cell is **C**.

*See* `STPA_COMPLETENESS_STATUS.md` *for the meta-claim on STPA completion.*
