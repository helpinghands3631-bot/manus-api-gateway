# 🔌 manus-api-gateway

> FastAPI gateway for orchestrating Manus agents with REST endpoints, WebSockets, and authentication.

[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Python 3.11+](https://img.shields.io/badge/python-3.11+-green.svg)](https://python.org)

## What Is This?

`manus-api-gateway` is the **single entry point** for the LeadOps Platform. It:

- Routes requests to the right backend service (scraper, outreach, agent, Grok)
- Handles **API key authentication** and **rate limiting**
- Exposes a unified REST + WebSocket API
- Serves as the bridge between the Manus UI and all GitHub-deployed services

## LeadOps Service Map

| Route | Backend Service | Repo |
|---|---|---|
| `POST /api/scrape` | Lead Scraper | manus-web-scraper |
| `POST /api/campaigns` | Outreach Engine | manus-sales-automation |
| `POST /api/send_batch` | Outreach Engine | manus-sales-automation |
| `GET /api/stats` | Outreach Engine | manus-sales-automation |
| `POST /api/agent` | Core Agent (ReAct) | manus-agent-core |
| `POST /api/grok` | xAI Grok LLM | api.x.ai |
| `POST /api/leads` | Lead Doctor | re-lead-doctor-api |
| `POST /api/content` | Content Generator | master-sprint-content |

## Quick Start

### 1. Clone & Configure

```bash
git clone https://github.com/helpinghands3631-bot/manus-api-gateway.git
cd manus-api-gateway
cp .env.example .env
# Edit .env with your service URLs and API keys
```

### 2. Environment Variables

```env
# Gateway config
GATEWAY_PORT=8000
GATEWAY_API_KEY=your_gateway_key_here
RATE_LIMIT_PER_MINUTE=60

# Backend service URLs
SCRAPER_URL=https://scraper.notion.locker
OUTREACH_URL=https://outreach.notion.locker
AGENT_URL=https://agent.notion.locker
DASHBOARD_URL=https://dashboard.notion.locker

# xAI Grok
XAI_API_KEY=your_xai_key_here
XAI_BASE_URL=https://api.x.ai/v1
XAI_MODEL=grok-2-latest

# Security
JWT_SECRET=your_jwt_secret_here
ALLOWED_ORIGINS=https://notion.locker,https://dashboard.notion.locker
```

### 3. Deploy with Docker

```bash
docker-compose up -d
```

### 4. Or Run Locally

```bash
pip install -r requirements.txt
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

## API Reference

### Authentication

All endpoints require an API key in the header:
```
Authorization: Bearer YOUR_GATEWAY_API_KEY
```

### Lead Scraping

```http
POST /api/scrape
Content-Type: application/json

{
  "query": "plumber",
  "location": "Melbourne",
  "max_results": 50
}
```

Response:
```json
{
  "leads": [
    {"name": "Joe's Plumbing", "phone": "+61...", "email": "...", "website": "..."}
  ],
  "total": 50,
  "source": "google_maps"
}
```

### Campaign Management

```http
POST /api/campaigns
{
  "name": "Plumbers_Melbourne_Q1",
  "segment": "A-tier",
  "templates": [{"subject": "...", "body": "..."}],
  "schedule": {"start_date": "2026-03-10", "daily_limit": 20}
}
```

### Core Agent Task

```http
POST /api/agent
{
  "goal": "Optimise campaign reply rate by 30%",
  "context": {"campaign_id": "camp_abc123", "current_reply_rate": 0.08}
}
```

### Grok LLM

```http
POST /api/grok
{
  "prompt": "Write a cold email for a plumber in Melbourne",
  "temperature": 0.8,
  "max_tokens": 200
}
```

## Architecture

```
Client / Manus UI
       |
       v
  api.notion.locker  (this gateway)
       |
  +----+----+--------+--------+
  |         |        |        |
  v         v        v        v
scraper  outreach  agent    grok
.notion  .notion  .notion  api.x.ai
.locker  .locker  .locker
```

## Tool Config

See [`leadops-tools.json`](leadops-tools.json) for the complete Manus tool definitions for all 5 services.

## Part of the Manus Ecosystem

| Repo | Description |
|---|---|
| [manus-agent-core](https://github.com/helpinghands3631-bot/manus-agent-core) | ReAct loop framework |
| [manus-web-scraper](https://github.com/helpinghands3631-bot/manus-web-scraper) | Lead scraping engine |
| [manus-sales-automation](https://github.com/helpinghands3631-bot/manus-sales-automation) | Outreach campaigns |
| **manus-api-gateway** | This repo - unified API gateway |
| [re-lead-doctor-api](https://github.com/helpinghands3631-bot/re-lead-doctor-api) | Lead enrichment and validation |
| [master-sprint-content](https://github.com/helpinghands3631-bot/master-sprint-content) | Content generation |
| [manus-telegram-agent](https://github.com/helpinghands3631-bot/manus-telegram-agent) | Telegram bot interface |

---

Built by Helping Hands 3631 | helpinghands.3631@gmail.com
