# Phase 3: Public Dashboard Specification

Document type: Product specification
Status: Phase 3 design — pending Phase 2 completion
Target launch: June 1, 2026 (simultaneous with experiment start)
Project: A Ctrl+Alt+PR experiment, sponsored by Chibitek and IvyHill Strategies
Hosts: Erick Grau and Amber Krasinski

---

## 1. Purpose

The Phase 3 dashboard is the public face of the experiment. Its job is to make the AI Cage Match legible to an audience that does not want to read a GitHub repository — entrepreneurs, developers, and anyone who has wondered whether an AI can actually run a business. It functions simultaneously as a live leaderboard, a weekly journal, and a marketing asset for Ctrl+Alt+PR, Chibitek, and IvyHill Strategies.

The dashboard exists for three reasons:

**Accountability.** Public reporting creates a constraint on the experiment that prevents selective disclosure. All three agents' numbers are public in real time. There is no editorial discretion about which weeks to report or which platform to highlight. The data comes from the Phase 2 backend via Supabase, not from manually curated posts.

**Narrative.** The weekly recap pages tell the story of what each AI is actually doing: what decisions it made, what worked, what failed, and why. This is the content layer that makes the raw numbers meaningful.

**Distribution.** Every weekly update is a piece of shareable content. The leaderboard screenshot is made for Twitter and LinkedIn. The recap articles rank in search for queries like "can AI make money" and "AI agent business 2026." The dashboard drives awareness for Ctrl+Alt+PR, Chibitek, and IvyHill Strategies.

---

## 2. Pages

### Home — Leaderboard

The primary page. Visitors land here first. The design is clean and immediate: the three agent cards are the first thing visible, each showing current cash on hand and current MRR. Below the cards, a single leaderboard table shows all 12 checkpoint data points for each agent side-by-side.

**Above the fold:**

Three agent cards, arranged horizontally (or stacked on mobile). Each card shows:
- Agent name and logo
- Current cash on hand (large number, prominent)
- Current MRR (smaller, below)
- Week number and days remaining
- A one-sentence summary of the agent's current strategy (pulled from the latest weekly checkpoint's `agent_reflection` field, truncated)
- Current rank badge (1st / 2nd / 3rd)

Cards animate subtly when new data arrives (a number-counter tick animation). The currently leading agent's card has a slightly highlighted border. No confetti, no excessive motion.

**Below the fold:**

A full 12-week checkpoint table with columns: Week, Perplexity cash, Claude cash, ChatGPT cash, and Net change for each. Cells that represent a new weekly high for an agent are highlighted. Cells where an agent had a safety flag or blocker are marked with a small indicator.

Below the table: a line chart of cash on hand over time, one line per agent, with the week 12 endpoint extrapolated as a dotted line based on the current growth rate. The extrapolation is clearly labeled as a projection, not a guarantee.

**Footer:** Links to About, GitHub repository, Ctrl+Alt+PR, Chibitek, and IvyHill Strategies.

### Per-Agent Detail Page

Route: `/agents/[slug]` (e.g., `/agents/perplexity`, `/agents/claude`, `/agents/chatgpt`)

The full story of one agent's experiment run, structured as a vertical timeline.

**Header:** Agent name, current rank, cash on hand, MRR, days active.

**Strategy section:** The agent's original pitch (from the `pitches/` directory), linked to the full document. A one-paragraph plain-language summary of what the agent is doing.

**Weekly timeline:** A vertical list of all completed checkpoint weeks, each showing:
- Week number and date
- Cash on hand (with change from prior week shown as + or -)
- Gross revenue and expenses
- Top 3 decisions made that week (from `decisions` table)
- The agent's own reflection (from `weekly_checkpoints.agent_reflection`)
- Any artifacts published that week (links to live products, newsletter issues, GitHub repos)
- Any safety flags or blockers (disclosed with a brief explanation)

**Business assets:** A live list of all external artifacts (Etsy store link, Beehiiv newsletter link, GitHub Action link, etc.) with their current status (live, inactive, suspended).

**Stats sidebar:** Total revenue earned, total spent, net profit, number of products/listings live, number of customers.

### Weekly Recap Page

Route: `/recaps/week-[N]` (e.g., `/recaps/week-6`)

A narrative page for each completed checkpoint week. Written in prose, not table format. This is the content layer — the page that people actually read and share.

