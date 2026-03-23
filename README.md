# Chotu â€” AI Business Operations Agent

> **WhatsApp-connected AI agent** that autonomously handles B2B sourcing operations, presentation generation, email campaigns, and team coordination for SourceWithAI.

Built on the [OpenClaw](https://github.com/nichochar/openclaw) gateway framework, Chotu is a production AI agent that operates 24/7 via WhatsApp, handling real business tasks with persistent memory, multi-tool orchestration, and autonomous decision-making.

---

## Architecture

```
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚                    WhatsApp Business                         â”‚
â”‚              Team Group Chat (4 members)                     â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
                       â”‚ Messages
                       â–¼
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚              OpenClaw Gateway (systemd)                      â”‚
â”‚                   Port 18789                                 â”‚
â”‚                                                             â”‚
â”‚  â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”  â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”  â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”  â”‚
â”‚  â”‚  SOUL.md    â”‚  â”‚  MEMORY.md   â”‚  â”‚  Session Logs     â”‚  â”‚
â”‚  â”‚  Identity   â”‚  â”‚  Long-term   â”‚  â”‚  (JSONL)          â”‚  â”‚
â”‚  â”‚  & Rules    â”‚  â”‚  Memory      â”‚  â”‚                   â”‚  â”‚
â”‚  â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜  â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜  â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜  â”‚
â”‚                                                             â”‚
â”‚  â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”    â”‚
â”‚  â”‚              Tool Scripts                            â”‚    â”‚
â”‚  â”‚                                                     â”‚    â”‚
â”‚  â”‚  otapi-helper.js (2700+ lines)                      â”‚    â”‚
â”‚  â”‚  â”œâ”€â”€ OTAPI product search (Alibaba/1688/Taobao)     â”‚    â”‚
â”‚  â”‚  â”œâ”€â”€ SWAI platform search integration               â”‚    â”‚
â”‚  â”‚  â”œâ”€â”€ Image-based product search                     â”‚    â”‚
â”‚  â”‚  â””â”€â”€ Chinese keyword translation (1281 keywords)    â”‚    â”‚
â”‚  â”‚                                                     â”‚    â”‚
â”‚  â”‚  sage-helper.js (929 lines)                         â”‚    â”‚
â”‚  â”‚  â”œâ”€â”€ US promotional product search                  â”‚    â”‚
â”‚  â”‚  â”œâ”€â”€ Inventory status checking                      â”‚    â”‚
â”‚  â”‚  â””â”€â”€ Product enrichment pipeline                    â”‚    â”‚
â”‚  â”‚                                                     â”‚    â”‚
â”‚  â”‚  smartlead-helper.js                                â”‚    â”‚
â”‚  â”‚  â”œâ”€â”€ Email campaign management                      â”‚    â”‚
â”‚  â”‚  â”œâ”€â”€ Lead list operations                           â”‚    â”‚
â”‚  â”‚  â””â”€â”€ Campaign analytics                             â”‚    â”‚
â”‚  â”‚                                                     â”‚    â”‚
â”‚  â”‚  short-term-memory.js                               â”‚    â”‚
â”‚  â”‚  â””â”€â”€ Auto-generates memory files every 2 hours      â”‚    â”‚
â”‚  â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜    â”‚
â”‚                                                             â”‚
â”‚  â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”    â”‚
â”‚  â”‚          External API Integrations                   â”‚    â”‚
â”‚  â”‚                                                     â”‚    â”‚
â”‚  â”‚  Gamma API â”€â”€â”€â”€ Presentation generation             â”‚    â”‚
â”‚  â”‚  OTAPI â”€â”€â”€â”€â”€â”€â”€â”€ B2B product sourcing (China)        â”‚    â”‚
â”‚  â”‚  SAGE â”€â”€â”€â”€â”€â”€â”€â”€â”€ US promo product sourcing           â”‚    â”‚
â”‚  â”‚  SWAI API â”€â”€â”€â”€â”€ Platform search integration         â”‚    â”‚
â”‚  â”‚  SmartLead â”€â”€â”€â”€ Email campaign automation           â”‚    â”‚
â”‚  â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜    â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
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
- **SOUL.md** â€” Core identity, rules, team preferences, API specifications
- **MEMORY.md** â€” Long-term persistent facts (client history, past decisions)
- **Daily memory files** â€” Auto-generated summaries of each day's operations
- **Short-term memory** â€” Auto-captures WhatsApp group messages every 2 hours
- **Session logs** â€” Full JSONL audit trail of every interaction

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
Chotu has a defined identity (SOUL.md) with personality traits, communication style, and operational boundaries. This isn't a generic chatbot â€” it's a team member with preferences and guardrails.

### Memory Architecture (3-Tier)
```
Long-term (MEMORY.md)     â†’ Permanent facts, client history, standing rules
Daily (memory/YYYY-MM-DD) â†’ Day-specific context, decisions made, tasks completed
Short-term (auto-generated)â†’ Rolling 48h window of group chat activity
```

### Tool Routing
Chotu decides which tool to invoke based on message intent:
- Product requests â†’ `otapi-helper.js` or `sage-helper.js`
- Presentation requests â†’ Gamma API
- Campaign management â†’ `smartlead-helper.js`
- General questions â†’ Direct LLM response

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

**Architect & Developer** â€” Designed and built the entire agent system:

- Designed the SOUL/MEMORY/TOOLS architecture for persistent AI agent behavior
- Built 4000+ lines of Node.js helper scripts for product sourcing across 3 platforms
- Integrated Gamma API for autonomous presentation generation
- Integrated SmartLead API for email campaign automation
- Built the Chinese keyword translation database (1281 term mappings)
- Configured production deployment (systemd, auto-restart, log management)
- Wrote operational SOPs, guardrails, and decision routing logic

---

## Status

**Production** â€” Running 24/7, actively handling business operations for SourceWithAI.

---

> *This is a private commercial project. The repository contains proprietary business logic, API integrations, and client data. Architecture details are shared here for portfolio purposes. Code review and live demonstration available upon request during interviews.*
