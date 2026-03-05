# Technical Setup & Infrastructure Guide
## Chinese Recipe Assistant - MVP 1.0

**Version:** 1.0  
**Date:** March 5, 2026  
**Audience:** Solo developer reference

---

## 1. Cost Strategy: Free Tier First

### Frontend — GitHub Pages ($0)
- Static hosting, unlimited bandwidth for public repos
- Custom domain supported (CNAME)
- No cold starts — CDN-served instantly

### Backend — Render Free Tier ($0)
- 750 free hours/month (enough for 1 service running ~24/7)
- **Critical limitation:** Service spins down after **15 minutes of inactivity**
- Cold start on first request after idle: **30–50 seconds**
- Mitigation strategy: add a "wake-up ping" from the frontend on app load (`GET /health`)
  so by the time the user types and submits, the backend is warm
- For MVP with low traffic, this is acceptable; upgrade to $7/month paid tier when DAU > 20

### OpenAI (GPT-4o-mini) — Minimize via RAG
See Section 3 for full RAG token strategy.  
Estimated cost at MVP scale: **$0–5/month** with RAG properly implemented.

### Total MVP Cost: $0/month (until scale demands it)

---

## 2. Repository Structure — Monorepo (Recommended)

### Why Monorepo over Two Separate Repos

| | Monorepo | Two Repos |
|---|---|---|
| Copilot sees all code | ✅ Yes (one workspace) | ❌ No (one at a time) |
| Shared types/constants | ✅ Easy | ❌ Must duplicate or publish package |
| Single git history | ✅ Yes | ❌ Fragmented |
| Deploy complexity | Medium (two pipelines) | Medium (two repos to manage) |
| Solo dev ergonomics | ✅ Better | ❌ More overhead |

**Use branches for code history, not for splitting projects.**  
Branches are not a good way to separate frontend/backend — they replace each other's code.

### Recommended Structure

```
recipe-ai/                        ← single GitHub repo
│
├── frontend/                     ← Next.js app
│   ├── app/                      ← Next.js App Router pages
│   ├── components/
│   ├── lib/                      ← API client, localStorage helpers
│   ├── public/
│   ├── package.json
│   └── next.config.js
│
├── backend/                      ← FastAPI app
│   ├── main.py                   ← FastAPI entry point
│   ├── routers/                  ← plan_meal.py, shopping_list.py, etc.
│   ├── services/                 ← chromadb_service.py, openai_service.py
│   ├── data/                     ← recipes JSON + ChromaDB storage
│   ├── scripts/                  ← verify_youtube.py, seed_db.py
│   ├── requirements.txt
│   └── render.yaml               ← Render deployment config
│
├── .github/
│   └── workflows/
│       └── deploy-frontend.yml   ← GitHub Actions: build & deploy to Pages
│
└── README.md
```

### Why This Works for Copilot
Open the entire `recipe-ai/` folder as your VS Code workspace.  
Copilot reads all files — frontend and backend — in one session.  
No multi-root workspace setup needed.

---

## 3. RAG Architecture — Reducing OpenAI Token Usage

### The Problem Without RAG
Sending all 500 recipes to OpenAI per query:
- ~500 recipes × ~200 tokens each = **~100,000 tokens per query**
- At GPT-4o-mini pricing: ~$0.015 per query → **$15/day at 1,000 queries** — unsustainable

### The Solution: RAG (Retrieval-Augmented Generation)

```
User query: "Sichuan dinner, medium spicy"
           │
           ▼
[1] Embed query → vector (all-MiniLM-L6-v2, runs locally, FREE)
           │
           ▼
[2] ChromaDB similarity search → top 10-15 relevant recipes retrieved
    (500 recipes in DB, only 10-15 returned)
           │
           ▼
[3] Build prompt with ONLY those 10-15 recipes (~3,000 tokens total)
    + user taste profile (~100 tokens)
    + meal history (~100 tokens)
           │
           ▼
[4] Send to OpenAI GPT-4o-mini (~3,200 tokens input)
    Cost: ~$0.0005 per query
           │
           ▼
[5] OpenAI selects best 3 from the 10-15 candidates
    Returns structured JSON (~500 tokens output)
```

### Token Budget Per Query

| Component | Tokens | Notes |
|---|---|---|
| System prompt | ~300 | Role, instructions, output format |
| Retrieved recipes (10–15) | ~2,500 | ~200 tokens each |
| Taste profile | ~100 | Injected from localStorage |
| Meal history (avoid list) | ~100 | Last 7 days, recipe names only |
| **Total input** | **~3,000** | vs 100,000 without RAG |
| Output (3 suggestions) | ~500 | Structured JSON |
| **Cost per query** | **~$0.0005** | GPT-4o-mini pricing |

At 1,000 queries/day: **~$0.50/day = ~$15/month** worst case.  
At MVP scale (30 DAU × 3 queries): **~$1.35/month**.

