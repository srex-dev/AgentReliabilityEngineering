# Agent Responsibility Engineering (ARE)

**A discipline for governing autonomous AI agents in production.**

---

## The Problem

Agents are already in production.

They are calling APIs, moving money, drafting communications, making decisions, and operating inside regulated environments with real consequences. Most of them have no identity. No verifiable authorization chain. No observable decision trail. No policy enforcement that isn't also the agent itself.

We gave them intelligence. We did not give them governance.

That is the problem ARE exists to solve.

---

## The Discipline

Agent Responsibility Engineering is the practice of making autonomous AI agents **safe to run at scale** in production environments.

Not safe in theory. Safe in practice — in the same way Site Reliability Engineering made distributed systems safe to operate at scale. SRE didn't eliminate failure. It made failure observable, bounded, and recoverable. ARE does the same for autonomous agents.

ARE is not about slowing agents down. It is about making them **trustworthy enough to move fast**.

---

## The Tenets

### FOUNDATIONAL — How you build it

**I. Governance is architectural, not operational.**
Accountability cannot be retrofitted. Identity, authority, and proof must be primitives present from the first action, not added after the first incident.

**II. The spawn chain is the authority chain.**
Every agent must be traceable to a human origin. Delegation is not inheritance. Each step must explicitly scope what transfers and what does not. Undocumented delegation is unauthorized delegation.

**III. The ledger is ground truth.**
What an agent did is defined by the immutable, cryptographically signed record of its actions, not by what it reported, what its logs suggest, or what its operator believes.

---

### OPERATIONAL — How you run it

**IV. Intelligence never grants authority.**
Capability is not permission. Authorization must be explicit, bounded, and separate from capability at every layer of the system.

**V. Scope is a contract, not a suggestion.**
An agent's defined scope is a binding constraint, not a behavioral description. Deviation is not a bug. It is a breach.

**VI. Trust has a half-life.**
Authorization degrades. Policies age. Models drift. The basis for trust must be continuously revalidated, not assumed to hold indefinitely.

---

### EPISTEMOLOGICAL — How you know it

**VII. Every agent must be provably legible.**
Any authorized party must be able to reconstruct who the agent is, what it was authorized to do, and what it actually did, with cryptographic certainty, at any point in time.

**VIII. Proof requires falsification.**
Observation produces data. Investigation produces hypotheses. Neither produces proof. A verified conclusion requires a documented falsification trail — what was tested, what failed, what survived, and why.

---

### POSTURE — How you hold the line

**IX. An unknown agent is an ungovernable agent.**
An agent that cannot be identified, traced, and scoped has no standing in a governed system. Ungovernability is treated as a critical condition by default.

**X. The system must explain itself.**
A governance system that only its builders can interpret has failed its primary obligation. Legibility to auditors, operators, and the people affected by agent decisions is not a feature. It is the measure of whether governance is actually governing anything at all.

---

## The Architecture

ARE operates across four layers:

```
┌─────────────────────────────────────┐
│           POSTURE LAYER             │  How you hold the line
├─────────────────────────────────────┤
│       EPISTEMOLOGICAL LAYER         │  How you know it
├─────────────────────────────────────┤
│         OPERATIONAL LAYER           │  How you run it
├─────────────────────────────────────┤
│         FOUNDATIONAL LAYER          │  How you build it
└─────────────────────────────────────┘
```

**Foundational** — Agent identity (passports), cryptographic authorization chains, immutable ledger, external policy enforcement. Nothing runs without this layer being correct.

**Operational** — Runtime enforcement, scope contracts, drift detection, SLO/SLI tracking for agent behavior. This layer makes the foundational layer enforceable.

**Epistemological** — How the system knows what agents know, what they were told, and whether that knowledge is still valid. Evidence decay, context staleness, falsification trails.

**Posture** — The organizational and cultural conditions required for the other three layers to hold under real-world pressure. The layer most often missing.

---

## STAMP paper (bounded safety case)

Structured argument connecting **STAMP/STPA** to **Agent Responsibility Engineering** at a **defined execution boundary** (not “safe AI” in the large). **Evidence is tiered** (public paper → one-page summary → full hashed bundle on request); see **`EVIDENCE_PUBLIC_SUMMARY.md`** and §13 of the Markdown/PDF.

| File | Description |
|------|-------------|
| **`README_PAPER_PIPELINE.md`** | **Start here** — build order, links, submission zip pointer. |
| **`STAMP_ARE_Paper.pdf`** | Latest PDF (Word pipeline: tables + embedded figure/equation PNGs). |
| **`STAMP_ARE_Paper_arxiv_ready.md`** / **`.docx`** | Sources synced from the ARE platform `paper/` build. |
| **`EVIDENCE_PUBLIC_SUMMARY.md`** | **Level 2** attestation: freeze id, commit, what artifact *classes* exist (no raw logs). |
| **`PDF_AND_WORD_FORMATTING.md`** | Why PDF layout differs from LaTeX; OneDrive / export tips. |
| **`ARXIV_AND_EVIDENCE_REALITY.md`** | Short pointer: arXiv LaTeX vs this Markdown path. |
| **`paper-assets/*.png`** | Raster figures embedded in the PDF (golden path + invariant equations). |

**Normative STPA package (public):** [`research/stpa/`](research/stpa/) — mirror of the private ARE repo’s `research/stpa/` so **`STPA_RESOLUTION.md`** and related closure docs are readable without monolith access.

Full **Level 3** frozen packets (logs, `FILES.sha256`, copied artifacts, etc.) remain with the **private** platform repository under `research/evidence-bundles/` — not here; reviewers can request or receive the zip as supplementary material.

---

## The Reference Implementation

Key components:

- **Guardian-Agent** — Rust-based policy co-processor. External enforcement on the hot path. [`srex-dev/guardian-agent`](https://github.com/srex-dev/guardian-agent)

---

## Who This Is For

ARE is for the engineer who just got asked "can you prove the agent only did what it was authorized to do" and doesn't have a clean answer.

For the architect building an agent platform inside a regulated industry who knows that compliance is coming and wants to build ahead of it.

For the team that shipped an agent to production and is now quietly terrified of what it might do next.

For the organization that wants to move fast with autonomous AI without becoming a case study in what happens when you don't govern it.

---

## The State of the Field

ARE is a new discipline. The vocabulary is being established now. The tooling is early. The patterns are emerging from production systems, not from whitepapers.

If you are building governed agent systems in production — the framework is open. The conversation is open. The discipline is being defined by the people doing the work.

That is how SRE started. That is how ARE starts.

---

## Get Involved

If this framework reflects problems you are solving, **star and watch this repo**.

The framework is evolving. Issues, discussion, and pull requests are open.

---

*Built by [Jonathan Kershaw](https://linkedin.com/in/jonjonkershaw) — Principal AI Platform Engineer, founding practitioner of governed autonomous runtimes in regulated financial services.*

*[github.com/srex-dev](https://github.com/srex-dev)*
