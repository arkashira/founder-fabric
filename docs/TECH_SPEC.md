# TECH_SPEC.md

---

## 1. Overview

`founder-fabric` is a **Python‑based micro‑service** that turns raw market signals into revenue‑validated product concepts.  
It ingests signals, synthesizes ideas, guards against duplication, validates willingness‑to‑pay, and emits a ready‑to‑ship PRD.  
The system is designed for rapid iteration, high test coverage, and easy integration into existing CI/CD pipelines.

---

## 2. Architecture

```
┌───────────────────────┐
│  1. Signal Ingestion   │
│  • RSS, Social, API    │
└───────┬───────────────┘
        │
        ▼
┌───────────────────────┐
│  2. Vector Store       │
│  • pgvector (PostgreSQL)│
└───────┬───────────────┘
        │
        ▼
┌───────────────────────┐
│  3. Idea Synthesis     │
│  • LLM (vLLM)          │
│  • Prompt Templates   │
└───────┬───────────────┘
        │
        ▼
┌───────────────────────┐
│  4. Portfolio Guardrail│
│  • Duplicate detection │
│  • Cross‑reference API │
└───────┬───────────────┘
        │
        ▼
┌───────────────────────┐
│  5. Revenue Validation │
│  • Survey Generator    │
│  • Outreach Scripts    │
└───────┬───────────────┘
        │
        ▼
┌───────────────────────┐
│  6. PRD Exporter       │
│  • Markdown Template   │
└───────┬───────────────┘
        │
        ▼
┌───────────────────────┐
│  7. API / CLI Layer    │
│  • FastAPI             │
│  • Typer CLI           │
└───────────────────────┘
```

*All components are stateless except the vector store, which persists embeddings and metadata.*

---

## 3. Core Components

| Layer | Module | Responsibility | Key Classes / Functions |
|-------|--------|----------------|-------------------------|
| **Signal Ingestion** | `business/ingestion.py` | Pulls data from RSS, Twitter, custom APIs | `SignalFetcher`, `RSSFetcher`, `TwitterFetcher` |
| **Vector Store** | `business/vector_store.py` | Stores embeddings in PostgreSQL + pgvector | `VectorStore`, `PostgresVectorStore` |
| **Idea Synthesis** | `business/synthesis.py` | Generates 5‑Why chains via LLM | `Synthesizer`, `LLMPipeline` |
| **Portfolio Guardrail** | `business/guardrail.py` | Checks against existing catalog | `Guardrail`, `DuplicateDetector` |
| **Revenue Validation** | `business/validation.py` | Generates surveys & outreach scripts | `Validator`, `SurveyGenerator` |
| **PRD Exporter** | `business/exporter.py` | Renders markdown PRD | `PRDExporter`, `TemplateRenderer` |
| **API / CLI** | `api/main.py`, `cli/main.py` | Expose endpoints & commands | `FastAPI`, `Typer` |

---

## 4. Data Model

```sql
-- PostgreSQL schema (pgvector extension required)
CREATE TABLE signals (
    id UUID PRIMARY KEY,
    source TEXT NOT NULL,
    raw_text TEXT NOT NULL,
    fetched_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

CREATE TABLE embeddings (
    signal_id UUID REFERENCES signals(id),
    embedding VECTOR(1536),          -- 1536‑dim for OpenAI embeddings
    PRIMARY KEY (signal_id)
);

CREATE TABLE ideas (
    id UUID PRIMARY KEY,
    title TEXT NOT NULL,
    description TEXT,
    pain_points TEXT[],
    hypotheses TEXT[],
    created_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

CREATE TABLE prds (
    id UUID PRIMARY KEY,
    idea_id UUID REFERENCES ideas(id),
    markdown TEXT NOT NULL,
    exported_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);
```

*All UUIDs are generated via `uuid4()`.*

---

## 5. Key APIs & Interfaces

| Endpoint | Method | Payload | Response | Notes |
|----------|--------|---------|----------|-------|
| `/ingest` | POST | `{ "source": "rss", "url": "..."} | 200 OK` | Triggers ingestion job |
| `/synthesize` | POST | `{ "signal_id": "..."} | 200 OK` | Returns synthesized idea JSON |
| `/validate` | POST | `{ "idea_id": "..."} | 200 OK` | Returns validation report |
| `/export` | GET | `?idea_id=...` | `text/markdown` | Downloads PRD |

