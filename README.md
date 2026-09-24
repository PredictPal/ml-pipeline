# PredictPal

[![LeedsHack 2026 — Parallax Challenge winner](https://img.shields.io/badge/LeedsHack_2026-Parallax_Challenge_Winner-7C3AED?style=flat-square)](https://eps.leeds.ac.uk/faculty-engineering-physical-sciences/news/article/6160/leedshack-2026-gains-major-league-hacking-status-and-a-record-turnout)
[![Devpost](https://img.shields.io/badge/Devpost-PredictPal-003E54?style=flat-square&logo=devpost)](https://devpost.com/software/predictpal)

PredictPal is a guided time-series forecasting workbench that turns raw target and driver data into evaluated forecasts, interactive analysis, and shareable notebook-style stories.

> **Winner — Parallax Sponsor Challenge: “Use the past to predict the future”, LeedsHack 2026.**

[Devpost submission](https://devpost.com/software/predictpal) · [University of Leeds coverage](https://eps.leeds.ac.uk/faculty-engineering-physical-sciences/news/article/6160/leedshack-2026-gains-major-league-hacking-status-and-a-record-turnout)

## Why PredictPal?

Forecasting software is often powerful but difficult to use or explain. PredictPal guides non-specialists through the full workflow: ingesting messy data, choosing preprocessing and modelling options, comparing a baseline with a driver-aware model, understanding the result, and turning it into a readable story.

The project was built during the 24-hour LeedsHack 2026 competition, where more than 50 projects were submitted. It won Parallax’s forecasting challenge for automating a data-analysis pipeline with clear outputs. Following the event, the team was invited to Parallax HQ to present the system and discuss its technical decisions and real-world product applications.

## Product flow

```mermaid
flowchart LR
    A["Upload data"] --> B["Process data"]
    B --> C["Train & forecast"]
    C --> D["Analyse results"]
    D --> E["Publish story"]
```

1. **Get Started** — upload a target dataset and optional driver datasets.
2. **Process Data** — select date/value columns, detect frequency, and configure missing-value and outlier handling.
3. **Train & Forecast** — compare a lagged Ridge or seasonal-naive baseline with a multivariate gradient-boosting model.
4. **Analysis & Results** — inspect holdout or walk-forward metrics, forecast charts, driver signals, and feature importance.
5. **Publish Story** — combine explanations and charts into a notebook-style post for the Explore feed.

A context-aware Gemini assistant can explain choices and suggest settings when `GEMINI_API_KEY` is configured. The core flow remains usable without it.

## What is implemented

- CSV and spreadsheet ingestion for target and driver series
- Frequency detection and frequency-aware driver alignment
- Separate preprocessing choices for target and driver data
- Lag, calendar, and holiday feature engineering
- Baseline and multivariate forecasting
- Single-split and walk-forward validation
- RMSE, MAE, NRMSE, and relative-improvement reporting
- Per-run JSON/CSV artefacts consumed by the analysis UI
- Interactive Recharts visualisations and notebook-style result publishing
- In-memory backend stories plus browser-local persistence for resilient demos
- Optional Gemini guidance; a Supabase schema is included for future persistence work

## Architecture

| Layer | Technologies | Responsibility |
| --- | --- | --- |
| Frontend | Next.js 16, React 19, TypeScript, Tailwind CSS 4, Zustand, Recharts | Guided workflow, analysis, visualisation, and story publishing |
| API | FastAPI, Pydantic, Uvicorn | Upload, preprocessing, training, analysis, chat, and story endpoints |
| Forecasting | pandas, scikit-learn, skforecast | Feature engineering, model fitting, evaluation, and forecast artefacts |
| Optional integration | Gemini | Contextual guidance when an API key is configured |

The backend writes artefacts for each run, and the frontend reads them for the analysis and publishing steps. Story posts live in backend memory or browser-local storage; the included Supabase schema is not wired into this flow.

## Repository layout

```text
backend/
  app/
    api/endpoints.py       API routes and in-memory project state
    core/                  preprocessing, features, models, evaluation, reporting
    main.py                FastAPI application
  tests/                   preprocessing and forecasting tests
frontend/
  src/
    app/                   Next.js routes
    components/steps/      five-stage forecasting workflow
    components/story/      published notebook rendering
    lib/                   API client, state, and local persistence
requirements.txt           Python dependencies
supabase_schema.sql        optional persistence schema
```

## Run locally

### Prerequisites

- Python 3.11 recommended
- Node.js 20+
- npm 10+

### 1. Clone the repository

```bash
git clone https://github.com/PredictPal/ml-pipeline.git
cd ml-pipeline
```

### 2. Set up the backend

```bash
python -m venv .venv
source .venv/bin/activate        # macOS/Linux

pip install -r requirements.txt
cp backend/.env.example backend/.env
cd backend
python -m uvicorn app.main:app --reload --port 8000
```

On Windows PowerShell, activate the environment and copy the template with:

```powershell
.\.venv\Scripts\Activate.ps1
Copy-Item backend\.env.example backend\.env
```

### 3. Set up the frontend

In a second terminal:

```bash
cd frontend
npm ci
npm run dev
```

Open:

- Frontend: <http://localhost:3000>
- API documentation: <http://localhost:8000/docs>
- Health check: <http://localhost:8000/health>

## Configuration

The forecasting flow can run locally without external services. Gemini guidance is optional. Supabase credentials are shown in the existing environment template, but the current story flow does not persist to Supabase.

Backend (`backend/.env`):

```env
# Supabase connection settings (schema included; story persistence not wired up)
SUPABASE_URL=
SUPABASE_KEY=

# Optional contextual assistant
GEMINI_API_KEY=
```

Frontend (`frontend/.env.local`, optional):

```env
NEXT_PUBLIC_API_URL=http://localhost:8000/api
```

If `NEXT_PUBLIC_API_URL` is omitted, the frontend uses `http://localhost:8000/api`.

## Verification

```bash
# Backend
python -m pip install pytest
python -m pytest backend/tests

# Frontend
cd frontend
npm run lint
npm run build
```

## Team

PredictPal was created collaboratively by:

- [Nathan Walsh](https://github.com/NathanWalash)
- [Cal Levitt](https://github.com/Cal-levitt111)
- [Gabriel Saban](https://github.com/gabrielsaban)
- [Kian Thakrar](https://github.com/KianThakrar)

The team worked across product design, frontend development, data ingestion and preprocessing, forecasting and validation, visualisation, and technical storytelling.

## Recognition

LeedsHack 2026 took place on 7–8 February 2026 and became part of the Major League Hacking network. PredictPal won the **Parallax Sponsor Challenge: “Use the past to predict the future”**. The University of Leeds described the project as an automated data-analysis pipeline with clear outputs.

Following the hackathon, the team was invited to Parallax HQ to discuss PredictPal.

## Project status

PredictPal is preserved as a hackathon prototype and portfolio project rather than a hosted production service. The repository reflects the final post-event implementation, including frequency-aware preprocessing and per-run analysis artefacts.

For the original submission story and judging context, see the [Devpost project](https://devpost.com/software/predictpal).