### ChromaDB Setup (Free, Runs Locally on Render)
- Embedded mode — no separate server, runs inside the FastAPI process
- Data persisted to disk (`/data/chromadb/`)
- On Render free tier: use a **persistent disk** (Render provides 1GB free)
- Embeddings generated once at setup using `sentence-transformers` (local, no API cost)
- Re-embed only when new recipes are added

---

## 4. Deployment Pipelines

### Frontend → GitHub Pages

**How it works:**
- Next.js configured for static export (`output: 'export'` in `next.config.js`)
- GitHub Actions builds and deploys on every push to `main`
- Served at: `https://{your-username}.github.io/recipe-ai/`
- Or custom domain: `recipes.yourdomain.com`

**Limitation:** GitHub Pages only serves static files.  
Next.js static export works fine for this app since all dynamic data comes from the FastAPI backend via API calls. No Next.js server-side features (SSR/API routes) are used.

**`deploy-frontend.yml` (what it does):**
```
On push to main:
  1. cd frontend/
  2. npm install
  3. npm run build       → generates /frontend/out/ (static files)
  4. Deploy /out/ to gh-pages branch → GitHub Pages serves it
```

### Backend → Render

**How it works:**
- Render detects `backend/render.yaml` and auto-deploys on push to `main`
- Or manually connect the repo in Render dashboard, set root directory to `backend/`
- Runs: `uvicorn main:app --host 0.0.0.0 --port $PORT`

**`render.yaml` (Render config):**
```yaml
services:
  - type: web
    name: recipe-ai-backend
    runtime: python
    rootDir: backend
    buildCommand: pip install -r requirements.txt
    startCommand: uvicorn main:app --host 0.0.0.0 --port $PORT
    envVars:
      - key: OPENAI_API_KEY
        sync: false          # set manually in Render dashboard (never commit keys)
    disk:
      name: chromadb-data
      mountPath: /data
      sizeGB: 1              # free persistent disk
```

### Environment Variables
| Variable | Where Set | Notes |
|---|---|---|
| `OPENAI_API_KEY` | Render dashboard (secret) | Never commit to git |
| `NEXT_PUBLIC_API_URL` | GitHub Actions secret or `frontend/.env.production` | Points to Render backend URL |
| `CHROMA_PERSIST_PATH` | Render env var | `/data/chromadb` |

---

## 5. CORS Configuration

Since frontend (GitHub Pages domain) and backend (Render domain) are on different origins,
FastAPI must allow cross-origin requests:

```python
# backend/main.py
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=[
        "https://{your-username}.github.io",
        "http://localhost:3000",   # local development
    ],
    allow_methods=["GET", "POST"],
    allow_headers=["Content-Type"],
)
```

---

## 6. Local Development Setup

Both services run simultaneously on your machine:

```bash
# Terminal 1 — Backend
cd backend/
pip install -r requirements.txt
uvicorn main:app --reload --port 8000

# Terminal 2 — Frontend
cd frontend/
npm install
npm run dev          # runs on localhost:3000
                     # API calls go to localhost:8000 (set in .env.local)
```

**`frontend/.env.local`** (git-ignored):
```
NEXT_PUBLIC_API_URL=http://localhost:8000
```

**`frontend/.env.production`** (committed, safe — no secrets):
```
NEXT_PUBLIC_API_URL=https://recipe-ai-backend.onrender.com
```

---

## 7. Render Free Tier — Cold Start Mitigation

The free tier sleeps after 15 minutes idle. Strategy to hide this from users:

```javascript
// frontend/lib/api.ts
// Fire a wake-up ping when the app loads, before any user action
export async function wakeUpBackend() {
  fetch(`${process.env.NEXT_PUBLIC_API_URL}/health`, { method: 'GET' })
    .catch(() => {})  // silent fail — just warming up
}

// Call in root layout on mount
useEffect(() => { wakeUpBackend() }, [])
```

The `/health` endpoint returns `{"status": "ok"}` in ~1ms once warm.  
By the time a new user completes their onboarding (5 questions) or types a query, the backend is ready.

---

## 8. Summary

| Concern | Solution | Cost |
|---|---|---|
| Frontend hosting | GitHub Pages (static export) | $0 |
| Backend hosting | Render free tier | $0 |
| LLM costs | OpenAI GPT-4o-mini + RAG (top 15 recipes only) | ~$1-5/month |
| Vector DB | ChromaDB embedded + Render persistent disk | $0 |
| Cold starts | Wake-up ping on app load | $0 |
| Monorepo + Copilot | Single repo, `frontend/` + `backend/` folders | $0 |
| Secrets management | Render dashboard env vars, GitHub Actions secrets | $0 |
| **Total** | | **~$1-5/month** |

Upgrade path: when traffic grows, switch Render to $7/month paid (no cold starts, more RAM).

---

*This document covers infrastructure decisions not in the main PRD.  
See [RECIPE_ASSISTANT_PRD_MVP1.md](./RECIPE_ASSISTANT_PRD_MVP1.md) for feature and product requirements.*
