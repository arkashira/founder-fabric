# STORIES.md

## 🎯 Product Vision

Founder‑Fabric turns raw market signals into revenue‑validated product concepts in minutes. The goal of this repository is to provide a **CLI + API** that early‑stage founders and venture scouts can use to:

1. **Ingest** market signals from diverse sources.  
2. **Synthesize** ideas with a 5‑Why chain.  
3. **Guardrail** against duplication with the existing portfolio.  
4. **Validate** willingness‑to‑pay quickly.  
5. **Export** a ready‑to‑ship PRD.

The following backlog is organized into **epics** that map to the feature list above. Stories are written from the perspective of the primary users (founders, scouts, and product managers) and are ordered to deliver an MVP that can be shipped in a single sprint.

---

## 📦 Epics & User Story Backlog

### Epic 1 – Signal Ingestion

| # | User Story | Acceptance Criteria |
|---|-------------|---------------------|
| **E1‑S1** | **As a founder, I want to pull market signals from RSS feeds, so that I can stay up‑to‑date with industry trends.** | • CLI command `ff ingest rss <url>` downloads the feed.<br>• Parsed items are stored in the vector store with metadata (source, timestamp).<br>• Duplicate items are deduplicated by hash. |
| **E1‑S2** | **As a founder, I want to ingest signals from a custom REST API, so that I can use proprietary data sources.** | • CLI command `ff ingest api <endpoint> --auth <token>` fetches JSON.<br>• Supports pagination via `next` links.<br>• Data is normalized to a common schema before storage. |
| **E1‑S3** | **As a product manager, I want to schedule ingestion jobs, so that I don’t have to run them manually.** | • CLI command `ff schedule rss <url> --cron "0 * * * *"` creates a cron job.<br>• Jobs are persisted in a lightweight SQLite DB.<br>• Cron output is logged to `logs/ingest.log`. |

---

### Epic 2 – Idea Synthesis

| # | User Story | Acceptance Criteria |
|---|-------------|---------------------|
| **E2‑S1** | **As a founder, I want the system to generate a 5‑Why chain for each signal, so that I can see the root pain points.** | • CLI `ff synthesize <signal-id>` outputs a markdown section with 5 layers of “Why?”.<br>• Uses the LLM model defined in `decisions/tech-stack.md` (default: vLLM).<br>• Each layer is a short sentence (≤20 words). |
| **E2‑S2** | **As a scout, I want to filter synthesized ideas by industry tags, so that I can focus on my niche.** | • CLI `ff filter --tag <industry>` returns only ideas tagged with the given industry.<br>• Tagging is inferred from the signal metadata. |
| **E2‑S3** | **As a founder, I want to export the 5‑Why chain to a PRD file, so that I can share it with my team.** | • CLI `ff export prd <idea-id> --format markdown` writes a file `prd/<idea-id>.md`.<br>• The file contains title, summary, 5‑Why chain, and a placeholder for MVP scope. |

---

### Epic 3 – Portfolio Guardrail

| # | User Story | Acceptance Criteria |
|---|-------------|---------------------|
| **E3‑S1** | **As a founder, I want the system to check new ideas against the existing portfolio, so that I avoid duplication.** | • CLI `ff guardrail <idea-id>` queries the vector store for similarity > 0.85.<br>• Returns a list of similar existing products with a similarity score.<br>• If any match > 0.9, the idea is marked as “duplicate” and not exported. |
| **E3‑S2** | **As a product manager, I want to view a dashboard of all ideas and their guardrail status, so that I can triage quickly.** | • CLI `ff dashboard` lists ideas with columns: ID, Title, Status (New/Guardrail/Approved).<br>• Supports filtering by status. |

---

### Epic 4 – Revenue Validation

| # | User Story | Acceptance Criteria |
|---|-------------|---------------------|
| **E4‑S1** | **As a founder, I want to run a quick willingness‑to‑pay survey for an idea, so that I can gauge market interest.** | • CLI `ff survey <idea-id>` generates a 5‑question survey template.<br>• Survey is sent via email (SMTP config in `config.yaml`).<br>• Responses are stored in `surveys/<idea-id>.json`. |
| **E4‑S2** | **As a scout, I want to see a summary of survey results, so that I can decide whether to pursue the idea.** | • CLI `ff survey summary <idea-id>` outputs: average WTP, % positive, key objections.<br>• Data is visualized as a simple ASCII bar chart. |

---

### Epic 5 – Exportable PRD

| # | User Story | Acceptance Criteria |
|---|-------------|---------------------|
| **E5‑S1** | **As a founder, I want to generate a complete PRD that includes user stories, MVP scope, and validation data, so that I can hand it to engineering.** | • CLI `ff prd <idea-id> --full` creates `prd/<idea-id>.md` with sections: Title, Summary, 5‑Why, User Stories, MVP Scope, Validation Results.<br>• The file follows the markdown template in `templates/prd.md`. |
| **E5‑S2** | **As a product manager, I want to export the PRD to a JSON format for API consumption, so that I can integrate with other tools.** | • CLI `ff prd <idea-id> --json` writes `prd/<idea-id>.json` with the same data structure. |

---

### Epic 6 – CLI & API

| # | User Story | Acceptance Criteria |
|---|-------------|---------------------|
| **E6‑S1** | **As a developer, I want to expose the core functionality via a REST API, so that I can integrate it into my CI/CD pipeline.** | • FastAPI server runs on `localhost:8000`.<br>• Endpoints: `/ingest`, `/synthesize`, `/guardrail`, `/survey`, `/prd`.<br>• API is documented with OpenAPI. |
| **E6‑S2** | **As a founder, I want a help command that lists all available CLI options, so that I can learn how to use the tool.** | • CLI `ff help` prints usage for all subcommands.<br>• Each subcommand has a `--help` flag. |

---

## 🚀 MVP Release Plan

1. **Signal Ingestion** (E1) – 2 days  
2. **Idea Synthesis** (E2) – 3 days  
3. **Portfolio Guardrail** (E3) – 2 days  
4. **Revenue Validation** (E4) – 3 days  
5. **Exportable PRD** (E5) – 2 days  
6. **CLI & API** (E6) – 3 days  

Total: **15 working days** (≈3 sprints).  
All stories are written to be shippable with unit tests and integration tests in the `tests/` folder.  

---

## 📚 Additional Notes

- **Data Sources**: The ingestion layer currently supports RSS and generic REST APIs. Future work will add social media (Twitter, LinkedIn) and custom webhook support.  
- **LLM Model**: The default model is `vllm-project/vllm`. The system can be swapped out via the `decisions/tech-stack.md` file.  
- **Vector Store**: Uses `pgvector` for similarity search. The guardrail checks are performed against the existing portfolio stored in the same database.  
- **Security**: All API keys and SMTP credentials are read from `config.yaml` and should be stored in environment variables in production.  

---

## 🎉 Acceptance Checklist

- [ ] All CLI commands pass unit tests.  
- [ ] API endpoints return correct responses and are documented.  
- [ ] PRD export matches the markdown template.  
- [ ] Guardrail correctly flags duplicates.  
- [ ] Survey integration sends emails and stores responses.  

---
