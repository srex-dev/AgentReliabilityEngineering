# Evidence — public one-page summary (Level 2 attestations)

**Purpose:** Give chairs and readers a **high-level view** without opening the **Level 3** frozen packet (raw logs, full gate matrices). This file is **not** a substitute for the bundle when a reviewer is checking hashes and transcripts.

**E2E / compose vs staging:** Strong claims in the manuscript should map to a **validation tier** (T0–T3). See [`docs/validation-tiers.md`](../docs/validation-tiers.md) and align prose with [`research/are-theory-expansion/GAP_MAP.md`](../research/are-theory-expansion/GAP_MAP.md) **claim status**—do not treat local Docker-only runs (T1) as proof of production behavior (T3) without separate evidence.

---

## Normative STPA (public, no ARE clone required)

The full **`research/stpa/`** tree is mirrored on the public discipline repo:  
[github.com/srex-dev/AgentResponsibilityEngineering/tree/main/research/stpa](https://github.com/srex-dev/AgentResponsibilityEngineering/tree/main/research/stpa)  
(`STPA_RESOLUTION.md`, `STPA_PACKAGE.md`, UCA enumeration, hazard closure, etc.)

## Frozen packet identity

| Field | Value |
|-------|--------|
| **Bundle directory** | `research/evidence-bundles/2026-04-26-stamp-safety-reviewer-packet-submission/` |
| **Git commit at freeze** (`GIT_HEAD.txt`) | `653d455346e57f6a0ba37eecb4132138d923a36d` |
| **What “frozen” means** | Copies of normative STPA docs + paper pointers + **full** test and gate logs + `FILES.sha256` for every file in the packet |

## What is inside (categories only)

1. **Normative analysis:** `STPA_RESOLUTION.md`, `STPA_PACKAGE.md`, `claims_ledger.md`, `interposition_audit.md`, `SAFETY_CASE.md` (copies at freeze time).  
2. **Execution of checks:** `test-output.log` (pytest), `run-all-internal-gates-full.log`.  
3. **Integrity:** `MANIFEST.md`, `FILES.sha256`, `GIT_HEAD.txt`, `GIT_BRANCH.txt`, `FREEZE_TIME_UTC.txt`.

## Recorded outcomes (from `MANIFEST.md` at freeze)

- **`python -m pytest tests/ -q`** — STAMP harness: **10 passed** (see bundle `test-output.log`).  
- **`python tools/testing/run-all-internal-gates.py`** — internal gate matrix: **48 components, ALL PASS** (see bundle logs).

*Re-freeze updates these numbers; align this summary when you cut a new bundle.*

---

## How to get more than this summary

| Need | Action |
|------|--------|
| **Level 3 full packet** | Request confidential zip from authors **or** run `python tools/paper/assemble_submission_package.py` from a clone that contains `research/evidence-bundles/…`. |
| **Verify integrity** | In the bundle directory: `sha256sum -c FILES.sha256` (Linux/macOS) or equivalent on Windows. |
| **Why not in the public PDF?** | See §13 of `STAMP_ARE_ARXIV_READY.md` — IP, hygiene, and size; **not** “evidence only on payment.” |

---

*This file is safe to mirror on a public discipline repo alongside the PDF.*
