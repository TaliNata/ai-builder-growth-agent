# AI Builder Growth Agent

An autonomous AI agent that discovers AI agent builders on GitHub, qualifies them, generates personalized outreach, and matches them to open jobs on AI talent marketplaces.

---

## What it does

The agent runs a full pipeline autonomously:

1. **Discovery** — searches GitHub for active AI agent builders using keyword queries
2. **Qualification** — scores each builder on relevance, market fit, and listing readiness
3. **Outreach** — generates personalized draft messages for human review
4. **Onboarding** — creates marketplace listings from builder profiles
5. **Job Matching** — matches qualified builders to open roles
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

PostgreSQL                 ← Persistent storage
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
- **LLM:** Anthropic Claude
- **Database:** PostgreSQL (psycopg3)
- **Queue:** Redis + Celery
- **Bot:** python-telegram-bot v21
- **Deployment:** Railway
- **Discovery:** GitHub REST API

---

## Running locally

**Requirements:** Docker

```bash
git clone https://github.com/TaliNata/ai-builder-growth-agent.git
cd ai-builder-growth-agent
cp .env.example .env
# Fill in your API keys in .env
docker compose up --build
```

API will be available at `http://localhost:8000`  
Docs at `http://localhost:8000/docs`

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

## API endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/discovery/run` | Run builder discovery pipeline |
| GET | `/builders` | List all discovered builders |
| GET | `/metrics/funnel` | Funnel analytics |
| GET | `/review-queue` | Pending outreach for review |
| POST | `/review/{id}/approve` | Approve outreach message |
| POST | `/review/{id}/reject` | Reject outreach message |
| POST | `/agent/execute` | Structured agent task endpoint |
