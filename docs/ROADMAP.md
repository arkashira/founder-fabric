# 🚀 Roadmap – founder‑fabric

> **Audience** – Product, Engineering, Ops, and Stakeholders  
> **Scope** – From MVP launch to v2.0, aligned with Axentx’s validated‑product workflow and existing portfolio.  
> **Version** – 1.0.0 (MVP) → 1.1.0 (v1) → 1.2.0 (v2)

---

## 📅 Timeline Overview

| Quarter | Milestone | Key Deliverables |
|---------|-----------|------------------|
| **Q2 2026** | **MVP** | Core ingestion, synthesis, guardrail, PRD export, CLI. |
| **Q3 2026** | **v1 – Validation & Automation** | Survey engine, outreach scripts, API surface, CI/CD. |
| **Q4 2026** | **v2 – Scale & Community** | Multi‑source ingestion, ML‑based duplication detection, web UI, open‑source contribution flow. |

---

## 🎯 MVP (Must‑Have for Launch)

| Feature | Description | Implementation Notes | MVP‑Critical |
|---------|-------------|----------------------|--------------|
| **Signal Ingestion** | Pull RSS, Twitter, Reddit, and custom API feeds into a unified vector store. | Use `feedparser`, `tweepy`, and a simple HTTP client. Store vectors in `pgvector` (shared BRAIN). | ✅ |
| **Idea Synthesis** | Generate 5‑Why chains that map pain points to product hypotheses. | Leverage a lightweight LLM wrapper (e.g., `vLLM` or `SGLang`) with a prompt template. | ✅ |
| **Portfolio Guardrail** | Cross‑check new ideas against existing catalog to avoid duplication. | Simple cosine‑similarity search in `pgvector` with a threshold. | ✅ |
| **Revenue Validation** | Quick willingness‑to‑pay survey generator and early‑adopter outreach script. | Template‑based email/SMS generator; no external survey platform integration yet. | ❌ (Optional for MVP) |
| **Exportable PRD** | Markdown PRD with specs, user stories, MVP scope. | Templated Markdown generator. | ✅ |
| **CLI** | Command‑line interface for all core functionalities. | `typer` or `click` for CLI. | ✅ |
| **Basic CI** | Unit tests, linting, and build status. | GitHub Actions. | ✅ |

### MVP Acceptance Criteria

1. **Signal ingestion** from at least two public sources (RSS + Twitter) works end‑to‑end.  
2. **Idea synthesis** produces a 5‑Why chain and a product hypothesis.  
3. **Guardrail** flags a duplicate if the similarity score > 0.85.  
4. **PRD export** generates a valid Markdown file with sections: Title, Problem, Solution, MVP Scope, Success Metrics.  
5. **CLI** exposes commands: `ingest`, `synthesize`, `guardrail`, `export`.  
6. **CI** passes on every push to `main`.  

---

## 📦 v1 – Validation & Automation (Q3 2026)

| Theme | Focus | Deliverables |
|-------|-------|--------------|
| **Revenue Validation** | Turn hypotheses into validated concepts. | • Survey engine (Google Forms API integration). <br>• Early‑adopter outreach scripts (email/SMS). <br>• WTP scoring model (simple logistic regression). |
| **API Surface** | Enable automation pipelines. | • RESTful endpoints for ingestion, synthesis, validation, and PRD export. <br>• OpenAPI spec. |
| **Observability** | Track signal quality and synthesis quality. | • Metrics (signal count, synthesis success rate). <br>• Logging & tracing (OpenTelemetry). |
| **Testing & Quality** | Raise coverage to 90%. | • Property‑based tests for ingestion pipelines. <br>• Integration tests for API. |
| **Documentation** | On‑boarding for new contributors. | • `docs/` folder with architecture, contribution guide, and API docs. |

### v1 Acceptance Criteria

- **Survey engine** can send a survey to 50 contacts and collect responses.  
- **WTP model** achieves ≥70% precision on a held‑out validation set.  
- **API** returns 200 OK for all endpoints under 200 ms average latency.  
- **CI** includes unit, integration, and security scans.  

---

## 🌱 v2 – Scale & Community (Q4 2026)

| Theme | Focus | Deliverables |
|-------|-------|--------------|
| **Multi‑Source Ingestion** | Expand to LinkedIn, Hacker News, and custom webhooks. | • Modular ingestion plugins. <br>• Scheduler (Celery/Prefect). |
| **ML‑Based Duplication Detection** | Improve guardrail with semantic similarity. | • Fine‑tuned sentence‑embedding model (e.g., `sentence-transformers`). <br>• Threshold tuning via A/B tests. |
| **Web UI** | Provide a visual interface for founders. | • React/Vue SPA. <br>• Dashboard: Signal feed, idea pipeline, PRD viewer. |
| **Community Contribution Flow** | Lower barrier to add new data sources and validation heuristics. | • Plugin SDK. <br>• Marketplace of community‑built plugins. |
| **Performance & Scaling** | Support 10k signals/day. | • Distributed vector store (Pinecone/Weaviate). <br>• Autoscaling CI runners. |
| **Governance & Security** | Ensure compliance with data privacy. | • GDPR‑ready data handling. <br>• Role‑based access control for API. |

### v2 Acceptance Criteria

- **Ingestion** handles 10k signals/day with <5 min latency.  
- **Duplication detection** precision ≥90% on a curated test set.  
- **Web UI** allows a founder to generate a PRD in <2 min.  
- **Plugin SDK** has at least 3 community‑submitted plugins.  
- **Security** audit passes with no critical findings.  

---

## 📌 Key Dependencies & Risks

| Dependency | Status | Mitigation |
|------------|--------|------------|
| **LLM inference** (`vLLM`/`SGLang`) | TBD | Start with open‑source LLMs; evaluate cost of hosted APIs. |
| **Vector store** (`pgvector`) | In‑house | Monitor query performance; plan migration to managed service if needed. |
| **Survey platform** | External | Use open APIs (Google Forms, Typeform) to avoid vendor lock‑in. |
| **Community plugins** | Uncertain | Provide clear SDK and documentation; incentivize contributions. |

---

## 📈 Success Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| **Time to Insight** | ≤5 min per idea | CLI/API response time |
| **Duplicate Rate** | ≤2% | Guardrail flagging |
| **WTP Accuracy** | ≥70% precision | Survey model evaluation |
| **User Adoption** | 100 founders in beta | Sign‑ups & PRD exports |
| **Contribution Rate** | 10+ plugins | Plugin registry |

---

## 📚 Next Steps

1. **Finalize tech stack** – lock decisions in `decisions/tech-stack.md`.  
2. **Set up CI/CD** – GitHub Actions with Docker builds.  
3. **Kick‑off MVP sprint** – assign tasks to engineering teams.  
4. **Engage early‑stage founders** for beta testing and feedback loops.  

---
