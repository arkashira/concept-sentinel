# ROADMAP.md – concept‑sentinel
*Version: 1.0 – Updated 2026‑06‑12*  

---

## 📖 Overview
**concept‑sentinel** is an Axentx product that **detects and prevents AI‑generated concept‑art plagiarism**.  
It protects human artists by flagging synthetic content, providing provenance reports, and integrating directly into the tools and platforms where artists showcase or sell their work.

The roadmap below is grounded in the current repository state, our existing data assets, and the validated need for trustworthy concept‑art pipelines.

---

## 🎯 MVP – “Launch‑Ready Guard”
**Target:** **Q2 2026** (12‑week sprint)  
**Goal:** Deliver a **shippable, self‑contained service** that can be used by individual artists and small studios to scan a single image and receive a confidence score + provenance report.

| Milestone | Description | Owner | Success Metric |
|-----------|-------------|-------|----------------|
| **[MVP] Data Ingestion & Labelling** | Curate a balanced dataset of **human‑created** vs **AI‑generated** concept art (≈ 30 k images). Use existing **auto** and **messages** datasets for metadata, and augment with public AI‑art sources. | Data Lead | ≥ 90 % label accuracy (human audit) |
| **[MVP] Baseline Detection Model** | Fine‑tune a **vLLM**‑based multimodal encoder (e.g., CLIP‑ViT) on the curated set. Export as a **SGLang**‑compatible inference service. | ML Engineer | ≥ 80 % ROC‑AUC on internal hold‑out |
| **[MVP] REST API** | Deploy a low‑latency inference endpoint (`/detect`) with JSON response: `{score, heatmap_url, provenance}`. Containerised with Docker, orchestrated via Kubernetes. | Backend Lead | ≤ 150 ms latency for 512 × 512 PNG |
| **[MVP] Web UI (single‑image scanner)** | Simple React app: upload → view score, heatmap, and downloadable PDF report. | Front‑end Lead | ≥ 95 % user‑task completion in usability test |
| **[MVP] Legal & Privacy Boilerplate** | Draft Terms of Service, GDPR‑compliant data handling, and an opt‑out mechanism for artists. | Legal/Compliance | Zero privacy incidents in beta |
| **[MVP] Beta Launch & Feedback Loop** | Closed beta with 20‑30 concept artists (via Axentx network). Capture NPS, false‑positive/negative logs, and iterate. | PM | NPS ≥ +30; < 5 % critical bugs |

> **MVP‑Critical** items are flagged with **[MVP]**. All other items in this phase support launch readiness but are not blockers.

---

## 🚀 Phase 1 – v1 “Enterprise Guard”
**Target:** **Q3‑Q4 2026** (6 months)  

### Theme – **Scalability & Integration**
| Feature | Description | Owner | KPI |
|---------|-------------|-------|-----|
| **Batch Processing API** | `/detect/batch` – up to 1 000 images per request, async job handling, result CSV. | Backend |
| **Platform Plugins** | Native extensions for **ArtStation**, **DeviantArt**, and **Behance** (OAuth‑based). Auto‑scan on upload. | Integrations |
| **Studio Dashboard** | Multi‑user workspace: project folders, audit trails, role‑based access, bulk export. | Front‑end |
| **Model Refresh Pipeline** | Automated weekly re‑training with new AI‑art samples (leveraging Axentx growth‑rate pipeline). | ML Ops |
| **Explainability Overlay** | Interactive heat‑map UI that highlights regions contributing to the AI‑generated score. | UX |
| **SLA & Monitoring** | 99.9 % uptime SLA, Prometheus + Grafana dashboards, alerting on latency spikes. | DevOps |
| **Pricing & Billing Engine** | Tiered subscription (Free ≤ 10 scans/mo, Pro ≤ 1 000 scans/mo, Enterprise). Stripe integration. | Business Ops |
| **Compliance Audits** | External security audit (SOC 2) and IP‑law review. | Legal |

**Success Criteria for v1**  
- **10 k** active paid accounts by end‑2026.  
- **≥ 92 %** detection AUC on a held‑out “real‑world” set.  
- **< 2 %** false‑positive rate reported by studios.

---

## 🌟 Phase 2 – v2 “Marketplace Protector”
**Target:** **2027 H1** (12 months)  

### Theme – **Real‑Time Protection & Ecosystem**
| Feature | Description | Owner | KPI |
|---------|-------------|
