# Chotu, a production AI operations agent

> A multi-agent system that has run a company's sourcing operations over WhatsApp, continuously, since February 2026.

Chotu is not a chatbot. It is a scheduled, multi-agent platform with persistent memory, tool routing, cost governance and an incident history. It sources products across OTAPI, Alibaba 1688 and SAGE, generates client-ready decks, runs outbound email campaigns and coordinates the team, and it does so unattended most of the time.

Most of the engineering value in it is not the model calls. It is everything built around them to stop an autonomous agent from doing damage: fabrication guards, output contracts, immutable doctrine files, allowlists, audit trails and a documented recovery runbook.

---

## Architecture

```
        WhatsApp Business  (team and client groups, direct messages)
                                |
                    Gateway  (Node, systemd, always-on)
                                |
    +---------------+-----------+-----------+------------------+
    |               |                       |                  |
 5 agents      Cron scheduler          Tool scripts        Memory tiers
 routed by     88 jobs defined         ~4,000 lines        identity / long
 group and     46 enabled              sourcing, decks,    term / rolling
 binding       per-tick reload         campaigns, memory   short term
                                                                |
                                            Structured run logs, S3 backup
```

**Five agents** with routing by group binding, so the team channel, the executive assistant channel and the partner channel each get an agent with its own context and permissions rather than one generic assistant guessing who it is talking to.

**A scheduler, not just a responder.** 88 defined jobs, 46 currently enabled, covering morning briefs, pipeline tracking, inbox scanning, campaign statistics, health checks and watchdogs. The scheduler reloads its job definitions every tick, so operational changes ship without restarting the gateway and dropping live messages.

---

## Model routing and cost governance

The system runs across roughly a dozen models from three families, tiered deliberately by job rather than defaulting to the strongest available.

| Tier | Used for | Job count |
|---|---|---|
| **Strong reasoning** | Executive synthesis and summarisation where output quality is visible to leadership | 10 |
| **Light and fast** | High frequency operational jobs, inbox scanning, statistics, reminders | 6 |
| **Balanced default** | Internal and engineering jobs, silent-on-empty monitors | 27 plus defaults |

**Cost engineering.** Over roughly two and a half months the entire stack was migrated off direct paid API billing onto a flat-rate subscription, ending with **zero execution paths billing a metered API key**. This was not a single switch. It required moving every scheduled job, then the runtime defaults, then the fallback chain, verifying each step against the live system.

**Fallback design.** The fallback model is deliberately chosen from a **different upstream family** than the primary. The most common reason a primary call fails is an upstream outage at that model's provider, and models sharing that upstream would fail at the same moment. A same-family fallback is not a fallback.

---

## Memory

Three tiers, split by how long the information needs to live, because context windows are finite and every token is paid for on every turn.

| Tier | Contents | Lifetime |
|---|---|---|
| **Identity** | Who the agent is, its rules, team preferences, interface specifications | Loaded every turn |
| **Long term** | Client history, standing decisions, durable facts | Changes slowly |
| **Rolling short term** | Per-channel conversation capture, regenerated on a schedule | Rolling window |

Every interaction is written to structured run logs, so any action can be traced back to the tool call and reasoning that produced it. Logs and memory are backed up to object storage, currently several thousand objects and a few gigabytes.

A hard constraint worth knowing about: the identity file has a **byte-based size cap**, not a character cap. Emoji are multi-byte, so a character count under-reads the real size and the file silently truncates past the limit, quietly dropping rules from the end. Trimming it is done through a token-preservation harness that verifies every header, rule, path and identifier survives before the write is committed.

---

## Keeping an autonomous agent honest

This is the part I would want to talk about in an interview.

**The fabrication problem.** The agent once sent a confident, detailed, entirely invented summary of team performance. Tracing it exposed three upstream bugs in a CRM integration: a list endpoint that silently discarded filter criteria, a field name that did not exist in the actual schema, and the wrong date field used for closed records. The data layer was returning nothing useful, and the agent filled the gap.

The fix was not only the three bugs. The data layer now self-reports a **health state**, and when that state is degraded it emits an explicit directive telling the agent to **suppress the summary entirely rather than produce one**.

The general lesson, which generalises well beyond this system:

> When an API response shape does not match what was expected, an agent's path of least resistance is fabrication from its training prior, not error reporting. The data layer has to detect its own degradation and instruct the agent accordingly, because the agent will not notice.

