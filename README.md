# AI App Store Screenshot Generator

> **Generate stunning, store-ready App Store & Google Play screenshots in minutes — not weeks.**
> A 4-agent AI pipeline that designs, localizes, exports, and validates screenshots across 39+ languages and every device size.

---

## 🎯 The Problem

Manual screenshot creation is one of the most expensive and time-consuming parts of app marketing:

- **Expensive** — Designers spend hours crafting each screenshot, and a single app needs 6–10 screenshots per device family.
- **Slow** — Every app update, A/B test, or seasonal campaign means re-rendering the entire set from scratch.
- **Localization nightmare** — Translating and re-laying out screenshots for 39+ App Store languages is a massive operational burden.
- **Error-prone** — Manual processes lead to inconsistent branding, wrong dimensions, and rejected submissions.

---

## ✅ The Solution: 4-Agent AI Pipeline

Our system uses a coordinated pipeline of four specialized AI agents that handle the entire screenshot lifecycle:

### 🎨 Design Agent
Generates template layouts and brand-consistent designs from your app's branding guidelines, color palette, and typography. Produces pixel-perfect compositions optimized for each store's visual patterns.

### 🌍 Localization Agent
Performs AI-powered translation across 39+ languages with cultural adaptation. Handles right-to-left (RTL) layouts, locale-specific formatting, and context-aware string selection — not just word-for-word translation.

### 📦 Export Agent
Renders screenshots at **all** required device sizes (iPhone 6.7", 6.5", 5.5", iPad 12.9", Android phones, tablets) and uploads them directly to App Store Connect and Google Play Console via their APIs.

### ✅ QA Agent
Validates every screenshot against platform guidelines — checking dimensions, safe areas, text legibility, branding consistency, and content policies — before submission. Catches rejections before they happen.

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        API Gateway (FastAPI)                     │
└──────────────────────────────┬──────────────────────────────────┘
                               │
        ┌──────────────────────┼──────────────────────┐
        ▼                      ▼                      ▼
┌───────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Design Agent │───▶│ Localization    │───▶│  Export Agent   │
│  (Templates & │    │ Agent (39+ langs│    │ (Render & Upload│
│   Branding)   │    │  + RTL support) │    │  to Stores)     │
└───────────────┘    └─────────────────┘    └────────┬────────┘
                                                      │
                                                      ▼
                                             ┌─────────────────┐
                                             │    QA Agent     │
                                             │ (Guideline      │
                                             │  Validation)    │
                                             └─────────────────┘
                                                      │
                                                      ▼
                                             ┌─────────────────┐
                                             │  Store-Ready    │
                                             │  Screenshot Set │
                                             └─────────────────┘
```

**Supporting Services:**
- **Task Queue** (Celery + Redis) — Async job processing for render farms
- **Object Storage** (S3-compatible) — Stores source assets and rendered outputs
- **PostgreSQL** — Job metadata, localization strings, and audit logs
- **LangGraph** — Orchestrates multi-agent workflows with state management

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| **Language** | Python 3.11+ |
| **API Framework** | FastAPI |
| **Agent Framework** | LangGraph |
| **LLM Integration** | OpenAI / Anthropic / Local models |
| **Image Processing** | Pillow, Playwright (headless rendering) |
| **Task Queue** | Celery + Redis |
| **Database** | PostgreSQL + SQLAlchemy |
| **Storage** | AWS S3 / MinIO |
| **Testing** | pytest |
| **CI/CD** | GitHub Actions |
| **Containerization** | Docker + Docker Compose |
| **Monitoring** | Prometheus + Grafana |

---

## 📦 Installation

### Prerequisites
- Python 3.11+
- Docker & Docker Compose (recommended)
- Redis 7+
- PostgreSQL 15+

### Option 1: Docker (Recommended)

```bash
git clone https://github.com/phanindraintelligenzit-afk/ai-app-store-screenshot-generator.git
cd ai-app-store-screenshot-generator
cp .env.example .env
# Edit .env with your API keys and credentials
docker compose up -d
```

### Option 2: Local Development

```bash
git clone https://github.com/phanindraintelligenzit-afk/ai-app-store-screenshot-generator.git
cd ai-app-store-screenshot-generator
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -e ".[dev]"
cp .env.example .env
# Edit .env with your configuration
```

### Environment Variables

```env
# LLM
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/screenshots

# Redis
REDIS_URL=redis://localhost:6379/0

# Storage
S3_BUCKET=screenshots
S3_REGION=us-east-1
AWS_ACCESS_KEY_ID=...
AWS_SECRET_ACCESS_KEY=...