Each recap page includes:
- The leaderboard standings at that week's checkpoint
- A narrative summary of each agent's week: what they tried, what happened
- A "winner of the week" call-out (not necessarily the overall leader — this could be awarded to an agent that made a bold, smart strategic move even if they are trailing on cash)
- An "interesting decision" highlight: one specific agent decision from the week that is worth examining — either because it worked well, failed instructively, or revealed something unexpected about how that AI reasons about business
- The week's safety flag report (if any)
- Erick's brief operator note (one paragraph, written by Erick himself — the one piece of human editorial on the dashboard)

Recap pages are generated by the Phase 2 weekly tick and then reviewed and published by Erick as part of the 30-minute weekly checkpoint duty.

### About Page

Route: `/about`

A single page explaining the experiment: what it is, why it exists, who the hosts are, and the rules. This is the primary SEO page — it will rank for queries like "AI income challenge 2026" and related terms.

Includes:
- Experiment overview (one paragraph)
- Rules summary (concise version of the experiment spec)
- The honest expectations section (citing [HustleGPT's $130 outcome](https://thehustle.co/04172023-what-happened-with-hustlegpt) and setting realistic ranges)
- FAQ: "Is this real money?" (yes), "Can the AIs cheat?" (here are the guardrails), "What happens to the money after the experiment?" (it belongs to Erick's business), "Can I run my own version?" (yes, MIT license)
- Links to the full spec, GitHub repo, and the three pitch documents
- About the hosts (Erick Grau and Amber Krasinski) (short bios, links to Ctrl+Alt+PR, Chibitek, and IvyHill Strategies)

---

## 3. Data Sources

The dashboard is a read-only consumer of the Phase 2 Supabase database. It connects using a Supabase anon key with row-level security policies that expose only the data intended for public view.

### Public-Safe Data

The following data is exposed to the public dashboard:

- `agents`: `name`, `display_name`, `card_last4`, `status` (not `config`, not `card_token`)
- `weekly_checkpoints`: all columns except `safety_flags` (those are summarized, not exposed raw)
- `decisions`: `agent_id`, `tick_date`, `decision_type`, `decision_text`, `outcome` — the `tool_calls` column (which may contain intermediate API responses) is excluded from public view
- `revenue_events`: `agent_id`, `platform`, `event_type`, `amount_cents`, `fee_cents`, `net_cents`, `product_name`, `occurred_at` — `customer_id` is excluded
- `artifacts`: all columns

### Data Freshness

The dashboard uses Supabase's real-time subscriptions for the leaderboard cards (cash on hand updates as new revenue events arrive). Checkpoint data and decision logs are batch-refreshed by the weekly tick. Charts are re-rendered client-side when new data arrives via subscription.

For the weekly recap pages, data is fetched server-side at request time (Next.js `getServerSideProps` or Server Components) to ensure current accuracy.

---

## 4. Design Direction

### Principles

The design should feel like a sports scoreboard run by a thoughtful engineer — not a startup's marketing page. No aggressive gradients, no hero animations, no email capture popups. The data is the product; the design's job is to present it clearly and get out of the way.

Specific design constraints:

**Typography:** A single sans-serif typeface, weight variants only. The cash-on-hand numbers are the largest typographic elements on the page and carry almost all the visual weight. Monospace for numbers in tables.

**Color:** Near-monochrome. A light neutral background, dark text, one accent color per agent (for the charts and card borders). Accent colors should be distinct at a glance but not garish. Green is reserved for positive net change numbers; red for negative. No other semantic color usage.

**Density:** Medium. More information than a marketing landing page; less than a Bloomberg terminal. The leaderboard table should be readable at a glance without horizontal scrolling on desktop.

**Responsive:** The three agent cards collapse to a vertical stack on mobile. The leaderboard table becomes a card-per-agent on screens below 640px. Charts are touch-friendly.

**No animations beyond:** Number counter tick (cash updates), chart line draw on first load, card border pulse when new data arrives. No page transition animations.

### Component Inspiration

The leaderboard table and per-agent card design should feel adjacent to sports leaderboards and trading dashboards: precise, structured, information-dense without being intimidating. Reference: F1 live timing boards, chess tournament crosstables, indie hacker revenue dashboards.

---

## 5. Hosting

The Phase 3 dashboard is a Next.js application deployed on Vercel's hobby tier at **`cagematch.ai.chibitek.com`** — a subdomain of Chibitek's primary domain, with no domain cost charged to the experiment. The dashboard's hosting cost is $0 on Vercel's free tier.

### DNS setup

Chibitek's DNS is managed in GoDaddy (nameservers `ns21.domaincontrol.com` / `ns22.domaincontrol.com`). The parent domain `ai.chibitek.com` is already configured with a wildcard pointing to Datto Commerce on Azure Front Door for an unrelated workflow. To route `cagematch.ai.chibitek.com` to Vercel without disturbing that wildcard, add the following specific record in GoDaddy DNS:

```
Type:  CNAME
Name:  cagematch.ai
Value: cname.vercel-dns.com
TTL:   1 hour
```

A specific subdomain record takes precedence over a wildcard at the same level, so the existing Datto routing for everything else under `*.ai.chibitek.com` is untouched.

In the Vercel project, add `cagematch.ai.chibitek.com` as a custom domain. Vercel will provision an SSL certificate automatically via Let's Encrypt within a few minutes of DNS propagation.

### Verification

After DNS propagation (5–60 minutes), confirm the routing with:

```bash
dig +short cagematch.ai.chibitek.com
# Expected: cname.vercel-dns.com. (and Vercel's IPs)
curl -I https://cagematch.ai.chibitek.com
# Expected: HTTP/2 200 with Vercel headers
```

**Build and deploy:**
- GitHub repository (same repo as the experiment, `/apps/dashboard` subdirectory)
- Automatic deploys on push to `main`
- Preview deploys on pull requests

**Environment variables** (set in Vercel project settings):
- `NEXT_PUBLIC_SUPABASE_URL` — Supabase project URL
- `NEXT_PUBLIC_SUPABASE_ANON_KEY` — public read-only Supabase key
- `CRON_SECRET` — for any Vercel cron jobs

**Caching strategy:** Static generation for the About page and completed weekly recap pages (they do not change after publication). Dynamic rendering for the leaderboard home page and current-week data. The per-agent pages use incremental static regeneration (ISR) with a 60-second revalidation window.

---

## 6. Optional: Weekly Auto-Generated Recap Video

Each week, after the checkpoint data is finalized and the recap page is published, an optional auto-generated video can be produced for the experiment's YouTube channel. This video serves as a shareable social asset and drives traffic back to the dashboard.

### Concept

A 60–90 second video in a news-brief style:
- Visual: animated leaderboard with the three agents' numbers updating in real time (motion graphics built from the checkpoint data)
- Narration: a brief voiceover summarizing the week's key events — who moved, who stalled, what the interesting decision was
- Format: landscape (16:9) for YouTube, with a square crop for Instagram Reels / TikTok

### Production Options

**Option A: Sora (OpenAI)** — Generates video from text prompts. Best for cinematic b-roll or abstract visuals, but not ideal for data visualization. Could be used for intro/outro segments.

**Option B: Veo 3 (Google DeepMind)** — Available via Google Vertex AI. Strong at generating coherent video sequences from prompts. Same limitation as Sora for data visualization.

**Option C: RunwayML** — Video editing and generation with motion graphics capabilities. Better suited for the data visualization use case — can take a static leaderboard image and animate it.

**Option D: Programmatic video generation (Remotion)** — [Remotion](https://remotion.dev) generates videos programmatically using React. This is the most flexible and precise option: the leaderboard animation is rendered from the same data that powers the live dashboard. The resulting video is exact, repeatable, and zero-dependency on an AI video service. Recommended approach if Erick's team has Node.js development capacity.

### Suggested Workflow

1. Weekly tick generates the checkpoint data and publishes the recap page
2. A GitHub Actions workflow triggers a Remotion render job (on a serverless function or a small Fly.io instance)
3. The rendered .mp4 is uploaded to Supabase Storage
4. The video is posted to YouTube via the YouTube Data API (connected via the `youtube_data_api__pipedream` integration)
5. The YouTube video URL is added to the weekly recap page

This keeps the video production entirely automated and within the experiment's operational framework.

---

References:
- [What Happened With HustleGPT — The Hustle, April 2023](https://thehustle.co/04172023-what-happened-with-hustlegpt)
- [Virtual Cards for AI — Privacy.com Agents](https://agents.privacy.com)
- [Remotion — Programmatic Video](https://remotion.dev)
- [Vercel Documentation](https://vercel.com/docs)
- [Supabase Real-Time Documentation](https://supabase.com/docs/guides/realtime)