*All endpoints are documented in `docs/openapi.yaml`.*

---

## 6. Technology Stack

| Layer | Technology | Rationale |
|-------|------------|-----------|
| **Runtime** | Python 3.11+ | Modern async support, type hints |
| **Web Framework** | FastAPI | Fast, async, OpenAPI auto‑docs |
| **CLI** | Typer | Click‑like interface, auto‑help |
| **LLM Inference** | vLLM (OpenAI GPT‑4o via API) | Low‑latency, scalable |
| **Vector Store** | PostgreSQL + pgvector | ACID, mature, easy to query |
| **ORM** | SQLAlchemy 2.0 | Declarative, async support |
| **Task Queue** | RQ (Redis) | Simple background jobs |
| **Testing** | pytest, httpx | Unit + integration |
| **CI** | GitHub Actions | Lint, test, build, publish |
| **Containerization** | Docker | Reproducible deployments |
| **Deployment** | Kubernetes (Helm) | Scalability, self‑healing |
| **Observability** | OpenTelemetry + Grafana | Metrics, tracing |
| **Secrets** | Vault / AWS Secrets Manager | Secure API keys |

---

## 7. Dependencies

```toml
[tool.poetry.dependencies]
python = "^3.11"
fastapi = "^0.110.0"
uvicorn = "^0.29.0"
typer = "^0.12.0"
sqlalchemy = {extras = ["asyncio"], version = "^2.0.30"}
psycopg2-binary = "^2.9.9"
pgvector = "^0.2.0"
vllm = "^0.5.0"
redis = "^5.0.0"
rq = "^1.15.0"
jinja2 = "^3.1.4"
pydantic = "^2.7.0"
httpx = "^0.27.0"
pytest = "^8.2.0"
pytest-asyncio = "^0.23.5"
```

*All versions are pinned to the latest stable releases as of 2026‑06‑10.*

---

## 8. Deployment

### 8.1 Docker

```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY pyproject.toml poetry.lock ./
RUN pip install poetry && poetry install --no-dev --no-root
COPY . .

CMD ["uvicorn", "api.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 8.2 Helm Chart

```yaml
# charts/founder-fabric/values.yaml
replicaCount: 2
image:
  repository: ghcr.io/your-org/founder-fabric
  tag: latest
service:
  port: 80
env:
  DATABASE_URL: postgres://user:pass@db:5432/founder_fabric
  REDIS_URL: redis://redis:6379/0
  OPENAI_API_KEY: <secret>
```

### 8.3 CI Pipeline

| Step | Action |
|------|--------|
| `lint` | flake8, black, isort |
| `test` | pytest, coverage |
| `build` | Docker build & push |
| `deploy` | Helm upgrade (k8s) |

---

## 9. Security & Compliance

- **API Keys** stored in Vault; injected via Kubernetes secrets.
- **Rate limiting** on public endpoints (60 req/min).
- **Input validation** via Pydantic models.
- **Audit logs** written to CloudWatch (or equivalent).

---

## 10. Extensibility

- **New Ingestion Sources**: Implement `SignalFetcher` subclass, register in `ingestion.py`.
- **Alternate LLMs**: Swap `LLMPipeline` implementation; only the `generate()` interface is required.
- **Custom Validation Rules**: Add to `Validator` via strategy pattern.

---

## 11. Testing Strategy

| Layer | Test Type | Tool |
|-------|-----------|------|
| Unit | Function tests | pytest |
| Integration | DB + API | httpx, asyncpg |
| End‑to‑End | CLI + API | Typer CLI, httpx |
| Performance | Load tests | Locust |

All tests must achieve ≥90% coverage before merge.

---

## 12. Documentation

- **API Docs**: Auto‑generated via FastAPI (`/docs`).
- **Developer Guide**: `docs/developer.md`.
- **Contribution Guide**: `CONTRIBUTING.md`.

---

## 13. Roadmap (next 3 months)

1. **MVP**: Signal ingestion + idea synthesis + PRD export.  
2. **Guardrail**: Duplicate detection against existing catalog.  
3. **Validation**: Survey generator + outreach script.  
4. **Observability**: OpenTelemetry instrumentation.  
5. **Auto‑Scaling**: Kubernetes HPA for API pods.

---

*Prepared by the Senior Product/Engineering Lead – 2026‑06‑10*
