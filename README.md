# Agent Reliability Engineering (ARE)

**A discipline for governing autonomous AI agents in production.**

---

## The Problem

Agents are already in production.

They are calling APIs, moving money, drafting communications, making decisions, and operating inside regulated environments with real consequences. Most of them have no identity. No verifiable authorization chain. No observable decision trail. No policy enforcement that isn't also the agent itself.

We gave them intelligence. We did not give them governance.

That is the problem ARE exists to solve.

---

## The Discipline

Agent Reliability Engineering is the practice of making autonomous AI agents **safe to run at scale** in production environments.

Not safe in theory. Safe in practice — in the same way Site Reliability Engineering made distributed systems safe to operate at scale. SRE didn't eliminate failure. It made failure observable, bounded, and recoverable. ARE does the same for autonomous agents.

ARE is not about slowing agents down. It is about making them **trustworthy enough to move fast**.

---

## The First Principles

**1. Intelligence never grants authority.**
An agent being capable of an action is not justification for performing it. Capability and authorization are separate concerns and must be separately enforced.

**2. Every agent must be provably legible.**
Not monitored. Not logged. *Legible* — any authorized party must be able to reconstruct exactly who the agent is, what it was authorized to do, and what it actually did, at any point in time.

**3. Trust has a half-life.**
The basis for authorizing an agent's action degrades over time. Authorization must be continuously re-established, not assumed from prior grant.

**4. Policy is infrastructure.**
Governance cannot live inside the agent. An agent that governs itself is not governed. Policy enforcement must be external, verifiable, and outside the agent's ability to override.

**5. Failure must be bounded.**
An agent that fails silently is more dangerous than an agent that fails loudly. ARE systems surface failures at the earliest detectable point and contain blast radius before escalation.

**6. The delegation chain is the audit trail.**
Every agent action must be traceable to a human decision. If you cannot answer "which human authorized this chain of actions," the system is ungoverned regardless of what the logs say.

**7. Observability is not optional.**
You cannot govern what you cannot see. Metrics, traces, and decision logs are not compliance features — they are the primitive layer everything else depends on.

**8. Posture degrades under pressure.**
Governance systems that hold in normal operation often collapse under load, incident, or deadline pressure. ARE systems are designed to hold their posture precisely when the environment is most hostile.

**9. The framework precedes the feature.**
Governance retrofitted onto an existing agent system is technical debt with compliance risk attached. ARE is built first, not bolted on after.

**10. Together or not at all.**
Governance that only one team believes in is not governance. ARE requires organizational alignment — engineering, product, compliance, and operations moving in the same direction.

---

## The Architecture

ARE operates across four layers:

```
┌─────────────────────────────────────┐
│           POSTURE LAYER             │  Org alignment, culture, incentives
├─────────────────────────────────────┤
│       EPISTEMOLOGICAL LAYER         │  What the system knows and how it knows it
├─────────────────────────────────────┤
│         OPERATIONAL LAYER           │  Runtime enforcement, observability, response
├─────────────────────────────────────┤
│         FOUNDATIONAL LAYER          │  Identity, authorization, policy primitives
└─────────────────────────────────────┘
```

**Foundational** — Agent identity (passports), cryptographic authorization chains, external policy enforcement. Nothing runs without this layer being correct.

**Operational** — Runtime enforcement, drift detection, incident intelligence, SLO/SLI tracking for agent behavior. This layer makes the foundational layer observable.

**Epistemological** — How the system knows what agents know, what they were told, and whether that knowledge is still valid. Evidence decay, context staleness, hallucination surface area.

**Posture** — The organizational and cultural conditions required for the other three layers to hold under real-world pressure. The layer most often missing.

---

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
