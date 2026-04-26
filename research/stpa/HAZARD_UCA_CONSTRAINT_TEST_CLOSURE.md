# Hazard → UCA → safety constraint → test (closure matrix)

**Purpose:** STPA **closure** requires that **each identified hazard** be linked to a **control** (UCA class mitigated), a **safety constraint**, and **evidence** (test, code, or explicit assumption). This matrix is the **backbone** for that argument; empty or *open* cells are **gaps** for strict reviewers.

**Legend:** H=hazard, L=loss, SC=constraint from `research/track-a-stamp/stpa/safety-constraints.md`. Status: **C**=closed in-repo, **P**=partial, **A**=assumption, **O**=open.

| Hazard | Representative UCAs (see `UCA_ENUMERATION.md`) | Safety constraints | Primary mechanism + path | Test / evidence | Closure |
|--------|-----------------------------------------------|--------------------|------------------------|-----------------|---------|
| H1 (no valid verdict) | UCA-B-01, UCA-P-03 | SC1, SC5 | PEV, atomic `execute` | `s4/atomic-execution` tests; PEV tests | **C** on designed path; **A** if gateway bypasses |
| H2 (bad authority) | UCA-P-01 | SC2 | Passport + authority + PEV | passport + PEV + authority engine tests | **C** |
| H3 (revocation lag) | UCA-P-05, UCA-R-01–04 | SC4, SC2 | PEV re-read; not single global instant | PEV; **no** end-to-end max-delay proof | **P** / **O** |
| H4 (evidence not bound) | UCA-M-04, UCA-P-03 | SC5, SC6 | receipt before executor; receipt path | `service_tests.rs` receipt before execute | **C** (atomic) |
| H5 (stale model) | UCA-M-01, UCA-B-05 | SC8 | PEV, TTL, cache | PEV decision expiry tests | **P** (coprocessor cache) |
| H6 (degraded control / Ledger) | UCA-P-L01, UCA-M-01, UCA-E + Ledger | SC7, SC6, SC8 | coprocessor Allow+Ledger; PEV fail-closed | `parallel_evaluation_and_ledger_paths`; PEV unavailability | **C** (Allow+Ledger); **O** (Escalate+Ledger) |
| H7 (strata disagree) | UCA-RC-*, UCA-E-02 | SC9, SC10, SC11 | architecture; partial hooks | docs + sibling tests | **P** / **A** |

## Coverage completeness (explicit)

| Question | Answer |
|----------|--------|
| Does every H1–H7 have at least one SC? | **Yes** in `safety-constraints.md` (SC1–SC11 map; see also traceability) |
| Does every row above have a test for **C** cells? | **Not** uniformly — *O/P/A* rows lack full proof |
| Is “no ungoverned path” **proven**? | **No** — `interposition_audit.md` is **audit**, not static **completeness** proof |
| Is every **O** / **P** / **A** **resolved**? | **Yes, within the defined boundary** — each hazard has a **decision** in `STPA_RESOLUTION.md` (mitigated, accepted with residual, or assumption). This matrix is the **structural** link; `STPA_RESOLUTION.md` is the **analytic** closure (not a machine proof). |