# Store APIs
APP_STORE_CONNECT_KEY_ID=...
APP_STORE_CONNECT_ISSUER_ID=...
GOOGLE_PLAY_SERVICE_ACCOUNT=...
```

---

## 🚀 Quick Start

### 1. Run Database Migrations

```bash
alembic upgrade head
```

### 2. Start the API Server

```bash
uvicorn app.main:app --reload --port 8000
```

### 3. Start the Worker

```bash
celery -A app.tasks worker --loglevel=info
```

### 4. Generate Your First Screenshot Set

```bash
curl -X POST http://localhost:8000/api/v1/generate \
  -H "Content-Type: application/json" \
  -d '{
    "app_name": "My Awesome App",
    "bundle_id": "com.example.myapp",
    "primary_color": "#4A90D9",
    "screenshots": [
      {
        "title": "Discover Amazing Features",
        "description": "Find everything you need in one place",
        "device_frame": "iphone_67"
      },
      {
        "title": "Seamless Experience",
        "description": "Built for speed and simplicity",
        "device_frame": "iphone_67"
      }
    ],
    "languages": ["en", "es", "fr", "de", "ja", "zh-Hans"],
    "stores": ["app_store", "google_play"]
  }'
```

### 5. Check Job Status

```bash
curl http://localhost:8000/api/v1/jobs/{job_id}
```

---

## 📡 API Endpoints

### Core Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/v1/generate` | Submit a new screenshot generation job |
| `GET` | `/api/v1/jobs/{job_id}` | Get job status and results |
| `GET` | `/api/v1/jobs` | List all jobs (paginated) |
| `DELETE` | `/api/v1/jobs/{job_id}` | Cancel/delete a job |
| `GET` | `/api/v1/jobs/{job_id}/download` | Download rendered screenshots as ZIP |

### Design & Templates

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/v1/templates` | Create a reusable design template |
| `GET` | `/api/v1/templates` | List available templates |
| `GET` | `/api/v1/templates/{id}` | Get template details |
| `PUT` | `/api/v1/templates/{id}` | Update a template |
| `POST` | `/api/v1/templates/{id}/preview` | Generate a preview without full render |

### Localization

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/v1/languages` | List supported languages (39+) |
| `POST` | `/api/v1/translate` | Translate screenshot text |
| `GET` | `/api/v1/locales/{locale}/strings` | Get localized strings for a job |

### Export & Upload

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/v1/export` | Export screenshots for specific devices |
| `POST` | `/api/v1/upload/app-store` | Upload to App Store Connect |
| `POST` | `/api/v1/upload/google-play` | Upload to Google Play Console |
| `GET` | `/api/v1/devices` | List supported device frames |

### QA & Validation

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/v1/validate` | Validate screenshots against guidelines |
| `GET` | `/api/v1/validate/{job_id}/report` | Get QA validation report |

### Health

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/health` | Service health check |
| `GET` | `/ready` | Readiness probe |

---

## 💼 Use Cases

### 📱 Mobile App Developers
Ship faster by automating screenshot creation for every release. No more waiting on designers for version updates, bug fix releases, or hotfixes.

### 🏢 ASO Agencies
Manage screenshot production for dozens of clients simultaneously. Run A/B test variants, seasonal campaigns, and rebranding — all from a single dashboard.

### 📣 App Marketing Teams
Launch in 39+ markets on day one. Localized, store-compliant screenshots without coordinating with translators and designers across time zones.

---

## 💰 Pricing

| Plan | Cost | What's Included |
|------|------|-----------------|
| **Open Source** | **Free** | Full repo — clone, test, and deploy yourself. Community support. |
| **Done-for-You Setup** | **$1,000 – $2,000** | We deploy, configure, and integrate with your App Store Connect & Google Play accounts. Includes template customization and team training. |

> The repo is **100% free and MIT licensed**. The setup fee covers our time to get you production-ready.

---

## 🗺️ Roadmap

- [x] 4-agent pipeline architecture (Design, Localization, Export, QA)
- [x] App Store Connect & Google Play Console integration
- [x] 39+ language localization with RTL support
- [x] All device frame sizes (iPhone, iPad, Android)
- [ ] Web dashboard for visual template editing
- [ ] Figma plugin for direct design import
- [ ] A/B test variant generation
- [ ] Screenshot analytics & performance tracking
- [ ] Custom brand kit management
- [ ] Batch processing for app portfolios
- [ ] Slack/Teams notifications
- [ ] Self-hosted LLM support (fully air-gapped)

---

## 📄 License

**MIT License** — see [LICENSE](LICENSE) for details.

You are free to use, modify, and distribute this software for any purpose, including commercial projects.

---

## 🤝 Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📬 Contact

- **Issues:** [GitHub Issues](https://github.com/phanindraintelligenzit-afk/ai-app-store-screenshot-generator/issues)
- **Discussions:** [GitHub Discussions](https://github.com/phanindraintelligenzit-afk/ai-app-store-screenshot-generator/discussions)

---

Built by [AIdentify](https://github.com/phanindraintelligenzit-afk/AIdentify) — AI Automation Marketplace.
