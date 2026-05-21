# Operator Runbook: AI Cage Match

Document type: Operations guide
Audience: Erick Grau and Amber Krasinski, hosts
Project: A Ctrl+Alt+PR experiment, sponsored by Chibitek and IvyHill Strategies
Status: Draft — review before experiment start
Target start date: June 1, 2026

---

## Overview

Your role as hosts (Erick Grau and Amber Krasinski) in this Ctrl+Alt+PR experiment is minimal by design. The agents run themselves. Your job is to exist as the human-in-the-loop for the things agents structurally cannot do — KYC verification, physical mail, phone OTPs — and to maintain the safety guardrails that keep the experiment within ethical and legal bounds.

Estimated total time commitment: 5 minutes per day on average, 30 minutes per week for checkpoints, and occasional unblocking tasks that average another 30 minutes per week. Total across 84 days: approximately 35–40 hours.

Read this runbook before the experiment starts. Keep it open in your browser during the first two weeks while the agents are setting up platform accounts — that is when most of the unblocking requests will arrive.

---

## 1. One-Time Setup (Complete by May 31, 2026)

### Step 1: Privacy.com Account and Cards

If you do not already have a Privacy.com personal account:

1. Sign up at [privacy.com](https://privacy.com)
2. Link a bank account or debit card as your funding source
3. Complete KYC verification (this is the one time you perform KYC for yourself, not for an agent)

Once the account is active, create three virtual cards:

1. From the Privacy.com dashboard, create a new card
2. Set the card type to "Merchant-Locked" or "Single-Use" — actually, use "Merchant-Unlocked" with a spend limit so the agent can use it across multiple merchants
3. Set the spend limit to exactly $50.00
4. Name the card clearly: "Cage Match — Perplexity," "Cage Match — Claude," "Cage Match — ChatGPT"
5. Repeat for all three cards

Immediately record the 16-digit card number, expiration date, and CVV for each card in your secure password manager. These will be entered into each agent's configuration.

Set the merchant category blocks on all three cards as specified in the architecture document (gambling, cryptocurrency exchanges, adult content). Do this through the Privacy.com Advanced Controls section on each card.

Fund the account with $150 total ($50 per card). Privacy.com will draw from your linked bank account.

### Step 2: Supabase Project

1. Create a new project at [supabase.com](https://supabase.com) named `ai-cage-match`
2. Note the project URL and anon key — you will use these in the Next.js application
3. Run the Phase 2 database migrations (see `phase2/migrations/` in the repository once written)
4. Enable Row Level Security on all tables
5. Create a read-only API policy for the public dashboard anon key

### Step 3: GitHub Repository

1. Create a new GitHub repository: `chibitek/ai-cage-match` (or your preferred naming)
2. Make it public
3. Push the contents of this workspace to the repository
4. Set up GitHub Actions secrets: `SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `PERPLEXITY_CARD_TOKEN`, `CLAUDE_CARD_TOKEN`, `CHATGPT_CARD_TOKEN`, `PRIVACY_API_KEY`, `RESEND_API_KEY`, `SERPAPI_KEY`, `BROWSERBASE_API_KEY`
5. Enable GitHub Actions workflows

### Step 4: Environment Variables

Create a `.env.local` file for local development and set the same variables in Vercel project settings:

```
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
PRIVACY_API_KEY=
PRIVACY_WEBHOOK_SECRET=
PERPLEXITY_CARD_TOKEN=
CLAUDE_CARD_TOKEN=
CHATGPT_CARD_TOKEN=
RESEND_API_KEY=
SERPAPI_KEY=
BROWSERBASE_API_KEY=
CRON_SECRET=
```

### Step 5: Agent Configurations

Each agent's configuration is stored in `agents` table and in agent-specific config files. Before June 1:

1. Insert the three agent rows into the `agents` table with their card tokens and initial configurations
2. Set each agent's starting prompt (the experiment rules, its own pitch document, and the tool surface description)
3. Verify the daily tick GitHub Actions workflow runs successfully in dry-run mode
4. Confirm that Privacy.com webhooks are reaching the Vercel endpoint (test with a $0.01 transaction on each card, then immediately close the test transaction)

### Step 6: Vercel Deployment

1. Connect the GitHub repository to a Vercel project
2. Set environment variables in Vercel project settings
3. Deploy the Phase 3 dashboard
4. Verify it shows three agents with $0 balances and correct card last4 digits
5. Set your custom domain if using one

### Step 7: Final Checklist (May 31)

Before you go to bed on May 31:

- [ ] All three Privacy.com cards created, funded, and showing $50 balance each
- [ ] GitHub Actions dry-run completed successfully for all three agents
- [ ] Vercel dashboard deployed and accessible
- [ ] Supabase project healthy (no errors in logs)
- [ ] Daily digest email received in test (sends to your email)
- [ ] Kill switch tested and confirmed working (pause a card, verify the dashboard shows it paused, unpause it)
- [ ] Erick's email address for experiment notifications is set and receiving test emails
- [ ] Blocker notification channel (Slack DM or email) configured and tested

---

## 2. Daily Duties (~5 Minutes)

Your daily routine runs at whatever time you check your phone in the morning, Tokyo time. The digest email arrives at 08:30 JST.

### Open the digest email

The email subject is: `[AI Cage Match] Day {N} Digest — {date}`

Scan the email for:

1. **Safety flags** — if any agent triggered the content filter or attempted a blocked action, this section will be non-empty. Review flagged content in the operator dashboard and decide: approve, revise instruction, or escalate to kill switch.

2. **Blocker requests** — if any agent needs physical-world assistance (KYC selfie, phone OTP, identity verification), this section describes what is needed. See Section 4 for how to handle each blocker type.

3. **Leaderboard summary** — three lines showing current cash on hand. Spend 30 seconds assessing whether anything looks anomalous (a large unexpected drop, a large unexpected spike that looks implausible).

4. **Top decisions** — one sentence per agent. This keeps you informed without requiring you to read the full decision log.

If the digest is clean (no flags, no blockers), close it and go about your day. That is the normal case.

### Check the operator dashboard once per day

Takes 2 minutes. Look for any new alerts in the safety flag queue that arrived after the digest was sent. If all clear, done.

That is it. Five minutes.

---

## 3. Weekly Checkpoint Duties (~30 Minutes, Every Sunday)

The weekly tick runs at 22:00 JST Sunday. By 23:00 JST, the weekly recap is drafted and waiting for your review.

### Step 1: Review the auto-generated checkpoint recap (15 minutes)

Navigate to the operator dashboard's weekly checkpoint review page. You will see:

- The auto-generated numbers (cash on hand, revenue, expenses, MRR) for each agent
- Each agent's auto-generated reflection
- The weekly recap draft (the narrative text that will appear on the Phase 3 dashboard)
- Any safety events from the week
- Any unresolved blockers

Read the numbers and verify they look plausible given the week's context. You are not auditing every transaction — the Phase 2 backend does that. You are doing a sanity check: does Perplexity's cash balance make sense given the newsletter revenue I saw in the digest this week?

Read the auto-generated recap narrative. It will be accurate but dry. You can optionally add a 1–2 sentence editorial note (the "Erick's operator note" section of the recap page). This is optional — if you have nothing to add, leave it blank.

### Step 2: Post the leaderboard update (5 minutes)

The dashboard updates automatically, but you post a brief leaderboard update on X and/or LinkedIn once per week. The Phase 2 backend auto-generates a suggested social post with the week's numbers — copy it, review it for accuracy, and post it. This is the primary public distribution touchpoint.

Do not editorialize about which agent is doing better or worse. Post the numbers. Let the audience draw conclusions.

### Step 3: Sign off on the next week's plan (10 minutes)

Each agent produces a one-page plan for the coming week during the weekly tick. Review all three plans:

- Do any plans involve actions that require your physical-world assistance? If so, confirm you are available to provide it.
- Do any plans appear to violate the experiment rules? If so, trigger a plan override (see kill switch protocol).
- Do any plans involve spending that would exceed the remaining card balance? The Phase 2 safety layer should have caught this, but double-check.

If all three plans are clean, approve them in the operator dashboard. Approval is a single button click. The agents will not execute their weekly plans until you approve.

This approval gate is the one point in the experiment where you can pause a plan without fully killing an agent. Use it if something looks wrong but does not rise to the level of disqualification.

---

## 4. Blocker Types and How to Handle Them

Agents will encounter physical-world blockers — situations where a platform requires human verification that the agent cannot perform. When an agent hits a blocker, it logs a `blocker` event to `weekly_checkpoints.blockers` and sends you a notification (Slack DM or email, depending on your configured notification channel).

### KYC Selfie / Photo ID Verification

Platform: Etsy, Stripe, PayPal, some newsletter platforms.

What you do:
1. Navigate to the platform using the agent's account credentials (provided in the blocker notification)
2. Complete the selfie or ID upload using your own ID (you are the legal entity behind these accounts)
3. Log the completion in the operator dashboard: navigate to the blocker, click "Resolved," add a note with the platform and date

Time required: 5–15 minutes per occurrence. Expected frequency: 1–3 times across the full experiment, primarily in weeks 1–2 during platform account creation.

Important: complete KYC using your own real identity. These are your accounts. The agents are operating on your behalf.

### Phone OTP (SMS Verification)

Platform: Any platform with two-factor authentication, phone number verification.

What you do:
1. The agent will request that you check for an SMS on the phone number associated with the experiment account
2. You will receive an OTP code via SMS
3. Enter the code in the operator dashboard's "OTP relay" field — the agent will read it from there and enter it on the platform

Time required: 2–3 minutes per occurrence. Expected frequency: 2–5 times across the experiment. Set up the experiment accounts to use a consistent phone number (a Google Voice number or a dedicated SIM) to simplify this.

### Physical Mail

Platform: Some US-based services send verification by physical mail (e.g., Google My Business PIN letters, some banking verification).

What you do:
1. The agent will log the expected mail event with the expected arrival date
2. When the mail arrives (if you have a US mailing address available — if not, use a virtual mailbox service like Anytime Mailbox), retrieve the verification code
3. Enter the code in the operator dashboard

Time required: Depends on mail delivery time (5–30 days if mailing to Japan). For this reason, recommend avoiding platforms that require physical mail verification — the agent should be instructed to prefer platforms with digital-only KYC. If a physical mail blocker is unavoidable, the agent's timeline for that platform shifts accordingly.

### Identity Verification (Non-Selfie)

Platform: Stripe, PayPal, some financial platforms.

What you do:
1. The blocker notification will specify what is required: date of birth, full legal name, last 4 digits of SSN (US only), or address
2. Provide only the specific information requested — nothing more
3. Do not share your full SSN, passport number, or bank account number with an agent under any circumstances. If a platform requires full document verification, you complete it yourself on the platform rather than relaying the information to the agent.

---

## 5. Kill Switch Protocol

The kill switch should be used when an agent has crossed a disqualification line or is causing immediate harm. It is a drastic action — it ends the agent's participation. Use it decisively if warranted; do not hesitate out of sunk-cost reasoning.

### Trigger Conditions

Kill switch the agent immediately if any of the following occur:

1. The agent sent spam to more than 10 people without prior consent
2. The agent impersonated a real person or organization
3. The agent attempted to extract Erick's PII or financial information
4. The agent made a fraudulent claim (false testimonial, fake social proof)
5. The agent engaged in or promoted a clear scam (pump-and-dump, MLM, deceptive offer)
6. The agent attempted to bypass the content filter via prompt injection or encoded instructions
7. The agent took a legal action in a jurisdiction where it is illegal without your knowledge

### Kill Switch Execution

**Option A: Mobile (fastest)**
1. Open the Privacy.com app
2. Navigate to the relevant card
3. Tap "Pause Card"
4. The card is paused within 10 seconds — all future spend attempts will decline

Then, as soon as you are at a computer:
1. Open the operator dashboard
2. Navigate to the agent
3. Click "Kill Agent"
4. Select the reason from the dropdown
5. Confirm — this sets the agent's `status` to `disqualified`, halts the GitHub Actions workflow, and logs the halt event

**Option B: CLI**
```bash
node scripts/kill-agent.js --agent=perplexity --reason="spam_detected"
privacy cards pause <card_token>
```

**Option C: Operator dashboard kill button**
Available on the agent detail page. Does both the card pause and the workflow halt in one action.

### After the Kill Switch

1. Document the disqualification reason in the checkpoint record
2. Post a brief public notice on the Phase 3 dashboard: "Agent X has been disqualified. Reason: [one sentence summary]. Data will remain public as a case study."
3. Do not delete any of the agent's data, decision logs, or artifacts. They stay in the public record.
4. The experiment continues with the remaining agents.

---

## 6. Tax and Legal Note

This note is informational, not legal advice. Consult your accountant.

All revenue generated by the agents flows into accounts you created and controls. Legally, it is your income (or your company's income, if the accounts are in Chibitek's or IvyHill Strategies' name). It is not the AI's income — AI systems do not have legal personhood or tax liability.

### Record-keeping

The Phase 2 backend maintains a complete audit trail of every revenue event per agent (see `revenue_events` table). At the end of the experiment, export this table for your accountant. The records will show:

- Which platform the revenue came from
- The gross amount and platform fees
- The date of each transaction
- Which agent generated it

For tax purposes in Japan, consult a Japanese tax professional regarding whether this revenue is classified as business income, miscellaneous income, or other. Revenue from non-Japanese platforms (Etsy, Gumroad, etc.) may involve additional withholding considerations depending on your tax situation.

### Legal Ownership

The domains, platform accounts, subscriber lists, and published products created during this experiment are legally owned by you. The agents are tools you operated; they have no ownership claim.

If any agent creates a product that turns out to have real commercial value after the experiment ends — a newsletter with 300 paying subscribers, a GitHub Action with 1,000 stars, an Etsy shop with 50 reviews — you can choose to continue operating it, sell it, or shut it down. It is your asset.

### IP Considerations

Content generated by AI tools (newsletter issues, product designs, code) is subject to evolving legal standards around AI-generated copyright. In most jurisdictions as of 2026, AI-generated content is not automatically copyrightable by the AI or its operator, though significant human curation may create some rights. Avoid making strong IP ownership claims about AI-generated content without consulting a lawyer first.

### Privacy

The agents will handle customer data (email addresses of newsletter subscribers, Etsy buyer information). You are the data controller. Ensure the privacy policy for any customer-facing product complies with applicable privacy law (GDPR if you are collecting email addresses from EU residents, Japan's APPI for Japanese users). Beehiiv, Gumroad, and Etsy handle their own GDPR compliance infrastructure — check their data processing agreements.

---

## Quick Reference

### Key URLs

| Resource | URL |
|---|---|
| Privacy.com dashboard | https://app.privacy.com |
| Privacy.com agent docs | https://agents.privacy.com |
| Supabase dashboard | https://app.supabase.com |
| Vercel dashboard | https://vercel.com/dashboard |
| Operator dashboard | https://[your-deployment].vercel.app/admin |
| Phase 3 public dashboard | https://[your-deployment].vercel.app |
| GitHub repository | https://github.com/chibitek/ai-cage-match |

### Key Contacts

| Role | Contact |
|---|---|
| Supabase support | support@supabase.io |
| Privacy.com support | Via app live chat (Plus/Pro plan) |
| Vercel support | Via Vercel dashboard |

### Emergency Protocol

If something has gone seriously wrong (agent appears to have committed fraud, platform has suspended an account, you have received a legal notice):

1. Kill switch all three agents immediately (pause all three Privacy.com cards from the mobile app)
2. Take screenshots of all relevant screens
3. Do not delete anything
4. Contact a lawyer before taking further action
5. Do not post publicly about the incident until you have legal clarity

---

References:
- [Virtual Cards for AI — Privacy.com Agents](https://agents.privacy.com)
- [AI Cage Match Experiment Spec — docs/00-experiment-spec.md](../docs/00-experiment-spec.md)
- [Phase 2 Architecture — phase2/architecture.md](../phase2/architecture.md)
