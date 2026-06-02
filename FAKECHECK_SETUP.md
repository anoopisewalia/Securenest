# SecureNest Fake News Detection Feature

Complete integration of a Python-based fake news detection microservice into the SecureNest MERN application.

## Architecture

```
┌─────────────────┐      ┌──────────────────┐      ┌─────────────────────┐
│  React Frontend │─────▶│  Node/Express    │─────▶│  FastAPI Microservice│
│  (Port 5173)    │      │  Backend         │      │  (Port 8000)         │
│                 │      │  (Port 5003)     │      │                      │
│  - FakeCheckPage│      │  - /api/fakecheck│      │  - /predict          │
│  - Autocomplete │      │  - Auth proxy    │      │  - /sources          │
└─────────────────┘      └──────────────────┘      │  - /sources/refresh  │
                                                    └─────────────────────┘
                                │                            │
                                ▼                            ▼
                         ┌──────────────┐          ┌─────────────────┐
                         │   MongoDB    │          │     Redis       │
                         │  (Port 27017)│          │   (Port 6379)   │
                         │              │          │                 │
                         │  - sources   │          │  - 24h cache    │
                         │  - predictions│         └─────────────────┘
                         └──────────────┘
```

## Features Implemented

### FastAPI Microservice (`fakecheck-api/`)
- ✅ Article fetching from URLs (newspaper3k)
- ✅ Claim extraction (heuristic: first paragraph + numeric claims)
- ✅ ClaimReview lookup (Google Fact Check Tools API)
- ✅ NewsAPI retrieval (country-filtered articles)
- ✅ NLI stance detection (HuggingFace transformers: `facebook/bart-large-mnli`)
- ✅ Redis caching (24h TTL)
- ✅ MongoDB logging (predictions + sources)
- ✅ Verdict aggregation (supports/refutes ratio → likely_real/likely_fake/not_enough_info)
- ✅ Explainable top-3 signals

### Node Backend Integration
- ✅ `/api/fakecheck` proxy route with authentication
- ✅ Internal API key security between Node ↔ Python

### React Frontend
- ✅ `/fakecheck` page with form (URL or text input)
- ✅ Country autocomplete (datalist with 15+ countries)
- ✅ State/province autocomplete (US states, India states)
- ✅ Results display: verdict, confidence %, top signals, evidence list

### Infrastructure
- ✅ Docker Compose setup (mongo, redis, fakecheck-api, backend)
- ✅ Seed script for example sources (US, India)
- ✅ Unit tests (pytest)
- ✅ Integration tests (Docker Compose + httpx)

## Prerequisites

