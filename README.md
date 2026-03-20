# Brand Guide AI

An AI-powered brand consistency platform built by **Humanitarians AI** — a nonprofit initiative that creates real-world AI tools for social good while training the next generation of AI practitioners.

---

## Why This Exists

Nonprofits pour enormous effort into developing brand guidelines — color palettes, typography systems, logo rules, voice and tone standards — yet most lack the resources to enforce them consistently across materials. A single poorly-branded flyer, off-color social post, or wrong-font presentation erodes credibility with donors, partners, and communities.

Enterprise brand tools exist but cost thousands of dollars per year and require dedicated brand managers to operate. For the 1.5 million nonprofits in the US alone, that's a non-starter.

**Brand Guide AI** solves this by automating brand audits. Upload your brand guidelines once; the system learns your colors, fonts, logo rules, and voice standards. Then run any document or image through the auditor — it flags violations, scores compliance, and shows exactly what's wrong and where.

---

## Who This Is For

### Nonprofits
Small-to-mid-sized organizations that need professional brand consistency without a dedicated brand team. Whether you're reviewing a grant proposal, a social media asset, or an annual report, Brand Guide AI checks it against your standards in seconds.

### Students & Emerging Practitioners
Every audit run on this platform is a real capstone project. Students at Humanitarians AI partner with nonprofits, run real audits, and build portfolio artifacts demonstrating AI for Good impact. The codebase itself is a learning environment — combining computer vision, NLP, and full-stack web development in a single deployable application.

### Educators
A structured codebase ready for coursework assignments around ML pipelines, REST API design, containerized deployment, and responsible AI in the nonprofit sector.

---

## What It Does

- **Brand Kit Extraction** — Upload a brand guideline PDF; the system extracts colors (hex/RGB/CMYK), fonts, logo rules, and voice/tone attributes using OCR and an LLM.
- **Document Auditing** — Upload any PDF or image; the auditor compares it against a brand kit and surfaces violations with page coordinates, severity levels, and fix suggestions.
- **Visual Compliance Viewer** — Violations are overlaid on a PDF viewer so you see exactly which element on which page is out of spec.
- **Compliance Scoring** — Each audit produces a 0–100 score and a status (COMPLIANT / ACTION REQUIRED / CRITICAL).
- **Multi-Element Coverage** — Logo placement and sizing, color palette adherence, typography hierarchy, imagery style, spacing, and voice/tone alignment.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 19, TypeScript, Vite, TailwindCSS 4, Radix UI |
| Backend | Python 3.10, FastAPI, SQLModel, asyncpg |
| Database | PostgreSQL 16 |
| Computer Vision | OpenCV (SIFT), CLIP (HuggingFace), Pillow, scikit-learn |
| OCR & PDF | Tesseract, PyMuPDF, pdf2image |
| LLM | Google Gemini via LiteLLM |
| Deep Learning | PyTorch (Siamese network for font matching), BART |
| Infra | Docker, Docker Compose, Nginx |

