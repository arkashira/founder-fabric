# REQUIREMENTS.md

## 1. Overview

`founder-fabric` is a product‑generation engine that transforms raw market signals into revenue‑validated product concepts. The system must ingest signals, synthesize ideas, guard against duplication, validate willingness‑to‑pay, and output a complete PRD. The following requirements document defines the functional, non‑functional, constraints, and assumptions for the next release.

---

## 2. Functional Requirements

| ID   | Description | Source |
|------|-------------|--------|
| **FR‑1** | **Signal Ingestion** – The system must pull market signals from RSS feeds, social media APIs (Twitter, Reddit, LinkedIn), and custom HTTP endpoints. | README |
| **FR‑2** | **Unified Vector Store** – All ingested signals must be converted into embeddings and stored in a vector database (e.g., pgvector) with metadata (source, timestamp, confidence). | README |
| **FR‑3** | **Idea Synthesis** – For each signal, generate a 5‑Why chain that identifies pain points and translates them into concrete product hypotheses. | README |
| **FR‑4** | **Portfolio Guardrail** – Before finalizing an idea, compare its embedding against the existing product catalog. If similarity > 0.85, reject the idea and log a duplication warning. | README |
| **FR‑5** | **Revenue Validation** – Automatically trigger a willingness‑to‑pay survey (via email or web form) and collect responses. Aggregate results to compute a weighted WTP score. | README |
| **FR‑6** | **PRD Generation** – Emit a Markdown PRD containing: <br>• Title, problem statement, target users <br>• User stories (at least 3) <br>• MVP scope <br>• Revenue validation summary <br>• Technical architecture sketch | README |
| **FR‑7** | **CLI Interface** – Provide a command‑line tool (`founder-fabric`) that supports sub‑commands: `ingest`, `synthesize`, `validate`, `export`. | README |
| **FR‑8** | **API Endpoints** – Expose HTTP endpoints for each major operation (ingest, synthesize, validate, export) with JSON payloads. | README |
| **FR‑9** | **Extensibility Hooks** – Allow plug‑in modules for new data sources, validation heuristics, or output formats without modifying core logic. | README |
| **FR‑10** | **Logging & Auditing** – Persist every step (ingestion, synthesis, validation, export) with timestamps and user identifiers for auditability. | README |
| **FR‑11** | **Error Handling** – Return clear error messages and HTTP 4xx/5xx codes for malformed requests or internal failures. | README |

---

## 3. Non‑Functional Requirements

| ID   | Requirement | Target |
|------|-------------|--------|
| **NFR‑1** | **Performance** – End‑to‑end processing of a single signal (ingest → synthesize → validate → export) must complete within 30 seconds on average hardware (4 CPU, 16 GB RAM). | 30 s |
| **NFR‑2** | **Scalability** – The system must handle up to 1,000 signals per minute in a distributed deployment. | 1k/min |
| **NFR‑3** | **Reliability** – 99.9% uptime for the API service; retries with exponential back‑off for transient failures. | 99.9% |
| **NFR‑4** | **Security** – All API endpoints require OAuth2 bearer tokens; data at rest encrypted (AES‑256) in the vector store. | OAuth2, AES‑256 |
| **NFR‑5** | **Data Privacy** – No personal data from users is stored beyond the survey responses; GDPR‑compliant retention policy (30 days). | 30 days |
| **NFR‑6** | **Observability** – Expose Prometheus metrics (`request_latency_seconds`, `ingestion_success_total`, etc.) and structured logs (JSON). | Prometheus |
| **NFR‑7** | **Maintainability** – Codebase follows PEP 8, uses type hints, and includes unit tests covering ≥80% of core modules. | 80% coverage |
| **NFR‑8** | **Documentation** – Public API docs (OpenAPI 3.0) and developer guide must be auto‑generated from code annotations. | Auto‑gen |
| **NFR‑9** | **Extensibility** – New data source adapters must implement a defined interface (`SignalAdapter`) and be discoverable via entry‑points. | Entry‑points |

---

## 4. Constraints

| ID   | Constraint | Rationale |
|------|------------|-----------|
| **C‑1** | **Open‑Source Licenses** – All third‑party libraries must be MIT, Apache‑2.0, or compatible. | Avoid license conflicts. |
| **C‑2** | **Python 3.11+** – The runtime environment is fixed to Python 3.11+. | Compatibility with existing dependencies. |
| **C‑3** | **Vector Store** – Must use `pgvector` extension on PostgreSQL 15+. | Consistency with company BRAIN. |
| **C‑4** | **No External Cloud Services** – All components must run on-prem or in a private VPC. | Data sovereignty. |
| **C‑5** | **CI/CD** – Must pass GitHub Actions workflow `CI` before merge. | Quality gate. |

---

## 5. Assumptions

| ID   | Assumption | Impact |
|------|------------|--------|
| **A‑1** | Market signals are available in JSON or RSS format. | Simplifies ingestion adapters. |
| **A‑2** | Survey responses are collected via a third‑party form service (e.g., Typeform) and can be fetched via webhook. | Offloads survey handling. |
| **A‑3** | Existing product catalog is stored in the same PostgreSQL instance with a `products` table. | Enables efficient similarity checks. |
| **A‑4** | Users of the CLI are authenticated via a local `.env` file containing API keys. | Simplifies local dev. |
| **A‑5** | The system will run in a Dockerized environment for deployment. | Standardizes runtime. |

---

## 6. Deliverables

1. **Source Code** – Complete implementation of all functional requirements.
2. **Unit & Integration Tests** – ≥80% coverage, CI pipeline integration.
3. **API Documentation** – OpenAPI spec and Swagger UI.
4. **CLI Documentation** – Usage examples and help output.
5. **Deployment Scripts** – Dockerfile, docker‑compose, and Helm chart (optional).
6. **Observability Setup** – Prometheus metrics and Grafana dashboards (templates).

---

## 7. Acceptance Criteria

- All functional requirements pass automated tests.
- Performance benchmarks meet NFR‑1 and NFR‑2.
- Security scans (Bandit, Snyk) show no high‑severity findings.
- API documentation is accessible at `/docs`.
- CLI commands produce expected output and logs.

---

### Revision History

| Date | Version | Author | Notes |
|------|---------|--------|-------|
| 2026‑06‑10 | 1.0.0 | AI Lead | Initial requirements draft |

---
