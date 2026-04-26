# STPA resolution (within the defined execution boundary)

**Document role:** This file **closes** the STPA *working package* into a **defensible, scoped** “STPA complete” position: every hazard in `STPA_PACKAGE.md` is **Mitigated**, **Accepted** (with justification and residual risk), or **Assumption-bounded**; UCAs and interposition are resolved **within scope**; **out-of-scope** and **residual** items are **explicit**—not hidden.

**Defined boundary (unchanged):** `STPA_PACKAGE.md` §1 — consequential execution via `s4/atomic-execution` with PEV, coprocessor verdicts, and receipt before executor; **plus** the **coprocessor** path for `Effect::Allow` and **Ledger** write semantics.

**Normative for paper claims** referencing *STPA analysis complete within boundary*: this document.

---

## 1. Hazard closure summary

| ID | Hazard (short) | Resolution | Rationale and residual (if any) |
|----|----------------|------------|----------------------------------|
| **H1** | Execution without valid verdict bound to action | **Mitigated** | PEV + atomic `execute` order; `SC1`, `SC5`. **Residual:** *Assumption A_route* (gateway routes to PEV/execution)—misconfig **out of scope** of this case until conformance fails. |
| **H2** | Invalid / forged / expired authority treated as valid | **Mitigated** | Passport + optional authority chain in coprocessor + PEV live checks; `SC2` tests. **Residual:** *minimal* on designed path. |
| **H3** | Revocation not visible in time at boundary | **Accepted** (bounded) | PEV re-reads passport at execution; fail-closed on dep down. **Residual:** *distributed propagation window* (no worst-case time bound in repo)—**accepted** as *operational* risk; mitigated by *fail-closed on read failure* and *expiry* checks. **Not* claimed: zero window globally. |
| **H4** | Evidence not bound / durable before side effect | **Mitigated** | Receipt before `executor.execute`; `SC5`, `SC6` on path; `s4/atomic-execution` tests. |
| **H5** | Stale or incomplete process model | **Accepted** (partial) + **Mitigated** (core) | PEV + TTLs + `SC8` reduce drift; coprocessor cache has TTL. **Residual:** *policy/Deploy skew* and *cache* vs live state—**accepted** with **monitoring/ops**; not eliminated by proof in repo. |
| **H6** | Degraded control / Ledger / dependency leaves permit path | **Mitigated** (Allow+Ledger) **+ Accepted** (non-Allow edge) | **Allow** + Ledger write fail → `Deny` + `ledger_write_failed` (**closed**). PEV + deps **fail closed**. **Residual (accepted):** `Escalate` (or other **non-Allow**) may return with `ledger_write=false`—**out of safety envelope** for *Permit* claim; *documented* in `interposition_audit.md`. |
| **H7** | Strata disagree; pipeline not fail-safe | **Assumption + Accepted** | **A_strata:** upstream composition must not merge contradictory evidence into silent allow. **Partial** evidence in sibling repos. **Residual:** *full* H7 closure **out of monolith**—**out of scope** for this *ARE-only* case except as declared assumption. |

**Closure rule (STPA within boundary):** Every H1–H7 has exactly one of **Mitigated** | **Accepted (bounded)** | **Assumption** | **Out of scope (declared)**, with text above.

---

## 2. UCA resolution (representative; full list in `UCA_ENUMERATION.md`)

| UCA | Resolution | Mechanism + evidence | Residual |
|-----|------------|----------------------|----------|
| **UCA-P-L01** (Allow, no Ledger) | **Closed** | Deny on write fail; `parallel_evaluation_and_ledger_paths` | None *within* Allow path. |
| **UCA-B-01** (block not when required) | **Closed** (path) | Coprocessor + PEV + atomic; tests | *A_route* if network bypasses. |
| **UCA-P-01** (permit when unsafe) | **Closed** *conditional* + **Accepted** | PEV+passport; H3 path **Accepted** (lag) | Residual: tiny revocation window. |
| **UCA-P-05** (permit too long) | **Accepted** | TTL + re-validation at PEV | Residual: cache/clock skew. |
| **UCA-M-04** (modify too late) | **Closed** | Receipt before execute | None on golden path. |
| **UCA-P-L02** (non-Allow + Ledger fail) | **Accepted** | Documented; not part of *Permit* claim | Tracked; optional future tighten. |
| **UCA-R-0x** (revoke) | **Accepted** | PEV re-fetch; domain revoke flows | Distributed lag **out of zero-proof**. |
| **UCA-E-0x** (escalate) | **Accepted** / **out of scope** (human) | SC10, ops | **Residual:** human/HITL path. |
| **UCA-RC-0x** (recover) | **Assumption** / **out of scope** | Sibling *recovery* | Not closed in are-only scope. |

