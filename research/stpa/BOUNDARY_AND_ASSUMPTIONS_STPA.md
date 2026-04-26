# System boundary, out-of-scope hazards, distributed assumptions (STPA)

**Purpose:** A **strict** STPA safety case must make the **operational** boundary **enforceable** and list **hazard classes** that are **not** mitigated. This document is the explicit **assumptions and exclusions** list.

## 1. Operational boundary (enforceable in principle)

| Boundary fact | How enforced (when deployment correct) | If violated |
|---------------|------------------------------------------|------------|
| Consequential execution only via `atomic-execution` with registered executors | Code order (validator → receipt → executor) | **Out of scope** of this case — *ungoverned executors* |
| Policy **Allow** requires coprocessor Ledger write | `Effect::Deny` + `ledger_write_failed` | **Mitigated in code** for this path |
| PEV is on path to execution | Architecture + integration | **Assumption** on routing |
| Gateway speaks to real S4 / S1 backends | **Assumption** | Misconfiguration = **out of scope** until detected |

**Enforceable** here means: *there exists a testable, deployable configuration where the property holds* — not that every possible deployment is safe.

## 2. Out-of-scope hazard classes (not addressed by this case)

| Class | Rationale | Reviewer phrasing |
|------|-----------|---------------------|
| **Physical-world harm** from correct authorized action | Policy/oracle; outcome, not just action | *Out of scope* |
| **Organizational** misuse of Escalation / HITL | Human process | *Out of scope* |
| **Global** multi-party strategic behavior | Not modeled | *Out of scope* |
| **Data plane** PII exfiltrated by a **non-ARE** service | S2–S6 not fully in boundary | *Partial* / *out of scope* per deployment |
| **Byzantine** compromise of HSM, DB root, or **all** replicas | TCB break | *Out of scope* (trust anchor assumed) |
| **Complete** proof of *no* alternate RPC path in every cluster | Network policy | *Open* / *deployment certification* |

## 3. Distributed and timing assumptions (failure consequences)

| Assumption | If false | Consequence (honest) | Addressed? |
|------------|----------|----------------------|------------|
| Passport/registry reads are **eventually** consistent and **fresh enough** for PEV | H3, L3: revoke after read | PEV re-read reduces window; **no** worst-case time bound in repo | **O** |
| **Ledger** is **append-only** and **durably** committed before coprocessor returns **Allow** | L6, L4 | **Mitigated** for Allow: deny if write not committed | **C** (Allow) |
| Multi-region or **partitioned** Ledger | CS-N-02: inconsistent read | **Not** proven in this monolith | **O** |
| **Strata** agree before request hits ARE | H7, L7 | **Strata** must fail upstream; not proven end-to-end | **P** / **O** |
| **Max delay** for revocation to PEV’s passport read | L3 | **O** (no number in spec) | **O** |

## 4. Feedback completeness (Ledger and registries)

**Question:** *Does the controller get enough feedback to keep the process model valid?*

| Feedback channel | What it does **not** guarantee | Status |
|------------------|--------------------------------|--------|
| Coprocessor → Ledger (decision write) | **All** other state transitions in the org | *Partial* view |
| PEV re-fetch at execution | Catches some drift; **not** a proof of global freshness | *Partial* |
| Strata + formation | Constrain inputs; not total model | *Partial* |

**Conclusion (for reviewers):** Feedback is **strong** for the **stitched** execution path; **completeness** of feedback for *all* hazards in all subsystems is **not** a proven property.

## 5. “Closed” claim — what is **not** being said

- **Not** saying: *every* STPA UCA is closed with machine-checked proof.  
- **Saying:** Boundary, **enumerated** UCAs (`UCA_ENUMERATION.md`), **closure** matrix (`HAZARD_UCA_CONSTRAINT_TEST_CLOSURE.md`), and **assumptions** (this file) are the **working** set toward a **stricter** STPA if the program requires it.

*Align with* `STPA_COMPLETENESS_STATUS.md` *and the paper* `paper/STAMP_ARE_FINAL.md` *on wording.*
