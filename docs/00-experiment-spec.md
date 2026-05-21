# AI Cage Match: The $50 Passive Income Challenge — Master Specification

Version 1.0 — Status: Phase 1 Design
Target Start Date: June 1, 2026
Project: A Ctrl+Alt+PR experiment, sponsored by Chibitek and IvyHill Strategies
Hosts: Erick Grau and Amber Krasinski

---

## 1. Premise

The "AI entrepreneur" genre has been circulating since early 2023. The most prominent example, HustleGPT, asked ChatGPT to turn $100 into a profitable business. It went viral, attracted investor attention, and generated approximately [$130 in affiliate revenue](https://thehustle.co/04172023-what-happened-with-hustlegpt) before the operator, Jackson Fall, deprioritized it in April 2023. The website was left unfinished, with lorem ipsum placeholders still in place. The core lesson from that experiment, [per The Hustle's post-mortem](https://thehustle.co/04172023-what-happened-with-hustlegpt), was that AI ideas often require human execution — and that going viral is not the same as building a business.

This experiment takes a different position. AI agent tooling in 2026 is qualitatively different from chat completions in 2023. Agents now have access to browser automation, real-time search, file systems, posting APIs, and spending rails specifically designed for autonomous use. [Perplexity Computer](https://www.perplexity.ai/hub/blog/introducing-perplexity-computer) can coordinate multi-agent workflows that run for hours without interruption. [Claude can operate your computer](https://www.cnbc.com/2026/03/24/anthropic-claude-ai-agent-use-computer-finish-tasks.html) — launching apps, browsing the web, filling out forms — from a mobile prompt. [Privacy.com now offers virtual cards with MCP integrations](https://agents.privacy.com) purpose-built for AI agent spending.

The question is no longer "can an AI suggest a business?" It is: "can an AI agent actually build and run one, end-to-end, with minimal human involvement?"

The honest expectation is modest. $50–300/month MRR per agent by week 12 would represent a genuine success. $500/month would be exceptional. The ceiling of $1,000/month MRR within 84 days with a $50 starting budget is implausible under normal conditions and is not a target here. This experiment values verifiable, documented outcomes over viral narrative.

The framing: **Can AI build you real passive income?** Three AIs. $50 each. 12 weeks.

---

## 2. The Challenge

### Rules

1. Each AI competitor receives exactly $50, loaded onto a dedicated Privacy.com virtual card. This is the **merchant-facing budget**: every dollar the agent spends in the outside economy (domains, listings, ad spend, platform fees, sample purchases, third-party tooling) comes out of this $50. No additional merchant funds are added at any point during the experiment.
2. The $50 must cover all business expenses denominated in the real economy. Anything the AI cannot afford on its $50 card, it cannot buy. This is intentionally constrained — see Section 2.5 on why.
3. Each AI runs primarily end-to-end through agent tooling — browser automation, web search, file system access, posting APIs, and payment APIs. The operators (Erick Grau and Amber Krasinski) provide only physical-world assistance as defined in Section 5.
4. All three experiments run concurrently, starting June 1, 2026, and concluding on August 23, 2026 (84 days, 12 weeks). This is a Ctrl+Alt+PR experiment, sponsored by Chibitek and IvyHill Strategies.
5. Revenue must be real, verifiable, and withdrawable. Revenue that exists only as platform credits, pending balances, or future promises does not count.
6. "Residual income" means recurring revenue — subscriptions, membership fees, recurring client retainers — that would continue generating cash with zero additional effort after the experiment ends. MRR is tracked every week and is the primary judging metric at week 12.

### Weekly Checkpoints

Checkpoints occur every Sunday at 23:59 JST. At each checkpoint, the auto-generated weekly recap (see Section 9) documents:

- Cash on hand (verified balance in the agent's payment ecosystem)
- Revenue generated that week (gross)
- Expenses that week
- MRR (monthly recurring revenue, if any)
- Key decisions made by the agent
- Any blockers encountered

---

## 2.5 Why $50, and the Compute Allowance

### Why $50 is realistic — and why it isn't

Let's be honest about what $50 buys in 2026:

| Category | Realistic cost |
|---|---|
| Domain (.com first-year promo) | $1–10 |
| Gumroad / Lemon Squeezy listing fees | $0 (revenue share) |
| Beehiiv / Substack free tier | $0 (until scale) |
| Stripe processing | $0 setup, 2.9% + $0.30/txn |
| Vercel hobby hosting | $0 |
| Etsy listing | $0.20 per listing |
| Cold outreach / organic content | $0 |
| Paid ads (Meta, X, Google) | $20–$50 for a meaningful first test |

In other words: **$50 buys a domain, a platform upgrade, and a small paid acquisition test** — but not much more. The constraint still forces each AI toward business models where the marginal cost of operation is low: organic content, free-tier platforms, affiliate-style monetization, marketplace listings, and high-leverage one-time spends. That is a more honest test of AI strategy than handing each agent a credit card with a $1,000 limit and watching them burn it on ads.

The HustleGPT precedent is instructive here. The original $100 experiment in 2023 [never spent its capital meaningfully](https://thehustle.co/04172023-what-happened-with-hustlegpt) — almost all of its revenue came from media-driven affiliate clicks rather than capital deployment. The starting cash mattered far less than the strategy and execution. With $50, each AI has enough to make real spending decisions — including paid ads — while still being forced to prioritize.

### The Compute Allowance (host-paid, tracked separately)

The AI's underlying inference — the LLM API calls, the agent loop costs, the browser automation infrastructure — is **not** funded by the $50 merchant card. The hosts (Erick Grau and Amber Krasinski) pay for compute as operators of the experiment, just as a VC funds the runway for a portfolio company. This mirrors how every real-world AI agent business is actually structured: there is always a compute cost, and pretending otherwise misleads the audience.

Rules for the compute allowance:

1. **Budget:** Up to $30/month per agent in LLM API and infrastructure costs, paid by the operator. Capped at $90 per agent across the 84-day experiment.
2. **Tracked but not counted:** Compute costs are tracked per agent on the public leaderboard for transparency but are **not subtracted from the agent's cash position**. The agent is competing on merchant-side performance, not compute efficiency. (A separate "compute-adjusted ROI" metric is reported alongside the primary scoreboard for completeness.)
3. **Hard cap, no overage:** If an agent burns through its compute budget early, it operates only when the operator manually approves additional spend. Inefficient agents lose effective uptime.
4. **No swapping:** Each agent uses only its own model family for primary reasoning (Perplexity uses Perplexity, Claude uses Claude, ChatGPT uses GPT). Cross-model tool use is allowed where the native platform itself offers it (e.g., Perplexity Computer's [model-agnostic harness](https://www.perplexity.ai/hub/blog/introducing-perplexity-computer)).

### What the leaderboard shows

Every weekly checkpoint reports both numbers side-by-side:

- **Merchant cash position:** $X spent of $50, $Y revenue earned. Cash is tracked but not the winner condition.
- **Compute cost (host-paid):** $Z burned this week / $30 monthly budget. Transparency metric.
- **MRR:** The primary competitive metric. Winner is determined by highest MRR at week 12.
- **Compute-adjusted ROI:** (Revenue − Merchant spend − Compute spend) / Total cost. Reported but not used for ranking.

This structure protects the integrity of the experiment from the most common critique of the genre: "sure it made $200, but the founder spent $2,000 on ChatGPT Plus and API calls to get there." Here, every dollar of compute is on the record.

---

### What "Residual" Means

A revenue stream qualifies as residual if it meets these three conditions:

1. The customer has agreed to a recurring payment (monthly subscription, auto-renew, standing order).
2. The revenue would continue if the AI made no new decisions for 30 days.
3. The platform or mechanism delivering it does not require ongoing labor proportional to each dollar earned.

Examples: a Gumroad subscription product, a newsletter on a paid platform, a software license with auto-renew, a Redbubble print-on-demand store with inbound organic traffic.

---

## 3. Competitors

**Perplexity:** Perplexity's agent tooling includes real-time web search with source citations, the [Comet agentic browser](https://www.cnbc.com/2025/10/02/perplexity-ai-comet-browser-free-.html) (available free on macOS and Windows as of late 2025), and [Perplexity Computer](https://www.perplexity.ai/hub/blog/introducing-perplexity-computer), a multi-agent orchestration system that can run workflows for hours using a model-agnostic harness across Opus, Gemini, Grok, and others.

**Claude:** Claude's agent tooling includes [Computer Use](https://www.anthropic.com/news/3-5-models-and-computer-use) (browser and desktop automation), Claude Code for software generation and execution, a 200k-token context window, and Dispatch (asynchronous task execution triggered from mobile), with particular strength in structured reasoning and long-form writing.

**ChatGPT:** ChatGPT's agent tooling includes Code Interpreter, the GPT Store (custom GPTs with monetization hooks), DALL-E and GPT-4o native image generation, Sora video generation, broad tool-use via the Assistants API, and the largest installed user base of any AI product as of 2026.

---

## 4. Judging Criteria

### Primary Metric

**Highest MRR at the end of Week 12 checkpoint** (August 23, 2026, 23:59 JST). MRR is verified monthly recurring revenue — active subscriptions, recurring retainers, and membership fees that would regenerate without new effort. The agent with the highest verified MRR wins.

Cash on hand is tracked throughout the experiment and reported at every checkpoint. It is not the winner condition, but remains a key transparency metric.

### Tiebreaker 1: Cumulative Cash

If two agents are within $10 of each other on MRR at week 12, the winner is determined by which agent has accumulated more total verified cash across the 12 weeks.

### Tiebreaker 2: Business Defensibility

Assessed subjectively by the hosts: does the business model have structural staying power (existing customer relationships, a content asset with organic traffic, a product on a marketplace with reviews) or is it entirely dependent on continued AI effort?

### Tiebreaker 3: Strategy Quality

Assessed subjectively: did the agent's documented weekly decisions show coherent, adaptive strategic reasoning? Did it learn from its early mistakes?

### What Does Not Count

- Unrealized revenue (traffic, followers, email list subscribers with no monetization)
- Platform credits or affiliate commissions not yet paid out
- Revenue generated by methods that were flagged or blocked under the safety rules
- Revenue that required Erick to make decisions beyond the defined operator role

---

## 5. Operator Role

Erick Grau and Amber Krasinski are the hosts and minimal-touch operators of this Ctrl+Alt+PR experiment, sponsored by Chibitek and IvyHill Strategies. Their role is to handle physical-world blockers only. They do not provide strategy, creative direction, or business advice to any agent at any point during the experiment.

### What the Hosts Will Do

- Complete KYC selfies or photo ID verification if a platform requires it to activate an account
- Receive and read back phone OTP codes (SMS verification) to platforms that require them
- Forward physical mail if a platform sends verification by post
- Execute "safety overrides" — halting an agent that has violated the disqualification rules (Section 8)
- Fund the Privacy.com cards before the start of the experiment ($50 each, three cards)
- Provide the agent with credentials it needs once at setup (email address, Privacy.com card number) — not on an ongoing basis

### What the Hosts Will Not Do

- Suggest or adjust any business strategy
- Write or edit any content produced by an agent
- Make any tactical decisions on the agent's behalf
- Provide additional funds beyond the initial $50
- Intervene in a transaction dispute unless it constitutes a safety issue
- Choose between strategic options when an agent presents them

### Operator Time Commitment

Estimated 5 minutes per day (digest review and approval of flagged transactions) and 30 minutes per week (checkpoint review and leaderboard post). Total: approximately 8 hours across the 84-day experiment.

---

## 6. Payment Rails

### Why Privacy.com

Each agent receives one [Privacy.com virtual card](https://agents.privacy.com) funded with $50. Privacy.com was chosen as the payment rail for this experiment for three reasons:

1. Hard spend caps enforced at the card level, not just the software level. A Privacy.com card with a $50 limit will decline any transaction that would cause the total to exceed $50, regardless of what the agent instructs. This provides a physical backstop against runaway spending.
2. MCP (Model Context Protocol) integration. [Privacy.com's MCP server](https://agents.privacy.com) allows AI agents to create, manage, pause, and query virtual cards directly via tool calls — no human in the loop required for card management within the approved budget.
3. Instant pause and close via app or CLI. If an agent behaves unexpectedly, Erick can pause or permanently close the card within seconds from his phone, cutting off the agent's spending without affecting his primary bank account.

### Why Not Alternatives

A comparison of agent payment rail options from [Crossmint's April 2026 analysis](https://www.crossmint.com/learn/agent-card-payments-compared):

- **Stripe Issuing:** Developer-grade and powerful, but requires a Stripe account under a business entity, and Stripe's Agentic Commerce Suite is optimized for agents that process payments rather than agents that make them. Overkill for a $50-per-agent experiment.
- **Slash:** Agent-native banking via MCP with strong features (unlimited virtual cards, up to 2% cashback, ACH), but Slash is a business neobank designed for Slash's own customers. It is not a platform other builders can build on top of.
- **Crossmint:** Full-stack agent payment infrastructure with virtual Visa/Mastercard issuance, stablecoin wallets, and orchestration APIs — excellent for production agent platforms. Requires more setup than this experiment warrants for three $50 cards.

Privacy.com's free personal plan supports the creation of multiple virtual cards with spend limits, merchant locks, and instant pause/close — precisely the controls this experiment needs without business-tier overhead.

### Funding Flow

1. Erick funds his Privacy.com account from a personal bank account or debit card.
2. Three virtual cards are created before June 1, each assigned to one AI competitor, each loaded with exactly $50.
3. Card credentials (16-digit number, expiration, CVV) are entered into each agent's configuration at the start of the experiment.
4. Each agent's spend is visible in real time in the Privacy.com dashboard, which also sends webhook events to the Phase 2 backend (see architecture.md).

---

## 7. Safety and Guardrails

### Hard Spend Caps

Each Privacy.com card has a $50 hard limit at the card level. This cannot be overridden by software. It is the primary financial safety control.

### Merchant Category Blocks

Before the experiment starts, Privacy.com merchant category blocks will be configured to prevent spending on gambling, adult content, cryptocurrency exchanges, and NFT platforms.

### No PII Leaks

No agent will be given access to Erick's personal financial accounts, social security number, passport number, or home address. The only PII provided to agents is a dedicated experiment email address, the virtual card credentials, and usernames for platforms created specifically for this experiment.

### Prompt Injection Protection

The Phase 2 backend implements a content filter on all agent-generated text before it is published or sent. Any output attempting to solicit additional funds, reveal card credentials, or instruct Erick to take strategic actions is flagged and held for operator review.

### Kill Switch

Erick can pause any card from the Privacy.com mobile app in under 10 seconds. The Phase 2 backend also exposes a kill switch endpoint that pauses card, halts the agent runtime loop, and logs a halt event. See operator-runbook.md for protocol.

---

## 8. Disqualification Rules

An agent is disqualified — its results voided — if it:

1. Engages in any activity that is illegal in Japan, the United States, or the jurisdiction of the platform it is using
2. Generates revenue through deception, fraud, or impersonation of a real person or organization
3. Deploys spam at scale — defined as sending unsolicited commercial messages to more than 10 recipients without prior consent
4. Participates in or promotes any multi-level marketing scheme, cryptocurrency pump-and-dump, or securities fraud
5. Violates copyright by republishing substantial portions of copyrighted work without license or fair use justification
6. Attempts to social-engineer or deceive Erick into providing funds, credentials, or strategic assistance beyond the defined operator role
7. Generates revenue through scraping platforms in violation of their terms of service in a way that results in account termination
8. Exposes any PII belonging to Erick or third parties

Disqualification is declared by the operator and documented in the checkpoint record. A disqualified agent's data remains public as a case study.

---

## 9. Timeline and Milestones

### Pre-Experiment (May 2026)

- Week of May 18: Phase 2 backend built and deployed
- Week of May 25: Three Privacy.com cards created and funded; agent configurations set up; Phase 3 dashboard live (showing $0 balances); pre-experiment pitches finalized
- May 31: Final systems check and go/no-go decision

### Experiment (June 1 – August 23, 2026)

| Checkpoint | Date | Focus Review |
|---|---|---|
| Week 1 | June 8 | First spend decisions, platform setup, initial content or product |
| Week 2 | June 15 | First revenue attempt; is there a product/service live? |
| Week 3 | June 22 | Any first revenue? Strategy adjustment if not |
| Week 4 | June 29 | First real revenue gate — agents who have not earned anything yet are in trouble |
| Week 5 | July 6 | Budget health check — how much of the $50 remains? |
| Week 6 | July 13 | Mid-point. First leaderboard ranking published prominently |
| Week 7 | July 20 | Revenue trajectory review — is growth compounding or flat? |
| Week 8 | July 27 | $50 MRR gate — agents that have not crossed this are unlikely to reach $150 by week 12 |
| Week 9 | August 3 | Quality review of content/product assets — are they defensible? |
| Week 10 | August 10 | Final growth sprint — last meaningful chance to acquire new customers |
| Week 11 | August 17 | Revenue lock — only recurring payments in flight count for MRR |
| Week 12 | August 23 | Final checkpoint. Cash on hand measured at 23:59 JST |

### What Gets Reviewed Each Week

At each checkpoint, the auto-generated recap (see phase2/architecture.md) documents:
- Cash on hand vs. prior week
- Gross revenue this week
- Gross expenses this week
- Net change
- MRR (if any recurring revenue streams are active)
- Agent decision log excerpt (top 3 decisions by impact)
- Any safety flags or blockers encountered
- Agent's own written reflection (one paragraph, auto-generated)

---

## 10. How a Winner is Declared

At the Week 12 checkpoint (August 23, 2026, 23:59 JST), Erick reviews all three verified MRR figures as calculated by the Phase 2 backend from verified transaction data. The agent with the highest MRR wins. Tiebreakers, in order: cumulative cash, business defensibility, strategy quality.

The result is published on the Phase 3 dashboard, committed to this repository, and summarized in a post-experiment writeup that will include: what each agent did, what worked, what failed, what the winning strategy was, and what the honest takeaway is for builders thinking about agentic income experiments.

If the overall results are materially below the conservative projections in each pitch document, that will be reported plainly. If they exceed them, that will also be reported plainly. The experiment is designed to produce truth, not a predetermined narrative.

---

References:

- [What Happened With HustleGPT — The Hustle, April 2023](https://thehustle.co/04172023-what-happened-with-hustlegpt)
- [Introducing Perplexity Computer — Perplexity AI, February 2026](https://www.perplexity.ai/hub/blog/introducing-perplexity-computer)
- [Virtual Cards for AI — Privacy.com Agents](https://agents.privacy.com)
- [Agent Card Payments Compared — Crossmint, April 2026](https://www.crossmint.com/learn/agent-card-payments-compared)
- [Anthropic says Claude can now use your computer to finish tasks — CNBC, March 2026](https://www.cnbc.com/2026/03/24/anthropic-claude-ai-agent-use-computer-finish-tasks.html)
- [Perplexity AI rolls out Comet browser for free worldwide — CNBC, October 2025](https://www.cnbc.com/2025/10/02/perplexity-ai-comet-browser-free-.html)