- **Node.js** 22+ (for backend)
- **Python** 3.11+ (for microservice)
- **Docker & Docker Compose** (recommended for local dev)
- **API Keys** (optional but recommended):
  - Google Fact Check Tools API: [Get key](https://console.cloud.google.com/)
  - NewsAPI: [Get key](https://newsapi.org/)

## Quick Start (Docker Compose)

### 1. Set environment variables
Create a `.env` file in the repo root:
```bash
# Required
FAKECHECK_INTERNAL_API_KEY=dev-internal-key
JWT_SECRET=your_jwt_secret

# Optional (for full functionality)
GOOGLE_FACTCHECK_API_KEY=your_google_key
NEWSAPI_KEY=your_newsapi_key
```

### 2. Start all services
```bash
cd c:\Users\Arshpreet Kaur\Downloads\SecureNest
docker compose up --build
```

This starts:
- MongoDB (27017)
- Redis (6379)
- FastAPI microservice (8000)
- Node backend (5003)

### 3. Seed sources (optional)
```bash
docker compose exec fakecheck-api python app/seed_sources.py
```

### 4. Start frontend (separate terminal)
```bash
cd SecureNest/Chat-App/frontend
npm install
npm run dev
```
Frontend runs at http://localhost:5173

### 5. Use the feature
- Login to SecureNest
- Navigate to http://localhost:5173/fakecheck
- Enter a news URL or paste text
- Select country (and optionally state)
- Click "Check"

## Manual Setup (Without Docker)

### Backend Setup
```bash
cd SecureNest/Chat-App/backend

# Install dependencies
npm install

# Configure .env (copy from .env.example)
cp .env.example .env
# Edit .env and set:
# FAKECHECK_API_URL=http://localhost:8000
# FAKECHECK_API_KEY=dev-internal-key
# GOOGLE_FACTCHECK_API_KEY=your_key
# NEWSAPI_KEY=your_key

# Start MongoDB and Redis locally (or use cloud services)
# Then start backend
npm run dev
```

### Python Microservice Setup
```bash
cd fakecheck-api

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Set environment variables
export FAKECHECK_INTERNAL_API_KEY=dev-internal-key
export MONGO_URI=mongodb://localhost:27017/securenest
export REDIS_URL=redis://localhost:6379
export GOOGLE_FACTCHECK_API_KEY=your_key
export NEWSAPI_KEY=your_key

# Start service
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

### Frontend Setup
```bash
cd SecureNest/Chat-App/frontend
npm install
npm run dev
```

## Testing

### Unit Tests
```bash
cd fakecheck-api
pytest tests/test_predict.py -v
```

### Integration Tests
```bash
# Ensure Docker Compose is running
docker compose up -d
pytest tests/test_integration.py -v
```

### Manual API Testing
```bash
# Health check
curl http://localhost:8000/health

# Predict (via Node proxy - requires auth)
# Login first, then use browser or Postman with cookies

# Direct to Python API (for testing)
curl -X POST http://localhost:8000/predict \
  -H "X-Internal-API-Key: dev-internal-key" \
  -H "Content-Type: application/json" \
  -d '{"text":"Official government report shows GDP growth of 5%","country":"US"}'
```

## Environment Variables Reference

### Backend (Node)
| Variable | Required | Description |
|----------|----------|-------------|
| `FAKECHECK_API_URL` | Yes | URL of Python microservice (default: `http://localhost:8000`) |
| `FAKECHECK_API_KEY` | Yes | Shared secret for Node ↔ Python auth |
| `GOOGLE_FACTCHECK_API_KEY` | Optional | Google Fact Check Tools API key |
| `NEWSAPI_KEY` | Optional | NewsAPI key for article retrieval |

### Microservice (Python)
| Variable | Required | Description |
|----------|----------|-------------|
| `FAKECHECK_INTERNAL_API_KEY` | Yes | Shared secret (must match backend) |
| `MONGO_URI` | Optional | MongoDB connection string |
| `REDIS_URL` | Optional | Redis connection string |
| `GOOGLE_FACTCHECK_API_KEY` | Optional | Google Fact Check API key |
| `NEWSAPI_KEY` | Optional | NewsAPI key |
| `NLI_MODEL` | Optional | HuggingFace model name (default: `facebook/bart-large-mnli`) |
| `USE_HF_ENDPOINT` | Optional | Set to `true` for HF Inference API (not yet implemented) |

## API Endpoints

### Node Backend
- `POST /api/fakecheck` - Proxy to Python microservice (requires authentication)
- `GET /api/fakecheck/sources` - List known sources (admin, requires authentication)

### Python Microservice
- `POST /predict` - Analyze news claim/URL
- `GET /sources` - List sources (admin, requires `X-Internal-API-Key`)
- `POST /sources/refresh` - Refresh sources from NewsAPI/GDELT (admin)
- `GET /health` - Health check

## Response Format

```json
{
  "verdict": "likely_real" | "likely_fake" | "not_enough_info",
  "confidence": 0.85,
  "evidence": [
    {
      "type": "claim_review" | "article",
      "source": "FactCheck.org",
      "url": "https://...",
      "stance": "supports" | "refutes" | "neutral",
      "score": 0.92
    }
  ],
  "top_signals": [
    "3 supporting sources vs 0 refuting",
    "ClaimReview found from FactCheck.org",
    "High reliability sources detected"
  ],
  "model_version": "v1.0"
}
```

## Limitations & Disclaimers

⚠️ **Important**: This system provides automated assistance but is NOT 100% accurate. Always recommend human review for:
- Confidence scores < 0.7
- High-stakes decisions
- Politically sensitive content

### Known Limitations
- **API Rate Limits**: NewsAPI free tier = 100 requests/day; Google FactCheck = 10,000/day
- **Model Size**: Default NLI model (~1.6GB) requires significant RAM; consider lighter models for CPU-only
- **Language**: Currently English-only
- **GDELT**: Placeholder only; requires BigQuery or GDELT API setup
- **Source Reliability**: Basic weighting; no dynamic reputation scoring yet

## Troubleshooting

### Port Already in Use (5003)
```bash
# Windows: Find and kill process
netstat -ano | findstr :5003
taskkill /PID <PID> /F

# Or change port in backend/.env
PORT=5004
```

### Model Download Issues
First run downloads ~1.6GB model from HuggingFace. Ensure:
- Stable internet connection
- Sufficient disk space (~3GB free)
- Or pre-download: `python -c "from transformers import pipeline; pipeline('zero-shot-classification', model='facebook/bart-large-mnli')"`

### Redis Connection Failed
Service works without Redis but loses caching. To enable:
```bash
# Docker
docker run -d -p 6379:6379 redis:7-alpine

# Or install locally
# Windows: https://github.com/microsoftarchive/redis/releases
```

### MongoDB Connection Failed
Service works without MongoDB but loses prediction logging. To enable:
```bash
# Docker
docker run -d -p 27017:27017 mongo:6
```

## File Structure

```
SecureNest/
├── fakecheck-api/                 # Python microservice
│   ├── app/
│   │   ├── main.py               # FastAPI app + /predict endpoint
│   │   ├── retrieval.py          # Article fetch, ClaimReview, NewsAPI
│   │   ├── nli_model.py          # HuggingFace NLI stance detection
│   │   ├── cache.py              # Redis caching layer
│   │   └── seed_sources.py       # Seed script for sources
│   ├── tests/
│   │   ├── test_predict.py       # Unit tests
│   │   └── test_integration.py   # Integration tests
│   ├── Dockerfile
│   ├── requirements.txt
│   └── README.md
├── SecureNest/Chat-App/
│   ├── backend/
│   │   └── src/
│   │       ├── routes/
│   │       │   └── fakecheck.route.js  # Node proxy route
│   │       └── index.js                # Route registration
│   └── frontend/
│       └── src/
│           ├── pages/
│           │   └── FakeCheckPage.jsx   # React UI
│           ├── data/
│           │   └── countries.js        # Country/state data
│           └── App.jsx                 # Route wiring
├── docker-compose.yml            # Local dev orchestration
└── FAKECHECK_SETUP.md           # This file
```

## Next Steps / Future Enhancements

- [ ] Source reliability dynamic scoring
- [ ] GDELT integration for regional news
- [ ] Multi-language support (spaCy + mBERT)
- [ ] FEVER/LIAR dataset fine-tuning
- [ ] HF Inference API endpoint support
- [ ] Rate limiting and request throttling
- [ ] Admin dashboard for source management
- [ ] User feedback loop (thumbs up/down on verdicts)
- [ ] Export reports (PDF/JSON)

## Support & Documentation

- **FastAPI Docs**: http://localhost:8000/docs (auto-generated OpenAPI)
- **Microservice README**: `fakecheck-api/README.md`
- **Backend .env.example**: `SecureNest/Chat-App/backend/.env.example`

## License

Part of SecureNest project. See main repository for license details.
