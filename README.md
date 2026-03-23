# Chotu - AI Business Operations Agent

> **WhatsApp-connected AI agent** that autonomously handles B2B sourcing operations, presentation generation, email campaigns, and team coordination for SourceWithAI.

Built on the [OpenClaw](https://github.com/nichochar/openclaw) gateway framework, Chotu is a production AI agent that operates 24/7 via WhatsApp, handling real business tasks with persistent memory, multi-tool orchestration, and autonomous decision-making.

---

## Architecture

```
+-------------------------------------------------------------+
|                    WhatsApp Business                         |
|              Team Group Chat (4 members)                     |
+----------------------+--------------------------------------+
                       | Messages
                       v
+-------------------------------------------------------------+
|              OpenClaw Gateway (systemd)                      |
|                   Port 18789                                 |
|                                                             |
|  +-------------+  +--------------+  +-------------------+  |
|  |  SOUL.md    |  |  MEMORY.md   |  |  Session Logs     |  |
|  |  Identity   |  |  Long-term   |  |  (JSONL)          |  |
|  |  & Rules    |  |  Memory      |  |                   |  |
|  +-------------+  +--------------+  +-------------------+  |
|                                                             |
|  +-----------------------------------------------------+   |
|  |              Tool Scripts                            |   |
|  |                                                     |   |
|  |  otapi-helper.js (2700+ lines)                      |   |
|  |  |-- OTAPI product search (Alibaba/1688/Taobao)     |   |
|  |  |-- SWAI platform search integration               |   |
|  |  |-- Image-based product search                     |   |
|  |  +-- Chinese keyword translation (1281 keywords)    |   |
|  |                                                     |   |
|  |  sage-helper.js (929 lines)                         |   |
|  |  |-- US promotional product search                  |   |
|  |  |-- Inventory status checking                      |   |
|  |  +-- Product enrichment pipeline                    |   |
|  |                                                     |   |
|  |  smartlead-helper.js                                |   |
|  |  |-- Email campaign management                      |   |
|  |  |-- Lead list operations                           |   |
|  |  +-- Campaign analytics                             |   |
|  |                                                     |   |
|  |  short-term-memory.js                               |   |
|  |  +-- Auto-generates memory files every 2 hours      |   |
|  +-----------------------------------------------------+   |
|                                                             |
|  +-----------------------------------------------------+   |
|  |          External API Integrations                   |   |
|  |                                                     |   |
|  |  Gamma API ---- Presentation generation             |   |
|  |  OTAPI -------- B2B product sourcing (China)        |   |
|  |  SAGE --------- US promo product sourcing           |   |
|  |  SWAI API ----- Platform search integration         |   |
|  |  SmartLead ---- Email campaign automation           |   |
|  +-----------------------------------------------------+   |
+-------------------------------------------------------------+
```

---

## What Chotu Does

### Product Sourcing
When the team requests products (e.g. "find cowboy hats under $5"), Chotu:
1. Determines the best source platform (China/US/SWAI catalog)
2. Executes multi-platform searches across OTAPI, 1688, and SAGE
3. Filters results by price, MOQ, and vendor quality
4. Returns formatted product cards with images, pricing, and supplier details
5. Handles follow-up refinements ("cheaper ones", "show more colors")

### Presentation Generation
Chotu generates client-ready product presentations via the Gamma API:
- Builds slide decks with 30-50 products across categorized sections
- Includes real product images sourced from supplier APIs
- Supports theming, budget tiers, and custom branding
- Delivers shareable links and PPTX exports directly in WhatsApp

### Email Campaign Management
Integrates with SmartLead for outbound email campaigns:
- Creates and manages lead lists
- Launches email sequences with scheduling
- Reports campaign analytics (open rates, replies, bounces)

### Memory & Context
Chotu maintains persistent awareness across conversations:
- **SOUL.md** - Core identity, rules, team preferences, API specifications
- **MEMORY.md** - Long-term persistent facts (client history, past decisions)
- **Daily memory files** - Auto-generated summaries of each day's operations
- **Short-term memory** - Auto-captures WhatsApp group messages every 2 hours
- **Session logs** - Full JSONL audit trail of every interaction

### Team Coordination
- Knows each team member's role and communication preferences
- Routes sourcing requests to the right workflow
- Tracks open tasks and follow-ups
- Sends proactive reminders for pending items

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| **AI Model** | Claude Sonnet 4.6 (via GitHub Copilot) |
| **Gateway** | OpenClaw (Node.js, systemd service) |
| **Messaging** | WhatsApp Business API |
| **Scripting** | Node.js (4000+ lines of custom helper scripts) |
| **Presentation** | Gamma API (slide generation, theming, PPTX export) |
| **Product Search** | OTAPI, Alibaba 1688, SAGE, SWAI platform API |
| **Email** | SmartLead API (campaign automation) |
| **Translation** | Chinese keyword database (1281 mapped terms) |
| **Infrastructure** | Vultr VPS, systemd, journalctl logging |

---

## Key Design Decisions

### Personality-Driven Agent
Chotu has a defined identity (SOUL.md) with personality traits, communication style, and operational boundaries. This isn't a generic chatbot - it's a team member with preferences and guardrails.

### Memory Architecture (3-Tier)
```
Long-term (MEMORY.md)      --> Permanent facts, client history, standing rules
Daily (memory/YYYY-MM-DD)  --> Day-specific context, decisions made, tasks completed
Short-term (auto-generated) --> Rolling 48h window of group chat activity
```

### Tool Routing
Chotu decides which tool to invoke based on message intent:
- Product requests --> `otapi-helper.js` or `sage-helper.js`
- Presentation requests --> Gamma API
- Campaign management --> `smartlead-helper.js`
- General questions --> Direct LLM response

### Guardrails
- Never exposes API keys, credentials, or internal architecture in chat
- Escalates to team leads for decisions above its authority
- Follows presentation SOPs with locked formatting rules
- Maintains audit trail of every action

---

## Results (Production Metrics)

| Metric | Value |
|--------|-------|
| **Presentations Generated** | 6+ client decks (30-50 products each) |
| **Product Searches** | Thousands of queries across 3 platforms |
| **Uptime** | 24/7 systemd-managed process |
| **Memory Files** | Auto-generated every 2 hours |
| **Helper Scripts** | 4000+ lines of custom tooling |
| **Team Members Served** | 4 (CTO, CEO, Finance, Business Dev) |

---

## My Role

**Architect & Developer** - Designed and built the entire agent system:

- Designed the SOUL/MEMORY/TOOLS architecture for persistent AI agent behavior
- Built 4000+ lines of Node.js helper scripts for product sourcing across 3 platforms
- Integrated Gamma API for autonomous presentation generation
- Integrated SmartLead API for email campaign automation
- Built the Chinese keyword translation database (1281 term mappings)
- Configured production deployment (systemd, auto-restart, log management)
- Wrote operational SOPs, guardrails, and decision routing logic

---

## Status

**Production** - Running 24/7, actively handling business operations for SourceWithAI.

---

> *This is a private commercial project. The repository contains proprietary business logic, API integrations, and client data. Architecture details are shared here for portfolio purposes. Code review and live demonstration available upon request during interviews.*
