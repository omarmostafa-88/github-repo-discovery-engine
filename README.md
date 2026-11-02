# AI-Powered Open-Source Discovery Platform

A hybrid search and recommendation engine that helps developers find GitHub repositories that match what they are looking for and who they are.

It combines classic Information Retrieval (**BM25**) with **semantic search** (sentence embeddings), then personalizes the results based on the user's goal, skill level and tech stack, and explains why each repository was recommended.

> Built for the CS313x – Information Retrieval & Data Analysis course.

---

## Features

- **Ethical web scraping**: collects data on 200 GitHub repositories, respects `robots.txt`, rate-limited requests.
- **NLP pipeline**: tokenization, stemming, lemmatization, stopword removal, field-weighted indexing (title, topics, description, README).
- **Hybrid ranking**: final score = BM25 keyword relevance + semantic similarity (`all-MiniLM-L6-v2`) + phrase match + metadata quality.
- **Personalized recommendations**: re-ranks results by goal (learn / contribute), skill level and preferred stack.
- **Explainable results**: every result shows its score breakdown and the reasons it matched.
- **Full-stack app**: FastAPI REST API + React (Vite) web interface with filters.

## Tech Stack

| Layer | Tools |
|---|---|
| Data collection | Python, requests, BeautifulSoup |
| Processing | NLTK |
| Search & ranking | rank-bm25, sentence-transformers, NumPy |
| Backend | FastAPI, Pydantic, Uvicorn |
| Frontend | React, Vite, Axios |

## Architecture

```
GitHub ──> scraper.py ──> data.json ──> process.py ──> processed.json
                                                          │
                                                          ▼
                                     ir_engine.py (BM25 + embeddings index)
                                                          │
                                                          ▼
                                   FastAPI backend  <──>  React frontend
```

## Getting Started

### 1. Backend

```bash
pip install -r requirements.txt
uvicorn backend.main:app --reload
```

Run this from the project root. The API runs at `http://127.0.0.1:8000`, with interactive docs at `/docs`.
A prebuilt search index is included in `search_index/`, so you don't need to re-scrape to try it.

### 2. Frontend

```bash
cd frontend
cp .env.example .env
npm install
npm run dev
```

Then open `http://localhost:5173`.

### Rebuild the data (optional)

```bash
python scraper.py      # collect repositories
python process.py      # clean and tokenize
python analysis.py     # dataset statistics
```

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| POST | `/search/` | Hybrid search |
| POST | `/search/explain` | Explain a result's score |
| POST | `/recommend/` | Personalized recommendations |
| GET | `/repos/` | List repositories |
| GET | `/repos/filters/options` | Available filter values |
| GET | `/health` | Health check |

## Project Structure

```
├── scraper.py                        # Data collection
├── process.py                        # NLP preprocessing
├── analysis.py                       # Dataset statistics
├── ir_engine.py                      # Hybrid BM25 + semantic search engine
├── smart_profile_recommender_v2.py   # Personalized recommender (CLI)
├── backend/                          # FastAPI app
├── frontend/                         # React app
└── search_index/                     # Prebuilt index (BM25 + embeddings)
```
