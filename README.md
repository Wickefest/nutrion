# NutriON

**AI-powered nutrition tracking and educational insights — not medical advice.**

NutriON helps users scan drink labels, photograph meals, and manually log food to track calories, sugar, carbs, protein, fat, and hydration. Three AI agents work together: OCR reads nutrition labels, vision AI estimates food portions, and an LLM provides grounded insights from confirmed data. Medical report uploads correlate lab results with nutrition patterns — always educational, never diagnostic.

---

## Features

| Feature | Description |
|---|---|
| 📷 Drink label scan | Azure Content Understanding OCR extracts nutrition facts from labels |
| 🍽️ Food photo scan | Kimi/Moonshot Vision detects items and estimates portions (shown as ranges) |
| 🧪 Medical report upload | Extracts HbA1c, glucose, cholesterol, LDL, HDL, triglycerides |
| ✅ Confirm before save | Users review and edit every value — only confirmed data feeds analytics |
| 📊 Dashboard | Calories, sugar, protein, carbs, fat, hydration — all from real Supabase data |
| 🤖 AI assistant | Real-time LLM chat grounded in confirmed logs + educational RAG knowledge |
| 💡 Proactive AI insight | Daily insight generated without user prompting (agentic) |
| 🔄 Post-scan suggestion | AI automatically suggests lower-sugar alternatives after every confirm |
| 📅 History calendar | Color-coded days, day detail, edit/delete with undo |
| 📈 Insights (Premium) | Trend charts, sugar sources, week-over-week, correlations |
| 📋 Personal report | AI-generated PDF-ready summary with patterns + questions for a professional |
| 🔔 Notifications | Data-driven alerts based on real daily totals (not hardcoded) |
| 📲 Telegram | Auto-sends daily summary after each scan confirm |
| 🌙 Dark mode | Full dark theme with class-based toggle |
| 📱 PWA | Installable to home screen with app icon |
| 🔥 Streak | Real consecutive-day tracking from backend data |

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 16, React 19, TypeScript, Tailwind CSS 4 |
| Backend | FastAPI, SQLAlchemy (async), Pydantic |
| Database | Supabase (PostgreSQL) |
| AI - OCR | Azure Content Understanding |
| AI - Vision | Kimi/Moonshot (food photos) |
| AI - LLM | Azure OpenAI (gpt-5-mini) |
| AI - RAG | ChromaDB + approved health knowledge |

---

## Getting Started

### Prerequisites

- Node.js 18+
- Python 3.12+
- Supabase project (Postgres database)
- Azure OpenAI / Content Understanding keys
- Kimi/Moonshot API key

### Backend

```bash
cd backend
python -m venv .venv
.venv/Scripts/pip install -r requirements.txt   # Windows
# .venv/bin/pip install -r requirements.txt     # macOS/Linux

# Copy and fill environment variables
cp .env.example .env
# Edit .env with your keys

# Start the server
.venv/Scripts/python -m uvicorn app.main:app --reload --port 8000
```

### Frontend

```bash
cd frontend
npm install

# Copy and fill environment variables
cp .env.local.example .env.local
# Edit .env.local if needed (BACKEND_URL defaults to http://localhost:8000)

# Start the dev server
npm run dev
```

Open http://localhost:3000 (or :3001 if 3000 is occupied).

### Database Setup

Run in Supabase SQL Editor:

```sql
-- See backend/supabase/schema.sql for full schema
-- Tables: intakes, analyses, medical_reports, insights
```

---

## Environment Variables

### Backend (.env)

```env
DATABASE_URL=postgresql://...@....pooler.supabase.com:5432/postgres
DATABASE_SSL_VERIFY=false

AZURE_OPENAI_API_KEY=...
AZURE_OPENAI_ENDPOINT=https://....services.ai.azure.com/openai/v1
AZURE_OPENAI_CHAT_DEPLOYMENT=gpt-5-mini
AZURE_OPENAI_EMBEDDING_DEPLOYMENT=text-embedding-3-small

AZURE_CONTENT_ENDPOINT=https://....services.ai.azure.com
AZURE_CONTENT_API_KEY=...

KIMI_API_KEY=...
KIMI_BASE_URL=https://api.moonshot.ai/v1
KIMI_VISION_MODEL=kimi-k3

TELEGRAM_BOT_TOKEN=...

USE_LIVE_AI=true
CORS_ORIGINS=http://localhost:3000,http://localhost:3001
```

### Frontend (.env.local)

```env
BACKEND_URL=http://localhost:8000
```

---

## Architecture

```
┌─────────────────────────────────────────────────┐
│                   Frontend                       │
│  Next.js 16 / React 19 / Tailwind 4             │
│  Proxies /api/* and /intakes to backend         │
└──────────────────────┬──────────────────────────┘
                       │ HTTP (rewrites)
┌──────────────────────▼──────────────────────────┐
│                   Backend                        │
│  FastAPI / SQLAlchemy / Pydantic                 │
│                                                  │
│  /api/drinks/analyze  → Azure Content (OCR)     │
│  /api/foods/analyze   → Kimi Vision             │
│  /api/medical/analyze → OCR + LLM extraction    │
│  /api/ai/analyze      → RAG + Azure LLM        │
│  /api/telegram/send-summary → Telegram Bot API  │
└──────────────────────┬──────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────┐
│              Supabase (PostgreSQL)               │
│  Tables: intakes, analyses, medical_reports,    │
│          insights                                │
└─────────────────────────────────────────────────┘
```

---

## Demo Accounts

| Email | Password | Plan |
|---|---|---|
| maya@example.com | demo1234 | Premium |
| alex@example.com | demo1234 | Free |

---

## API Endpoints

### Scan & Confirm Flow
- `POST /api/drinks/analyze` — OCR drink label
- `POST /api/drinks/{id}/confirm` — Save confirmed drink
- `POST /api/foods/analyze` — Vision food estimate
- `POST /api/foods/{id}/confirm` — Save confirmed food
- `POST /api/medical/analyze` — Extract medical metrics
- `POST /api/medical/{id}/confirm` — Save confirmed metrics

### AI
- `POST /api/ai/analyze` — Grounded LLM Q&A
- `POST /api/ai/insights/daily` — Proactive daily insight
- `POST /api/ai/insights/weekly` — Weekly insight

### Analytics
- `GET /api/analytics/daily` — Daily totals
- `GET /api/analytics/weekly` — Weekly summary
- `GET /api/analytics/top-sugar-sources` — Ranked sugar contributors
- `GET /api/analytics/trends` — Nutrition trend over time
- `GET /api/analytics/completeness` — Logging completeness

### Telegram
- `POST /api/telegram/send-summary` — Send daily summary
- `POST /api/telegram/test` — Test connection

---

## Product Rules

1. Sugar tracked in grams, energy in kilocalories
2. Carbohydrates and sugar are distinct metrics
3. Never reduces next day's target after exceeding today's
4. Never diagnoses medical conditions or prescribes treatment
5. AI estimates shown as ranges — never false precision
6. Users always review and correct before saving
7. Only confirmed data feeds analytics
8. Data sources and confidence clearly labeled
9. Supportive guidance, never guilt-based messaging

---

## Team

Built during HackXperience 2026.

---

## License

Hackathon project — not for production use without proper security, auth, and compliance review.
