# STORIES.md  

## Project: concept‑sentinel  
**Goal:** Build a production‑ready service that detects AI‑generated concept‑art plagiarism and protects the originality of human artists.  

---  

## Epics & Backlog  

| Epic | Description | MVP Priority |
|------|-------------|--------------|
| **E1 – Ingestion & Indexing** | Collect, store, and index reference concept‑art assets (human‑created) for fast similarity search. | ✅ |
| **E2 – AI‑Generated Detection Engine** | Run incoming artwork through detection models (vLLM + SGLang) to produce a plagiarism score. | ✅ |
| **E3 – API & Integration Layer** | Expose REST/GraphQL endpoints for external tools (DAWs, asset managers, marketplaces). | ✅ |
| **E4 – Dashboard & Reporting** | UI for artists & admins to view results, audit logs, and request appeals. | ✅ |
| **E5 – Alert & Enforcement** | Automated notifications and optional takedown actions when plagiarism is confirmed. | ✅ |
| **E6 – Continuous Learning Loop** | Capture false‑positive/negative feedback to fine‑tune models and improve precision. | ✅ |
| **E7 – Security & Compliance** | Ensure data privacy, role‑based access, and GDPR/CCPA compliance. | ✅ |
| **E8 – Scalability & Ops** | Deploy on Kubernetes with autoscaling, monitoring, and CI/CD pipelines. | ✅ |

---  

## User Stories  

### Epic E1 – Ingestion & Indexing  

| # | Story | Acceptance Criteria |
|---|-------|----------------------|
| **E1‑S1** | **As a** **Content Curator**, **I want** to bulk‑upload folders of human‑created concept art, **so that** they become searchable reference assets. | 1. UI drag‑and‑drop or CLI accepts ZIP/Folder.<br>2. Files are validated (image type, size ≤ 20 MB).<br>3. Metadata (artist, title, tags) can be supplied via CSV or UI form.<br>4. Assets are stored in object storage and indexed in pgvector within 5 min. |
| **E1‑S2** | **As a** **System**, **I want** to generate a vector embedding for each uploaded asset using the vetted vLLM model, **so that** similarity queries are fast. | 1. Embedding generation is triggered automatically after upload.<br>2. Embedding stored in `concept_art_vectors` table with asset ID.<br>3. Process logs success/failure; failures are retried up to 3 times. |
| **E1‑S3** | **As a** **Admin**, **I want** to tag assets as “protected”, “public”, or “restricted”, **so that** access rules are enforced downstream. | 1. Tag can be set via UI or API.<br>2. Tag change updates ACLs instantly.<br>3. Audit entry recorded with user, timestamp, and previous tag. |

### Epic E2 – AI‑Generated Detection Engine  

| # | Story | Acceptance Criteria |
|---|-------|----------------------|
| **E2‑S1** | **As a** **Artist**, **I want** to submit a new concept‑art file for plagiarism check, **so that** I know if it contains AI‑generated elements. | 1. Upload endpoint accepts PNG/JPEG up to 30 MB.<br>2. Service returns a JSON payload with `plagiarism_score` (0‑100) and `matched_refs` (top‑5).<br>3. Score ≥ 70 is flagged as “high risk”. |
| **E2‑S2** | **As a** **Detection Service**, **I want** to run the submitted image through a structured generation model (SGLang) that extracts semantic tokens, **so that** similarity is measured on both visual and textual cues. | 1. Image is passed to SGLang → token list.<br>2. Tokens are embedded with the same vector space as reference assets.<br>3. Combined similarity = 0.6 × visual + 0.4 × semantic (configurable). |
| **E2‑S3** | **As a** **Product Owner**, **I want** the detection latency to stay under 2 seconds for 95 % of requests, **so that** the tool feels instantaneous. | 1. Load‑test with 500 RPS shows 95 th percentile ≤ 2 s.<br>2. Autoscaling triggers when CPU > 70 % or queue length > 100. |
| **E2‑S4** | **As a** **Quality Engineer**, **I want** a deterministic test harness that verifies the same input always yields the same score, **so that** regression bugs are caught. | 1. Seeded random generators are fixed.<br>2. Test suite runs nightly; any score drift > 1 % fails the build. |

### Epic E3 – API & Integration Layer  

| # | Story | Acceptance Criteria |
|---|-------|----------------------|
| **E3‑S1** | **As a** **Marketplace Integrator**, **I want** a REST endpoint `/v1/check` with API‑key auth, **so that** I can embed plagiarism checks into my upload flow. | 1. Endpoint follows OpenAPI spec.<br>2. Returns HTTP 200 with JSON payload (score, matches).<br>3. Invalid/expired API key returns 401. |
| **E3‑S2** | **As a** **Third‑Party Tool**, **I want** a GraphQL mutation `checkConceptArt(input: File!)`, **so that** I can request checks alongside other queries. | 1. Mutation resolves within the same latency SLA as REST.<br>2. Supports batch of up to 10 files per request. |
| **E3‑S3** | **As a** **Developer**, **I want** SDKs (Python & TypeScript) that wrap the API, **so that** integration is frictionless. | 1. Published to PyPI and npm.<br>2. Includes typed request/response models and example scripts. |

### Epic E4 – Dashboard & Reporting  