**Verify before done.** A behavioural rule, reinforced by a per-turn reminder injected into every conversation, requiring the agent to confirm an action actually completed before reporting it as complete. This exists because it once announced a task as finished and then admitted a minute later that it had not started.

**Confidentiality guards.** An outbound audit hook watches for internal supplier taxonomy and strategic context appearing in client-facing conversations. The first version ran in-process and missed most outbound traffic because one delivery path bypassed the event it listened to. It was replaced with a polling monitor that sees everything.

**Silence as a feature.** Scheduled jobs that have nothing material to report emit an explicit no-reply token and deliver nothing. Client-facing jobs carry an output contract that bans narration, internal jargon, reasoning preamble and status noise. An operations agent that speaks constantly gets muted, and a muted agent is worthless.

**Readback and confirm.** After the agent inferred dates from an image and created a batch of duplicate calendar events, the first rule written was "never infer from images". That was wrong, and it was corrected: the agent may infer, but it must **read its interpretation back in plain language and wait for confirmation** before any write. The failure mode was silent inference, not inference.

**Doctrine files are immutable.** The identity and rules files are filesystem-immutable and can only be changed through an unlock, backup, patch, validate, relock, verify procedure. Chat history and transcripts explicitly do not count as doctrine. Only the locked files and explicit human authorisation do.

**Probe testing.** The trust rules were verified behaviourally in production rather than assumed, with deliberate probes for false completion claims, duplicate detection and date resolution. Three of three passed.

---

## Operating it

Real production systems break, and this one has an incident history with a written runbook.

- **A crash loop** caused by making the scheduler's own state file immutable. The scheduler rewrites that file every tick, so immutability produced a write failure, a non-zero exit, and an automatic restart loop that generated over thirteen thousand orphaned temporary files. The lock tooling now explicitly excludes it, and that exclusion predates the incident by six weeks, which is its own lesson about respecting decisions you did not make.
- **A four hour messaging outage** after a server-side session invalidation. Encrypted credential backups turned out to be useless for recovery because they do not preserve live session ratchet state. The only path back was a fresh pairing, and the first attempts hit a provider-side rate limit. The runbook now covers preparation before recovery is attempted.
- **A thirteen hour silent sync failure** where a backup job swallowed a rejected push and logged success. Now it detects the condition explicitly and exits loudly.

That last one produced the principle I have found most useful:

> Infrastructure that silently no-ops on failure is worse than infrastructure that crashes. Every operational script must succeed loudly or fail loudly, never neither.

Supporting operational work: scheduled backups to object storage, external uptime monitoring, anti-stampede scheduling so a restart does not fire every job at once, and a documented change procedure that requires a timestamped backup, a deep comparison against untouched state, validation, and an atomic write with automatic rollback on any failed assertion.

---

## Knowledge transfer

An agent that only one person can operate is a liability regardless of how well it runs. The system is documented in an operations manual and a context pack totalling several hundred kilobytes, covering invariants, change procedures, an incident playbook and the reasoning behind decisions that look strange without context. **A second engineer has been onboarded against that documentation.**

For scale: an external vendor proposed a multi-month engagement to deliver the governance, documentation and reliability work. Roughly half that scope was delivered in-house in about a month, and the remaining items were consciously deferred rather than missed.

---

## Stack

| Component | Technology |
|---|---|
| Runtime | Node, systemd-managed long-running process |
| Models | Multi-provider, tiered by job class, single-subscription billing |
| Messaging | WhatsApp Business |
| Tooling | ~4,000 lines of custom scripts for sourcing, decks, campaigns and memory |
| Integrations | OTAPI and Alibaba 1688 for China sourcing, SAGE for US promotional products, Zoho CRM, Gamma for presentation generation, SmartLead for email campaigns, the SourceWithAI platform API |
| Scheduling | 88 defined jobs, 46 enabled, per-tick reload |
| Storage | Object storage backups, structured run logs |
| Monitoring | External uptime checks, watchdog jobs, file size and health monitors |

---

## My role

Architect and developer. I designed and built the agent, its memory architecture, the tool routing, the scheduled job system, the model tiering and cost migration, and the trust and confidentiality guards. I wrote the operations documentation and ran the handover to the second engineer.

---

## Status

**In production**, running continuously and handling live business operations.

> Private commercial repository containing proprietary business logic, integrations and client data. Architecture and engineering approach are documented here for portfolio purposes. A walkthrough and live demonstration are available on request during interviews.
