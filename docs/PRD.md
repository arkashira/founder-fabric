# Product Requirements Document – founder‑fabric

---

## 1. Executive Summary  

**founder‑fabric** is a SaaS‑style tool that transforms raw market signals into fully‑specified, revenue‑validated product concepts for early‑stage founders and venture scouts. By automating ideation, validation, and documentation, it cuts the time to insight by >70 % while ensuring every output is unique and backed by willingness‑to‑pay evidence.

---

## 2. Problem Statement  

- **Long Ideation Loops**: Founders spend weeks or months researching, drafting, and refining product ideas before a prototype.  
- **Uncertain Demand**: Many concepts are built on assumptions that rarely translate into paying customers.  
- **Duplication Risk**: Without a central guardrail, teams may reinvent solutions already in the market or in our own portfolio.  
- **Fragmented Tooling**: Existing solutions split signal ingestion, idea synthesis, validation, and documentation across multiple tools, creating friction.

---

## 3. Target Users  

| Persona | Goals | Pain Points | How founder‑fabric Helps |
|---------|-------|-------------|--------------------------|
| **Early‑Stage Founder** | Quickly generate validated product concepts to pitch investors or launch MVPs | Limited time, lack of market research expertise | Instant PRDs with validated revenue signals |
| **Venture Scout / Angel** | Identify high‑impact opportunities across sectors | Scanning vast data, avoiding duplicates | Automated cross‑check against portfolio + revenue‑first scoring |
| **Product Manager (Startup)** | Prioritize ideas for roadmap | Unclear market fit, resource constraints | Ranked idea list with WTP metrics and PRD export |
| **Growth Hacker** | Test hypotheses rapidly | Manual survey setup, low response rates | Built‑in willingness‑to‑pay survey templates |

---

## 4. Goals & Success Metrics  

| Goal | Success Metric | Target |
|------|----------------|--------|
| **Speed to Insight** | Time from signal ingestion to PRD export | ≤ 5 minutes |
| **Revenue Validity** | % of generated ideas that pass WTP threshold | ≥ 80 % |
| **Uniqueness** | % of ideas flagged as duplicates | < 5 % |
| **Adoption** | Monthly active users (MAU) | 1,000 by Q3 2026 |
| **Ecosystem Growth** | Number of community‑submitted data sources | ≥ 20 by Q4 2026 |
| **Quality of PRDs** | User satisfaction score (post‑export survey) | ≥ 4.5/5 |

---

## 5. Key Features (Prioritized)

| Rank | Feature | Description | Acceptance Criteria |
|------|---------|-------------|---------------------|
| 1 | **Signal Ingestion** | Pull RSS, social, and custom APIs into a unified vector store. | • Supports at least 3 source types.<br>• Stores vectors in pgvector.<br>• Handles 10k+ signals/day. |
| 2 | **Idea Synthesis** | Generates 5‑Why chains linking pain points to product hypotheses. | • Produces 3–5 hypotheses per signal.<br>• Output is human‑readable markdown. |
| 3 | **Portfolio Guardrail** | Cross‑checks new ideas against existing catalog to avoid duplication. | • Flags duplicates with similarity > 0.85.<br>• Provides alternative unique ideas. |
| 4 | **Revenue Validation** | Runs quick WTP surveys and early‑adopter outreach scripts. | • Generates a survey URL.<br>• Collects ≥ 30 responses within 48 h. |
| 5 | **Exportable PRD** | Emits a ready‑to‑use markdown PRD (specs, user stories, MVP scope). | • Includes sections: Problem, Solution, Metrics, Roadmap.<br>• Markdown passes linting. |
| 6 | **CLI & API** | Command‑line and HTTP endpoints for automation. | • CLI supports `ingest`, `synthesize`, `validate`, `export` commands.<br>• API follows OpenAPI spec. |
| 7 | **Extensibility Layer** | Plug‑in new data sources or validation heuristics. | • Supports plugin registration via `plugins/` directory.<br>• No core code changes required. |
| 8 | **Community Contribution Flow** | Simple process for adding new data sources or heuristics. | • PR template with tests.<br>• CI passes on merge. |

---

## 6. Scope  

| Category | In‑Scope | Out‑of‑Scope |
|----------|----------|--------------|
| **Core Functionality** | Signal ingestion, synthesis, validation, PRD export, CLI/API, guardrail | Full‑stack web UI (to be addressed in a separate sprint) |
| **Data Sources** | RSS, Twitter, Reddit, custom REST APIs | Proprietary paid data feeds |
| **Validation** | WTP surveys, early‑adopter outreach scripts | Full market sizing or competitive analysis |
| **Infrastructure** | Local dev environment, Docker, CI/CD | Cloud deployment (to be handled by Ops later) |
| **Security** | Basic auth for API, rate limiting | Advanced IAM, GDPR compliance |

---

## 7. Dependencies & Constraints  

- **Tech Stack**: Python 3.11+, FastAPI, pgvector, vLLM (for LLM inference), SGLang (structured generation).  
- **Data**: Must use only datasets listed in the company context (auto, messages, conversations, query‑resp).  
- **Licensing**: All code under MIT; external libraries must be compatible.  
- **Performance**: Inference latency < 2 s per idea; vector store queries < 50 ms.  
- **Compliance**: No PII collected without consent; surveys must be opt‑in.  

---

## 8. Deliverables  

1. **CLI** – `founder-fabric` executable with subcommands.  
2. **API** – FastAPI app with OpenAPI docs.  
3. **PRD Exporter** – Markdown generator.  
4. **Tests** – Unit, integration, and end‑to‑end tests covering 90 % of code.  
5. **Documentation** – README, usage guide, plugin docs.  
6. **CI/CD** – GitHub Actions for linting, testing, and Docker image build.  

---

## 9. Timeline (High‑Level)

| Phase | Duration | Milestone |
|-------|----------|-----------|
| Discovery & Design | 2 weeks | PRD finalization, tech‑stack lock |
| Core Development | 6 weeks | Signal ingestion, synthesis, guardrail |
| Validation & PRD Export | 4 weeks | WTP survey integration, PRD generator |
| API & CLI | 3 weeks | FastAPI endpoints, CLI wrapper |
| Testing & QA | 2 weeks | Full test suite, performance benchmarks |
| Release & Docs | 1 week | Docker image, GitHub release, docs |

---

## 10. Risks & Mitigations  

| Risk | Impact | Mitigation |
|------|--------|------------|
| **LLM inference cost** | High | Use vLLM with GPU off‑load; cache results |
| **Duplicate detection false positives** | Missed unique ideas | Tune similarity threshold; allow manual override |
| **Survey response rate** | Low validation | Provide incentives; integrate with existing outreach tools |
| **Data source volatility** | Ingestion failures | Implement retry logic; monitor source health |

---

## 11. Success Review Checklist  

- [ ] All core features pass acceptance criteria.  
- [ ] PRD export matches template.  
- [ ] Duplicate detection accuracy ≥ 95 %.  
- [ ] WTP survey collects ≥ 30 responses in 48 h for 80 % of ideas.  
- [ ] CLI and API documented and tested.  
- [ ] CI/CD pipeline builds and deploys Docker image.  

---