| # | Story | Acceptance Criteria |
|---|-------|----------------------|
| **E4‑S1** | **As an** **Artist**, **I want** a web dashboard that shows my recent checks, scores, and matched references, **so that** I can track originality over time. | 1. List view with pagination, sortable by date/score.<br>2. Clicking a result opens a modal with side‑by‑side image comparison.<br>3. Export to CSV button. |
| **E4‑S2** | **As an** **Admin**, **I want** aggregate reports (daily/weekly) of total checks, high‑risk detections, and false‑positive rates, **so that** I can monitor platform health. | 1. Charts rendered via Chart.js.<br>2. Data refreshed every hour.<br>3. Exportable PDF report. |
| **E4‑S3** | **As an** **Artist**, **I want** to submit an “appeal” on a flagged result, **so that** a human reviewer can reassess. | 1. Appeal button opens a form (reason, optional evidence).<br>2. Appeal status tracked (Pending → Resolved).<br>3. Notification email sent to reviewer queue. |

### Epic E5 – Alert & Enforcement  

| # | Story | Acceptance Criteria |
|---|-------|----------------------|
| **E5‑S1** | **As a** **System**, **I want** to send an email/SMS alert when a score ≥ 85 is detected, **so that** the artist is immediately informed. | 1. Configurable alert thresholds.<br>2. Email template includes score, matches, and appeal link.<br>3. Delivery success logged. |
| **E5‑S2** | **As a** **Marketplace Owner**, **I want** an optional automatic takedown flag that can be toggled per account, **so that** infringing assets are removed without manual steps. | 1. Setting stored per‑account in DB.<br>2. When enabled, high‑risk assets are marked `REMOVED` and hidden from public listings.<br>3. Audit trail records action and actor (system). |

### Epic E6 – Continuous Learning Loop  

| # | Story | Acceptance Criteria |
|---|-------|----------------------|
| **E6‑S1** | **As a** **Data Scientist**, **I want** to export all appeal outcomes and model predictions to a training dataset, **so that** we can retrain the detection model quarterly. | 1. Export job produces CSV with columns: `asset_id, score, appeal_result, timestamp`.<br>2. Stored in secure bucket with versioning. |
| **E6‑S2** | **As a** **System**, **I want** to automatically schedule a nightly job that re‑indexes any newly approved assets, **so that** the reference library stays current. | 1. Cron runs at 02:00 UTC.<br>2. Logs number of assets re‑indexed; failures trigger alert. |

### Epic E7 – Security & Compliance  

| # | Story | Acceptance Criteria |
|---|-------|----------------------|
| **E7‑S1** | **As a** **Compliance Officer**, **I want** all stored images to be encrypted at rest and access‑controlled, **so that** we meet GDPR requirements. | 1. Object storage uses SSE‑AES‑256.<br>2. IAM policies enforce least‑privilege.<br>3. Quarterly audit reports generated. |
| **E7‑S2** | **As a** **User**, **I want** the ability to request deletion of my uploaded images, **so that** I control my personal data. | 1. “Delete my data” button triggers immediate removal from storage and vector DB.<br>2. Confirmation email sent. |
| **E7‑S3** | **As a** **Security Engineer**, **I want** rate‑limiting (100 req/min per API key) and IP‑allowlist support, **so that** abuse is mitigated. | 1. Exceeds limit returns 429 with Retry‑After header.<br>2. Configurable allowlist via admin UI. |

### Epic E8 – Scalability & Ops  

| # | Story | Acceptance Criteria |
|---|-------|----------------------|
| **E8‑S1** | **As a** **DevOps Engineer**, **I want** the service containerized with Helm charts, **so that** we can deploy to any Kubernetes cluster. | 1. Helm chart includes deployments, services, ingress, configmaps, secrets.<br>2. Chart versioned and published to internal chart repo. |
| **E8‑S2** | **As a** **Site Reliability Engineer**, **I want** Prometheus metrics (`request_latency_seconds`, `plagiarism_score_histogram`, `error_total`) exposed, **so that** we can monitor health. | 1. `/metrics` endpoint conforms to Prometheus format.<br>2. Grafana dashboards pre‑built and imported. |
| **E8‑S3** | **As a** **Release Manager**, **I want** a CI/CD pipeline that runs unit, integration, and performance tests and automatically rolls out to staging on merge, **so that** releases are safe. | 1. GitHub Actions workflow with stages: lint → unit → integration → perf → deploy.<br>2. Deployment to staging triggers smoke‑test suite; only on success does it tag the release. |

---  

## MVP Scope (first ship)  

1. **E1‑S1, E1‑S2, E1‑S3** – Asset ingestion & indexing.  
2. **E2‑S1, E2‑S2, E2‑S3, E2‑S4** – Detection engine with latency SLA.  
3. **E3‑S1, E3‑S2, E3‑S3** – Public API & SDKs.  
4. **E4‑S1, E4‑S3** – Artist dashboard with appeal flow.  
5. **E5‑S1** – High‑risk alert notification.  
6. **E7‑S1, E7‑S2** – Core security/compliance.  
7. **E8‑S1, E8‑S2, E8‑S3** – Deployable Helm chart, monitoring, CI/CD.  

All other stories are planned for post‑MVP iterations, feeding back into continuous improvement and expanded enforcement capabilities.
