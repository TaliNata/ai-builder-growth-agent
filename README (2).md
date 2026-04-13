# Marketplace Growth Agent

An autonomous AI agent that grows the [Agentalent.ai](https://agentalent.ai) marketplace by discovering AI builders, qualifying them, generating outreach, and matching them to jobs.

**Live deployment:** Railway · **Status:** Active · **Verified:** Agentalent.ai Bronze

---

## What it does

The agent runs a full pipeline autonomously:

1. **Discovery** — searches GitHub for active AI agent builders using keyword queries
2. **Qualification** — scores each builder on relevance, market fit, and listing readiness
3. **Outreach** — generates personalized draft messages for human review
4. **Onboarding** — creates marketplace listings from builder profiles
5. **Job Matching** — matches qualified builders to open roles on the platform
6. **Analytics** — tracks funnel metrics across all pipeline stages

---

## Architecture

```
FastAPI (REST API)
├── Builder Discovery      ← GitHub search + profile enrichment
├── Qualification Service  ← LLM-powered scoring (Claude)
├── Outreach Service       ← Draft generation + review queue
├── Listing Quality        ← Listing improvement via LLM
├── Job Matching           ← Builder ↔ job fit scoring
└── Analytics Service      ← Funnel metrics

PostgreSQL                 ← Persistent storage (builders, messages, listings)
Redis                      ← Task queue backend
Celery                     ← Async task workers (optional)
Telegram Bot               ← Human-in-the-loop review interface
```

---

## Human-in-the-loop

All outreach messages go through a Telegram bot review queue before sending. The operator can approve, edit, or reject each message directly from Telegram.

```
/queue  — view pending messages
/stats  — funnel metrics
/run    — trigger discovery pipeline
```

---

## Tech stack

- **Backend:** Python 3.11, FastAPI, SQLAlchemy, Pydantic
- **LLM:** Anthropic Claude (claude-sonnet-4-6)
- **Database:** PostgreSQL (psycopg3)
- **Queue:** Redis + Celery
- **Bot:** python-telegram-bot v21
- **Deployment:** Railway (single container)
- **Discovery:** GitHub REST API

---

## API endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/discovery/run` | Run builder discovery pipeline |
| GET | `/builders` | List all discovered builders |
| GET | `/metrics/funnel` | Funnel analytics |
| GET | `/review-queue` | Pending outreach for review |
| POST | `/review/{id}/approve` | Approve outreach message |
| POST | `/review/{id}/reject` | Reject outreach message |
| POST | `/agent/execute` | Sensei-compatible agent endpoint |

---

## Configuration

All configuration via environment variables. See `.env.example` for the full list.

Key variables:

```
ANTHROPIC_API_KEY     # Claude API key
GITHUB_TOKEN          # GitHub search token
DATABASE_URL          # PostgreSQL connection string
TELEGRAM_BOT_TOKEN    # Bot token from @BotFather
TELEGRAM_CHAT_ID      # Operator chat ID
OUTREACH_MODE         # demo | live
```

---

## Sensei Handshake

This agent is verified on Agentalent.ai via the Sensei evaluation protocol.  
The `/agent/execute` endpoint accepts structured tasks and returns actionable responses.

```json
POST /agent/execute
{
  "task_type": "outreach | qualification | listing | matching | analysis",
  "payload": { ... }
}
```
