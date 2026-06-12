# REQUIREMENTS.md

## Project: concept‑sentinel  
**Purpose** – Detect and prevent AI‑generated concept‑art plagiarism, preserving the originality and credibility of human concept artists.  
**Scope** – Web service with REST API, web dashboard, and optional design‑tool plugin. The system ingests images, runs AI‑generation and plagiarism detection, and returns a confidence score and provenance report.

---

## 1. Functional Requirements

| ID | Requirement | Description | Acceptance Criteria |
|----|-------------|-------------|---------------------|
| **FR‑1** | Image Ingestion | Accept single or batch image uploads (JPEG/PNG/WebP) up to 10 MB each. | API returns 200 OK with image ID; size limit enforced. |
| **FR‑2** | AI‑Generation Detection | Classify each image as “AI‑generated” or “human‑created” using a trained classifier. | Accuracy ≥ 92 % on internal test set; confidence score ≥ 0.7 for AI flag. |
| **FR‑3** | Plagiarism Detection | Compare image against internal database of known works (including AI‑generated and human). | Return similarity score ≥ 0.8 flagged as plagiarism; false‑positive rate ≤ 3 %. |
| **FR‑4** | Originality Scoring | Provide a composite originality score (0–100) combining AI‑generation and plagiarism metrics. | Score formula documented; score displayed in UI and API. |
| **FR‑5** | Provenance Report | Generate a PDF/JSON report containing image metadata, detection results, similarity matches, and recommended actions. | Report contains all required fields; downloadable via API. |
| **FR‑6** | REST API | Endpoints: `/upload`, `/status`, `/report`, `/settings`. | Swagger/OpenAPI spec available; authentication via OAuth2. |
| **FR‑7** | Web Dashboard | User interface for uploading, monitoring jobs, viewing reports, and managing settings. | Responsive design; accessible on Chrome/Firefox/Edge. |
| **FR‑8** | Design‑Tool Plugin | Optional plugin for Photoshop/Illustrator that submits current canvas to the service. | Plugin installs via marketplace; single‑click submit. |
| **FR‑9** | User Authentication | OAuth2 with role‑based access (Artist, Reviewer, Admin). | JWT tokens issued; role enforced on endpoints. |
| **FR‑10** | Admin Controls | CRUD for image libraries, model versions, and system settings. | Admin UI available; audit logs maintained. |
| **FR‑11** | Data Privacy | Store images and results encrypted at rest and in transit; comply with GDPR & CCPA. | Encryption keys rotated quarterly; data retention policy documented. |
| **FR‑12** | Multi‑Tenant Isolation | Separate data and model usage per tenant. | Tenant ID included in all requests; no cross‑tenant leakage. |
| **FR‑13** | Model Versioning | Ability to deploy new AI‑generation and plagiarism models without downtime. | Blue‑green deployment pipeline; rollback on failure. |
| **FR‑14** | Notification System | Email/SMS alerts for high‑risk plagiarism or AI‑generation flags. | Configurable thresholds; delivery confirmed. |
| **FR‑15** | Audit Logging | Record all user actions, model inference requests, and system events. | Logs stored for 90 days; searchable via UI. |

---

## 2. Non‑Functional Requirements

| ID | Requirement | Description | Acceptance Criteria |
|----|-------------|-------------|---------------------|
| **NFR‑1** | Performance | Average inference latency ≤ 2 s for 512 × 512 images; 95 % of requests under 3 s. | Load‑test results meet thresholds. |
| **NFR‑2** | Scalability | Handle 10 k concurrent users with auto‑scaling on Kubernetes. | Kubernetes HPA scales pods; no request timeouts. |
| **NFR‑3** | Reliability | 99.9 % uptime SLA; automated failover for critical services. | Uptime monitoring reports ≥ 99.9 %. |
| **NFR‑4** | Security | OWASP Top‑10 mitigations, OWASP API Security 2023, TLS 1.3. | Pen‑test results show no critical vulnerabilities. |
| **NFR‑5** | Availability | 24/7 operation with 30 min recovery time objective (RTO). | Incident response drills meet RTO. |
| **NFR‑6** | Maintainability | Codebase follows Go/Node.js best practices,