---

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (includes Docker Compose)
- A [Google Gemini API key](https://aistudio.google.com/app/apikey)

For local development without Docker:
- Python 3.10+
- Node.js 20+
- PostgreSQL 16
- System packages: `poppler-utils`, `tesseract-ocr`, `libgl1` (Linux) or equivalents on macOS via Homebrew

---

## Quick Start (Docker — Recommended)

### 1. Clone the repository

```bash
git clone https://github.com/humanitarians-ai/brandguideai.git
cd brandguideai
```

### 2. Configure environment variables

```bash
cp .env.example .env
```

Edit `.env` and set your values:

```env
GEMINI_API_KEY=your_gemini_api_key_here
LLM_MODEL=gemini/gemini-2.0-flash

POSTGRES_USER=user
POSTGRES_PASSWORD=password
POSTGRES_DB=brandguide
DATABASE_URL=postgresql+asyncpg://user:password@db:5432/brandguide

BACKEND_URL=localhost:8000
FRONTEND_URL=localhost:5173
```

> **Note**: Never commit your `.env` file. It's already in `.gitignore`.

### 3. Start the application

```bash
docker compose up --build
```

The first build downloads ML models and installs dependencies — expect 5–10 minutes. Subsequent starts are fast.

| Service | URL |
|---|---|
| Frontend | http://localhost:5173 |
| Backend API | http://localhost:8000 |
| API Docs | http://localhost:8000/docs |

### 4. Stop the application

```bash
docker compose down
```

To also remove the database volume (destroys all data):

```bash
docker compose down -v
```

---

## Local Development Setup

Use the dev compose file for hot-reload on both frontend and backend:

```bash
docker compose -f docker-compose.dev.yml up --build
```

This mounts `./backend/src` and `./frontend/src` as volumes so code changes are reflected immediately without rebuilding.

### Running services individually

**Backend**
```bash
cd backend
pip install -e ".[dev]"
uvicorn src.api:app --reload --port 8000
```

**Frontend**
```bash
cd frontend
npm install
npm run dev
```

**Database** (Docker only)
```bash
docker run -d --name brandguide_db \
  -e POSTGRES_USER=user \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_DB=brandguide \
  -p 5432:5432 \
  postgres:16-alpine
```

---

## Running Tests

```bash
cd backend
pytest tests/ -v
```

Run a specific test file:
```bash
pytest tests/test_color_audit.py -v
```

---

## Project Structure

```
brandguideai/
├── backend/
│   ├── src/
│   │   ├── api.py                        # FastAPI routes
│   │   ├── models.py                     # Database & response models
│   │   ├── database.py                   # Async PostgreSQL setup
│   │   ├── config.py                     # Environment config
│   │   ├── brand_auditor.py              # Core audit engine
│   │   ├── brand_guideline_extractor.py  # PDF → brand spec extraction
│   │   ├── brand_guideline_generator.py  # Brand spec from raw assets
│   │   ├── asset_classifier.py           # CLIP-based asset tagging
│   │   ├── layout_classifier.py          # Page layout analysis
│   │   ├── typography/                   # Font detection (Siamese network)
│   │   └── services/ml_service.py        # ML model singleton loader
│   ├── data/models/                      # Trained model weights
│   ├── tests/                            # Pytest test suite
│   └── uploads/                          # Uploaded files (mounted volume)
├── frontend/
│   ├── src/
│   │   ├── App.tsx                       # App shell & state
│   │   ├── types.ts                      # TypeScript interfaces
│   │   ├── components/                   # UI components
│   │   └── lib/                          # API client, mappers, utils
│   └── public/
├── docker-compose.yml                    # Production orchestration
├── docker-compose.dev.yml                # Development orchestration
└── .env.example                          # Environment variable template
```

---

## Collaboration Guide

### Branching Strategy

We use a simple trunk-based workflow:

- `main` — always deployable; protected branch
- Feature branches: `feat/<short-description>` (e.g., `feat/wcag-contrast-check`)
- Bug fixes: `fix/<short-description>` (e.g., `fix/logo-dpi-scaling`)
- Documentation: `docs/<short-description>`

### Commit Convention

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
feat(backend): add WCAG contrast ratio enforcement
fix(frontend): resolve broken image URL on brand kit cards
docs: update setup instructions for macOS
chore: upgrade dependencies
```

### Contribution Workflow

1. **Fork or branch** from `main`
2. **Open an issue first** for non-trivial changes — describe the problem and proposed solution before writing code
3. **Write tests** for new backend functionality in `backend/tests/`
4. **Run linting** before pushing:
   ```bash
   # Backend
   cd backend && ruff check src/ && ruff format src/

   # Frontend
   cd frontend && npm run lint
   ```
5. **Open a pull request** against `main` with a clear description of what changed and why
6. PRs require at least one review before merging

### Code Style

**Python**: Ruff enforces PEP 8 with 88-character line length targeting Python 3.10+. Type annotations are expected on all public functions.

**TypeScript/React**: Follow existing component patterns. Prefer functional components with hooks. Use the existing Radix UI + TailwindCSS component library in `frontend/src/components/ui/` rather than adding new UI dependencies.

### Areas Open for Contribution

- **WCAG accessibility enforcement** — contrast ratio violations are detected but not yet fully enforced against brand color rules
- **Async audit processing** — audits currently run synchronously; background job queue (Celery or asyncio tasks) would improve UX for large documents
- **Cloud storage** — replace local file volumes with S3-compatible storage
- **Voice/tone analysis** — expand LLM-based text compliance beyond basic keyword matching
- **Export** — generate downloadable PDF audit reports
- **Logo clear space validation** — mathematical enforcement of logo padding rules

### Getting Help

- Open a GitHub issue for bugs or feature requests
- For questions about the ML pipeline, see `backend/system_architecture.md`
- For questions about color detection specifics, see `color_gap_analysis.md`

---

## License

This project is maintained by [Humanitarians AI](https://humanitariansai.org). See `LICENSE` for details.
