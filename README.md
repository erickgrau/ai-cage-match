# AI Cage Match: The $10 Residual Income Challenge

## The Pitch

Three AI systems — Perplexity, Claude, and ChatGPT — each receive a $10 Privacy.com virtual card and exactly 84 days to generate as much cash and recurring revenue as possible, running nearly end-to-end through agent tooling. There is one human operator, Erick Grau (founder of [Chibitek / ChibitekLabs](https://chibitek.com) and the [OpenClaw](https://www.privacy.com/blog/openclaw-ai-agent-spending-virtual-card) AI orchestration platform), who handles only the physical-world blockers — KYC selfies, phone OTPs, and identity verification — that no autonomous agent can yet clear. Everything else is the AI's problem. At 12 weekly checkpoints, whoever holds the most verified cash wins.

This is not a get-rich experiment. It is a structured test of what agentic AI systems can actually do in the real economy with a minimal budget and minimal human support in 2026. The genre has been overhyped before — HustleGPT generated [a modest $130 in affiliate revenue](https://thehustle.co/04172023-what-happened-with-hustlegpt) before losing steam, despite viral coverage. This experiment learns from that: it uses hard spend caps, verifiable payment rails, honest weekly accounting, and a public leaderboard. Realistic ceiling: $50–300/month MRR per agent by week 12. The goal is truth, not theater.

### A word on the $10

$10 is realistically not enough to fund a business by itself — it buys a domain and not much else. That is intentional. The $10 is the **merchant-facing budget**: what each AI can spend in the real economy. Compute (LLM API calls, infrastructure) is funded separately by the operator at up to $30/month per agent, tracked transparently on the leaderboard but not subtracted from the agent's cash position. This mirrors how every real AI agent business is structured and protects the experiment from the common critique that viral $10/$100 challenges quietly hide $1,000s in compute costs. See [docs/00-experiment-spec.md §2.5](docs/00-experiment-spec.md) for the full structure.

## Document Index

| File | What It Is |
|---|---|
| [docs/00-experiment-spec.md](docs/00-experiment-spec.md) | Master specification: rules, judging, payment rails, safety, timeline |
| [pitches/perplexity-pitch.md](pitches/perplexity-pitch.md) | Perplexity's first-person business pitch and 12-week plan |
| [pitches/claude-pitch.md](pitches/claude-pitch.md) | Claude's first-person business pitch and 12-week plan |
| [pitches/chatgpt-pitch.md](pitches/chatgpt-pitch.md) | ChatGPT's first-person business pitch and 12-week plan |
| [phase2/architecture.md](phase2/architecture.md) | Phase 2 technical build: stack, data model, agent runtime, safety layer |
| [phase3/dashboard-spec.md](phase3/dashboard-spec.md) | Phase 3 public dashboard spec: leaderboard, per-agent pages, hosting |
| [docs/operator-runbook.md](docs/operator-runbook.md) | Erick's minimal-touch operating guide: setup, daily/weekly duties, kill switch |

## Status

Phase 1 — Spec & Design (in progress). Target experiment start: June 1, 2026.

## How to Follow Along

1. Watch this repository for updates. Weekly checkpoint summaries will be committed to `docs/checkpoints/` as the experiment runs.
2. The public leaderboard (Phase 3) will be hosted on Vercel at [cagematch.ai.chibitek.com](https://cagematch.ai.chibitek.com) once Phase 2 is built.
3. Discussion and questions: open a GitHub Issue. Strategy suggestions directed at a specific AI will be ignored — the whole point is to let them figure it out.

## Stack

- Orchestration backend: Node.js, [Supabase](https://supabase.com), [Vercel](https://vercel.com), [GitHub Actions](https://github.com/features/actions)
- Agent payments: [Privacy.com virtual cards](https://agents.privacy.com) (one per AI, $10 hard limit each)
- Optional orchestration substrate: [OpenClaw](https://www.privacy.com/blog/openclaw-ai-agent-spending-virtual-card)

## License

MIT. You are free to fork and run your own version of this experiment. Attribution appreciated but not required.

---

Built by [Erick Grau](https://chibitek.com), Tokyo, Japan.
