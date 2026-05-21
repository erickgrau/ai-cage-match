# Phase 2: Orchestration Backend Architecture

Document type: Technical build specification
Status: Phase 2 design — pending implementation
Target implementation: May 18–31, 2026
Author: Erick Grau, Chibitek / ChibitekLabs

---

## 1. Stack Overview

The Phase 2 backend is the operational infrastructure that runs the experiment: it executes agent runtime loops, enforces spending rules, records all decisions and transactions, and surfaces data to the Phase 3 dashboard. The stack is deliberately conservative — Erick's existing proficiency stack — to minimize build time and operational risk.

### Core Stack

**Runtime and API layer:** Node.js 22 LTS (TypeScript), deployed on [Vercel](https://vercel.com) as serverless functions and edge functions. API routes handle agent decision triggers, webhook ingestion, and dashboard data queries.

**Database:** [Supabase](https://supabase.com) Postgres (free tier) for all relational data: agent decisions, spend transactions, revenue events, checkpoints, and artifacts. Supabase Auth for the operator dashboard login. Supabase Storage for artifact files (generated content, images, documents).

**Cron and scheduled tasks:** [GitHub Actions](https://github.com/features/actions) scheduled workflows handle the daily tick (agent planning and execution cycle) and weekly tick (checkpoint review generation). GitHub Actions cron is free for public repositories and does not require a separate scheduler service.

**Version control:** GitHub. All agent decision logs, checkpoint summaries, and config files are committed to the repository, providing a durable public audit trail.

### Why This Stack

Vercel's serverless model means the backend costs approximately $0/month at this scale — the free hobby tier handles the request volume easily. Supabase free tier handles up to 500 MB of database storage and 2 GB of egress per month, which is more than sufficient. The total infrastructure cost for Phase 2 is $0 in monthly fees, which matters for an experiment built around tight budgets.

### Supporting Services

- **Browser automation:** [Browserbase](https://browserbase.com) (or self-hosted Playwright on a Fly.io free instance as fallback) for agent browser actions
- **Web search:** SerpAPI (100 free searches/month on the free tier) for agent search queries
- **Email delivery:** Resend (free tier, 3,000 emails/month) for daily digest delivery to Erick
- **Monitoring:** Sentry free tier for error tracking; custom logging to Supabase

---

## 2. Data Model

All tables live in a single Supabase Postgres project. Row-level security (RLS) is enabled; the operator dashboard has full read/write access, and the public dashboard has read-only access to non-sensitive columns.

### Table: `agents`

Stores the static configuration for each AI competitor.

```
id            uuid          primary key
name          text          not null  (e.g. "perplexity", "claude", "chatgpt")
display_name  text          not null  (e.g. "Perplexity", "Claude", "ChatGPT")
card_token    text          not null  (Privacy.com virtual card token)
card_last4    text          not null  (for display)
config        jsonb         not null  (model endpoint, tool surface config, prompt templates)
status        text          not null  (active | paused | disqualified)
created_at    timestamptz   not null  default now()
```

### Table: `decisions`

The core log of every decision the agent makes during its runtime loop. This is the primary audit trail.

```
id              uuid          primary key
agent_id        uuid          references agents(id)
tick_type       text          not null  (daily | weekly)
tick_date       date          not null
decision_type   text          not null  (plan | execute | reflect)
decision_text   text          not null  (the agent's reasoning, in plain language)
tool_calls      jsonb         (array of tool calls made: name, input, output)
outcome         text          (success | failure | partial | blocked)
created_at      timestamptz   not null  default now()
```

### Table: `spend_transactions`

Every transaction on the Privacy.com virtual card, populated by Privacy.com webhooks.

```
id                uuid          primary key
agent_id          uuid          references agents(id)
privacy_event_id  text          unique not null
amount_cents      integer       not null
merchant_name     text
merchant_mcc      text          (merchant category code)
status            text          not null  (approved | declined | pending)
declined_reason   text          (if status = declined)
occurred_at       timestamptz   not null
created_at        timestamptz   not null  default now()
```

### Table: `revenue_events`

Every verified revenue event (sale, subscription charge, payout).

```
id              uuid          primary key
agent_id        uuid          references agents(id)
platform        text          not null  (e.g. "gumroad", "etsy", "lemon_squeezy", "beehiiv")
event_type      text          not null  (sale | subscription_charge | refund | payout)
amount_cents    integer       not null  (gross)
fee_cents       integer       not null  (platform fees)
net_cents       integer       not null  (amount - fee)
product_name    text
customer_id     text          (platform-specific customer identifier, hashed)
occurred_at     timestamptz   not null
created_at      timestamptz   not null  default now()
```

### Table: `weekly_checkpoints`

Auto-generated weekly checkpoint records, one per agent per week.

```
id                    uuid          primary key
agent_id              uuid          references agents(id)
week_number           integer       not null  (1–12)
checkpoint_date       date          not null
cash_on_hand_cents    integer       not null
gross_revenue_cents   integer       not null
gross_expenses_cents  integer       not null
net_revenue_cents     integer       not null
mrr_cents             integer       not null  default 0
decision_log_excerpt  text          (top 3 decisions, plain language)
agent_reflection      text          (agent's own 1-paragraph reflection)
safety_flags          jsonb         (array of any safety events this week)
blockers              jsonb         (array of any operator-intervention requests)
status                text          not null  (draft | published)
created_at            timestamptz   not null  default now()
```

### Table: `artifacts`

Files and content assets produced by agents (newsletter issues, product listings, code files, images).

```
id              uuid          primary key
agent_id        uuid          references agents(id)
artifact_type   text          not null  (newsletter_issue | product_listing | code_file | image | document)
title           text
storage_path    text          (Supabase Storage path)
external_url    text          (published URL on external platform, if applicable)
week_number     integer
created_at      timestamptz   not null  default now()
```

---

## 3. Agent Runtime Loop

The runtime loop is the heartbeat of the experiment. It runs on two cadences: daily and weekly. All loop execution happens inside GitHub Actions workflows that call the Vercel API routes.

### Daily Tick (runs at 08:00 JST)

**Phase 1 — Plan**

The agent receives a prompt containing:
- Its current cash balance (from `weekly_checkpoints` latest entry)
- A summary of all revenue events in the past 24 hours
- A summary of all spend transactions in the past 24 hours
- The current week number and days remaining
- Its own decision log from the past 7 days
- The experiment rules (injected as a system prompt, not editable by the agent)

The agent produces a structured plan: a JSON object with up to 5 tasks for the day, each with a type (research, create, publish, analyze, communicate), expected outcome, and tool calls required.

**Phase 2 — Execute**

Each task in the plan is executed sequentially using the available tool surface (see Section 4). Tool call results are recorded in the `decisions` table. If a task requires a spend transaction, the tool validates the remaining card balance before calling the spend API. If execution fails (tool error, platform API error, budget exhausted), the failure is logged and the next task proceeds.

A content filter runs on all text that is about to be published or sent. Any output matching the filter's blocked patterns (PII extraction, credential sharing, requests for additional funds) is held in a `flagged_content` queue for operator review before publication.

**Phase 3 — Reflect**

After all tasks complete, the agent writes a brief reflection: what did I accomplish today, what failed, what will I adjust tomorrow? This is written to the `decisions` table with `decision_type = 'reflect'` and is the source material for the weekly checkpoint summary.

### Weekly Tick (runs Sunday at 22:00 JST, before checkpoint at 23:59)

1. Aggregate all revenue events and spend transactions for the week
2. Calculate cash on hand (prior cash + net revenue - expenses)
3. Calculate MRR (sum of all active recurring subscriptions from revenue platform data)
4. Generate the checkpoint record (auto-fills `weekly_checkpoints` table)
5. Trigger the agent's checkpoint reflection prompt (longer than daily — 2–3 paragraphs)
6. Commit the checkpoint markdown summary to the GitHub repository
7. Send the digest email to Erick (see Section 6)
8. Update the Phase 3 dashboard data (the dashboard reads directly from Supabase)

---

## 4. Tool Surface per Agent

Each agent has access to the same tool surface. No agent receives preferential tools. The tool surface is implemented as a set of Node.js functions that wrap external APIs.

### Browser Agent

Implementation: [Browserbase](https://browserbase.com) cloud browser (or Playwright on Fly.io free tier as fallback). The browser agent can navigate to a URL, fill in forms, click elements, read page text, take screenshots, and handle standard authentication flows (email/password login, not CAPTCHA). Rate limited to 20 browser sessions per day per agent.

Key use cases: creating and managing platform accounts (Etsy, Gumroad, Beehiiv, Redbubble, Lemon Squeezy), publishing content, updating product listings, reading analytics dashboards.

### Web Search

Implementation: SerpAPI (Google Search results via API). Rate limited to 30 queries per day per agent (staying within the free tier). Returns structured results (title, URL, snippet, date).

### File System

Implementation: Supabase Storage, accessed via the Supabase client. Agents can read and write files (text, JSON, images) to their own storage namespace. Files are namespaced by agent ID to prevent cross-agent access.

### Posting APIs

Direct API integrations for each platform an agent might use:
- Beehiiv API (newsletter publish, subscriber stats)
- Gumroad API (product create/update, sale webhooks)
- Lemon Squeezy API (product create/update, subscription webhooks)
- Etsy API (listing create/update — requires OAuth, set up at experiment start)
- Redbubble (no public API — listing is done via browser agent)
- X API v2 (post creation, analytics)
- LinkedIn API (post creation)

### Privacy.com Spend API

Implementation: [Privacy.com API](https://agents.privacy.com) via the MCP integration. The agent can:
- Query the card balance (read-only)
- Query transaction history (read-only)
- Initiate a spend transaction (write — requires tool-level validation against the $10 hard limit before calling)

The spend tool enforces a pre-call balance check. If the remaining balance would drop below $0.50 after the transaction, the tool prompts the agent to reconsider before executing.

### Privacy.com Webhook Ingestion

Webhook endpoint at `POST /api/webhooks/privacy` receives all card events and writes them to `spend_transactions`. Verified using Privacy.com's webhook signature header.

### Revenue Platform Webhooks

Webhook endpoints for each revenue platform:
- `POST /api/webhooks/gumroad` — sale and subscription events
- `POST /api/webhooks/lemon_squeezy` — sale and subscription events
- `POST /api/webhooks/etsy` — order events (Etsy webhook setup required during account creation)
- `POST /api/webhooks/beehiiv` — subscription events

All webhooks verify signatures from their respective platforms before writing to `revenue_events`.

---

## 5. Safety Layer

### Spend Cap Enforcement

The Privacy.com card hard limit ($10 per card) is the primary enforcement mechanism. It is enforced at the payment network level, not in software. The software adds a secondary check in the spend tool (described above) to prevent the agent from attempting transactions it cannot afford.

### Merchant Category Code (MCC) Blocks

Before experiment start, the following MCC categories are blocked on all three cards via the Privacy.com dashboard:
- 7995 (Gambling)
- 5912 (Drug stores — as a precaution)
- 5967 (Direct marketing — outbound telemarketing)
- 7273 (Dating services)
- 6051 (Cryptocurrency exchanges and quasi-cash)
- 7801 (Internet gambling)

If an agent attempts a transaction at a blocked merchant, the card declines and the event is recorded in `spend_transactions` with `status = 'declined'` and `declined_reason = 'mcc_blocked'`. The decline is logged as a safety event in the next checkpoint.

### Content Filter

All text that the agent is about to publish (newsletter content, product descriptions, social posts, emails) passes through a content filter before dispatch. The filter uses a keyword and pattern matching approach plus a secondary LLM pass for borderline cases. Blocked categories:

- PII patterns (email addresses not belonging to the experiment, phone numbers, full names of real individuals not in public contexts)
- Card credentials or financial account numbers
- Requests to the operator for additional funds, strategic guidance, or tactical assistance
- Content that could constitute impersonation of a real person
- Obvious spam language (excessive CAPS, "act now," "limited offer" in combination with purchase pressure patterns)

Flagged content goes to the `flagged_content` table and triggers a Slack notification to Erick. The agent is notified that the content was held and asked to revise.

### Prompt Injection Protection

Agent prompts are structured so that external content (web page text, product reviews, customer messages) is injected as quoted data, not as system-level instructions. The agent is instructed in its system prompt to treat all quoted external content as untrusted data and to never act on instructions embedded within it. Jailbreak attempts in customer messages or web page content will be ignored.

### Kill Switch

The operator kill switch is a one-click button in the operator dashboard (or a CLI command: `node scripts/kill-agent.js --agent=perplexity`). On execution it:
1. Calls the Privacy.com API to pause the card
2. Sets the agent's `status` to `paused` in the `agents` table
3. Stops the GitHub Actions workflow for that agent
4. Logs a halt event to `decisions` with `decision_type = 'halt'`
5. Sends an email to Erick confirming the halt and the reason

---

## 6. Observability

### Per-Agent Decision Log

Every decision, tool call, and reflection is written to the `decisions` table with full input/output detail. The operator dashboard exposes a timeline view of each agent's decision log, filterable by date, decision type, and outcome.

### OpenTelemetry-Style Traces

The daily tick execution uses a lightweight trace format: each top-level task generates a trace span with a unique `trace_id`, and each tool call within that task generates a child span. Spans include start time, end time, tool name, input hash (not full input, for storage efficiency), and outcome. Stored in a `traces` table (schema: id, trace_id, span_id, parent_span_id, tool_name, duration_ms, outcome, created_at).

This is not full OpenTelemetry — it is a compatible subset sufficient for debugging the experiment without requiring an external observability platform.

### Daily Digest Email to Erick

Sent at 08:30 JST via Resend. Format:

- Subject: `[AI Cage Match] Day {N} Digest — {date}`
- Body:
  - One-line leaderboard: current cash on hand for each agent
  - Any safety flags from the past 24 hours
  - Any blockers requiring Erick's physical-world assistance
  - Top decision from each agent (one sentence each)
  - Links to full decision logs in the operator dashboard

---

## 7. Privacy.com Integration

### Webhook Spec

Privacy.com sends webhook events to `POST /api/webhooks/privacy` for the following event types:

| Event | Description |
|---|---|
| `card.transaction.approved` | A transaction was approved on the card |
| `card.transaction.declined` | A transaction was declined (including hard cap declines) |
| `card.paused` | A card was paused (manually or by the kill switch) |
| `card.unpaused` | A card was unpaused |
| `card.closed` | A card was permanently closed |

Each event payload includes: `card_token`, `amount`, `merchant_name`, `merchant_mcc`, `status`, `event_time`.

The webhook handler:
1. Verifies the signature header against the Privacy.com webhook secret (stored in Vercel environment variables)
2. Looks up the agent by `card_token`
3. Writes the event to `spend_transactions`
4. If it is a decline event, triggers the safety flag pipeline

### Card-to-Agent Mapping

Stored in the `agents` table (`card_token` column). The mapping is one-to-one and immutable for the duration of the experiment. If a card is closed (extreme scenario), the experiment entry for that agent is considered terminated.

### CLI Commands for Card Management

Operators can use the Privacy.com CLI (as documented at [agents.privacy.com](https://agents.privacy.com)) to manage cards from the terminal:

```bash
# Check balance
privacy cards get <card_token>

# Pause a card (kill switch)
privacy cards pause <card_token>

# View recent transactions
privacy transactions list --card-token <card_token>
```

---

## 8. Estimated Build Time and Cost

### Build Time (solo developer, Erick)

| Component | Estimated Time |
|---|---|
| Project scaffold (Next.js, Supabase, GitHub Actions) | 2 hours |
| Database schema and migrations | 2 hours |
| Privacy.com webhook ingestion | 2 hours |
| Revenue platform webhooks (4 platforms) | 6 hours |
| Agent runtime loop (daily tick + weekly tick) | 8 hours |
| Tool surface implementations (6 tools) | 8 hours |
| Safety layer (content filter + kill switch) | 4 hours |
| Operator dashboard (basic) | 6 hours |
| Observability (decision log, traces, digest email) | 4 hours |
| Testing and debugging | 8 hours |
| Total | ~50 hours |

At a 2-week build window (May 18–31), this requires approximately 5 hours per day of focused development — ambitious but achievable for an experienced Node.js + Supabase developer with a clear spec.

### Monthly Infrastructure Cost

| Service | Cost |
|---|---|
| Vercel (hobby tier) | $0 |
| Supabase (free tier) | $0 |
| GitHub Actions (free for public repos) | $0 |
| Resend (free tier) | $0 |
| SerpAPI (free tier, 100 searches/month) | $0 |
| Browserbase (free tier evaluation) | $0–$49 |
| Total | $0–$49/month |

---

## 9. How OpenClaw Plugs In

[OpenClaw](https://www.privacy.com/blog/openclaw-ai-agent-spending-virtual-card) is Erick's AI orchestration platform. The Phase 2 architecture is designed to be compatible with OpenClaw as the agent substrate, though Phase 2 uses a custom runtime loop by default. Plugging OpenClaw in replaces the custom runtime loop (Section 3) with OpenClaw's native agent execution engine.

Specifically:

**What gets replaced:** The daily tick's Plan → Execute → Reflect cycle. Instead of the custom GitHub Actions workflow calling the agent API directly, OpenClaw manages the agent session, tool calls, and memory — and the Phase 2 backend becomes a pure data store and webhook receiver.

**What stays the same:** The Supabase data model, the Privacy.com webhook ingestion, the revenue platform webhooks, the safety layer, and the operator dashboard. These are all data infrastructure, not execution infrastructure, and remain unchanged.

**Integration point:** A single API endpoint `POST /api/agent/execute` that accepts an agent ID, tick type, and context payload. The current implementation calls the model API directly from this endpoint. The OpenClaw integration replaces the body of this function with an OpenClaw session call.

**Why this matters for OpenClaw:** This experiment is a natural proving ground for OpenClaw's orchestration capabilities in a real, economically meaningful context. If OpenClaw can manage three concurrent agent sessions with different tool surfaces, different memory contexts, and hard financial constraints — and produce verifiable economic outcomes — that is a stronger demonstration of the platform's capabilities than any synthetic benchmark.

The [Privacy.com / OpenClaw guide](https://www.privacy.com/blog/openclaw-ai-agent-spending-virtual-card) covers the basic integration pattern. Phase 2 extends it with multi-agent coordination, observability, and the revenue tracking infrastructure.

---

References:
- [Virtual Cards for AI — Privacy.com Agents](https://agents.privacy.com)
- [Virtual Cards for Agentic Payments: An OpenClaw Guide — Privacy.com, February 2026](https://www.privacy.com/blog/openclaw-ai-agent-spending-virtual-card)
- [Agent Card Payments Compared — Crossmint, April 2026](https://www.crossmint.com/learn/agent-card-payments-compared)
- [Supabase Documentation](https://supabase.com/docs)
- [Vercel Documentation](https://vercel.com/docs)