*Every other UCA row in `UCA_ENUMERATION.md` is classified **Mitigated**, **n/a (availability)** , **Accepted** (with residual in §5), or **Deferred** to product backlog—not left “unknown.”*

---

## 3. Assumptions (explicit and bounded)

| ID | Assumption | What it bounds | If false |
|----|------------|----------------|----------|
| **A_fb** | **Feedback (Ledger) within boundary:** all **consequential** executions in scope produce **durable** evidence on the **governed** path: (1) coprocessor **Allow** → successful **Ledger** write for that decision, or `ledger_write_failed` Deny; (2) atomic path → **receipt** + outcome update per engine. **Events** that never cross `atomic-execution` with registered executors are **out of scope** for *this* interposition claim. | “Sufficient” feedback to tie **Permit** and **execution** to **recorded** evidence for the **defined** path. | Safety case for **that** path fails; *not* a claim about every Kafka message. |
| **A_pass** | **Passport / registry** infrastructure used by PEV is the **intended** production dependency (correct URL, mTLS as deployed). | Identity reads at PEV. | **Fail-closed** on dep failure; **wrong** dep = **deployment bug**, *out of scope* of code proof. |
| **A_route** | **API gateway** (or client stack) routes execution traffic to real **PEV** and **coprocessor**, not a stub. | **H1** | Same as A_route above. |
| **A_ex** | **Executors** are only reachable through **atomic-execution** registration. | Un-governed **direct** call to a side effect. | **Out of scope** of repo if operators expose another path—**A_int** in interposition. |
| **A_strata** | **Strata** do not call ARE in a way that **masks** upstream disagreement (per integration contracts). | **H7** | Residual: see hazard table. |
| **A_dist** | **Partition / multi-region** Ledger is operated so **Append** visibility matches deployment SLO, or else **out of scope** for *this* case’s *distributed proof*. | `CS-N-*` in causal scenarios. | Stated as **residual** (§5), not *proven* HA here. |

---

## 4. Interposition (governed vs accepted ungoverned / out of scope)

**Statement (Option B, scoped):** Within the **defined execution boundary** (this document + `STPA_PACKAGE` §1):

- **Governed (verified in architecture):** `s4/atomic-execution` requires validator → receipt → executor; coprocessor **Allow** requires **Ledger** write or **Deny**.
- **Ungoverned paths known from audit** (`interposition_audit.md`, `tools/interposition_audit/audit.py` heuristics): e.g. **S2–S6** ad-hoc side channels, **Kafka** producers not behind ARE, **strata-execution** misconfiguration empty `ARE_AUTHORITY_URL`—are **not** in the *minimum execution proof* and are **explicitly out of scope** for *“no ungoverned path in the product”* **unless** a deployment **certification** (A_int) shows routing and network policy.

**Conclusion for STPA *within* boundary:** **No** *identified* path **inside** the **golden** coprocessor → PEV → atomic chain is left *unlabeled*: it is **governed** or an **A_route / A_ex** *deployment* obligation. *Global* product closure is **A_int** + ops, not this paper alone.

---

## 5. Residual risk statement (required)

**Residual risk** remains where hazards depend on:

- **Policy correctness** (the oracle—not proven safe by ARE).
- **Org / human** follow-through (escalation, HITL).
- **Infrastructure** not modeled as code (mTLS, network policy, *correct* registry endpoints).
- **Distributed consistency** beyond what PEV and coprocessor **fail closed** (e.g. *finite* but **unbounded in spec** revoke lag; multi-region **Ledger** read-your-writes).
- **Strata and recovery** behavior **outside** the are monolith.

**These are explicitly *out of scope* for *this* safety case** except where **A_***  above state what we **assume** for the **bounded** claim.

**Implication:** *STPA analysis is **complete** for the **stated** boundary and **resolution** table—not for the entire product surface area or the physical world.*

---

## 6. How this connects to the paper (one claim)

> The STPA analysis is **complete within the defined execution boundary** in `STPA_RESOLUTION.md`: each hazard (H1–H7) is **mitigated**, **accepted** with **documented** residual, or **assumption**-**bounded**; interposition and feedback are **declared** as in §3–4; **residual** risk in §5 is **explicit**.

*Reviewer instruction:* **Do not** read “complete” as “all UCAs closed with formal proof”—read as “**no orphan O/P without decision** in this file.”

*Version: 2026-04-26. Cross-ref:* `STPA_COMPLETENESS_STATUS.md` *(updated to reference this document as the closure record).*
