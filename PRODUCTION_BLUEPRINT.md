# Production Build & Deployment Blueprint: Sovereign Multi-Repo Ecosystem
**Target Environment:** 11-Repository Sovereign / Rick C-137 / Portal AI Ecosystem  
**Audit Reference Date:** October 2026  
**Status:** Canonical Engineering Blueprint, Code Artifacts & Deployment Architecture

---

## Table of Contents
1. [Executive Ecosystem Architecture](#1-executive-ecosystem-architecture)
2. [Standardized Core Configuration Files](#2-standardized-core-configuration-files)
3. [Universal CI/CD Pipeline](#3-universal-cicd-pipeline)
4. [Containerization & Orchestration](#4-containerization--orchestration)
5. [Production Security Hardening](#5-production-security-hardening)
6. [Inter-Service API Contract](#6-inter-service-api-contract--shared-protocol)
7. [Installation & Bootstrap Guide](#7-installation-local-development--bootstrap-guide)
8. [Repo-by-Repo Completion Blueprint](#8-repo-by-repo-actionable-completion-blueprint)
9. [Production Deployment Runbook](#9-production-release--deployment-runbook)

---

## 1. Executive Ecosystem Architecture

The portfolio consists of four distinct operational layers across 11 repositories:

```
+-----------------------------------------------------------------------------------+
|                            PORTAL & INTERACTION LAYER                             |
|  - sovereign-portal-c137 (Primary Unified Monorepo & UI Control Center)           |
|  - oracle-c137-harmony-hub (AI Stack, Voice, WebGPU & Model Orchestration)        |
|  - rick-portal-app / rick-c137 (Targeted Consumable Frontends / App Shells)       |
+-----------------------------------------------------------------------------------+
                                        |  HTTP / WebSocket / gRPC
                                        v
+-----------------------------------------------------------------------------------+
|                             LOGIC & ENGINE LAYER                                  |
|  - sovereign-blob (Core Design, Joinery, Structural & Nesting Engine - 91 Tests)  |
|  - the-sovereign-blob (Web-Accessible Engine Client / API Interface)             |
|  - codex-control-deck (Workflow Orchestrator & Rule Catalog)                      |
+-----------------------------------------------------------------------------------+
                                        |  Internal Event Bus / REST
                                        v
+-----------------------------------------------------------------------------------+
|                           BRIDGES & HARDWARE GATEWAYS                             |
|  - sovereign-bridge (Cross-Platform API & Protocol Translator)                    |
|  - sovereign-b2d (Blob-to-Device / Binary Output Pipeline)                         |
|  - xbox-plasma-sovereign (Flask Hardware Controller / Peripheral Bridge)          |
+-----------------------------------------------------------------------------------+
                                        |  Telemetry & Logs
                                        v
+-----------------------------------------------------------------------------------+
|                            OBSERVABILITY & TELEMETRY                              |
|  - prometheus-a17 (Metrics Ingestion, Health Heartbeats, Alert Engine)             |
+-----------------------------------------------------------------------------------+
```

---

## 2. Standardized Core Configuration Files

All repositories require unified configuration hygiene to eliminate build drift and secret leaks.

### A. Universal `.gitignore`
Placed in all repo roots — prevents secret leaks and build artifacts.

### B. Standard `.env.example`
Templates for all required environment variables across the ecosystem.

### C. Unified `tsconfig.base.json`
Shared TypeScript configuration for consistency.

### D. Standard `.eslintrc.json` & `.prettierrc`
Code quality standards enforced uniformly.

---

## 3. Universal CI/CD Pipeline

Place `.github/workflows/ci.yml` in each repository to enable:
- Linting & type checking
- Security scanning & dependency audit
- Unit & integration tests
- Production build verification

---

## 4. Containerization & Orchestration

### Multi-Stage Dockerfile
Node.js → Production nginx runtime with health checks.

### docker-compose.yml
Orchestrates all five core services in one command:
- sovereign-portal (UI, port 3000)
- sovereign-blob (Engine, port 4000)
- oracle-harmony-hub (AI, port 5000)
- prometheus-telemetry (Metrics, port 9090)
- postgres (Database, port 5432)

---

## 5. Production Security Hardening

### nginx.conf
Secures edge traffic:
- X-Frame-Options: DENY
- X-Content-Type-Options: nosniff
- Strict-Transport-Security (HSTS)
- Content-Security-Policy with wasm-unsafe-eval
- Gzip compression
- Static asset caching (6M)
- Healthcheck route (/healthz)

---

## 6. Inter-Service API Contract & Shared Protocol

### Design Request Schema
```json
{
  "job_id": "uuid",
  "parameters": {
    "dimensions": { "width_mm": 900, "height_mm": 1800, "depth_mm": 300 },
    "material": "plywood_18mm",
    "load_bearing_target_kg": 100
  }
}
```

### Design Response Schema
```json
{
  "job_id": "uuid",
  "status": "COMPLETED",
  "metrics": {
    "total_volume_mm3": 450000.0,
    "calculated_deflection_mm": 0.42,
    "safety_factor": 3.8,
    "sheet_efficiency_percentage": 88.5
  },
  "artifacts": {
    "dxf_download_url": "/api/v1/artifacts/job-uuid.dxf",
    "cutlist_json_url": "/api/v1/artifacts/job-uuid.cutlist.json"
  }
}
```

---

## 7. Installation, Local Development & Bootstrap Guide

### System Requirements
- Node.js >= 20.12.0 LTS
- Python >= 3.11
- Docker & Docker Compose v2.20+
- Git

### One-Command Setup
Run the initialization script to bootstrap all repos:
```bash
bash scripts/init-sovereign-environment.sh
```

---

## 8. Repo-by-Repo Actionable Completion Blueprint

| Repository | Grade | Role | Critical Actions |
|---|---|---|---|
| **sovereign-blob** | 🟢 8.5/10 | Core Engine | Expose REST/gRPC endpoint. Add TypeScript definitions. |
| **sovereign-portal-c137** | 🟢 7.5/10 | Master Monorepo & UI | Wire Docker network. Add workspace orchestration. |
| **oracle-c137-harmony-hub** | 🟡 7.0/10 | AI / WebGPU | Pin dependencies. Lock ORM schemas. Verify env vars. |
| **prometheus-a17** | 🟢 6.5/10 | Observability | Add JWT auth. Implement `/metrics` endpoint. |
| **rick-portal-app** | 🟡 5.5/10 | Client App | Consolidate UI or create tenant demo. |
| **rick-c137** | 🟡 5.0/10 | Playground | Add `.gitignore`. Purge scaffolds or merge. |
| **xbox-plasma-sovereign** | 🟡 5.0/10 | Hardware | Add Pydantic models. Rate limiting. Failsafes. |
| **the-sovereign-blob** | 🟠 4.5/10 | Legacy | Deprecate or repurpose as API gateway. |
| **sovereign-bridge** | 🟠 4.0/10 | Integration | Define typed RPC client. Strip boilerplate. |
| **sovereign-b2d** | 🟠 4.0/10 | Output Pipeline | Implement DXF/G-code streaming. Memory limits. |
| **codex-control-deck** | 🟠 4.0/10 | Console | Build functional dashboard. Wire metrics. |

---

## 9. Production Release & Deployment Runbook

### Pre-Deployment Checklist
1. **Secrets Audit:** No `.env` or `.pem` files committed
2. **Dependency Freeze:** Lock files (`package-lock.json`, `pnpm-lock.yaml`) committed
3. **Database Migrations:** Run ORM migration scripts
4. **Smoke Testing:** Verify all `/healthz` endpoints

### Deployment Sequence
```bash
# 1. Pull latest
git submodule update --init --recursive

# 2. Build production images
docker compose -f docker-compose.yml build --no-cache

# 3. Restart services
docker compose -f docker-compose.yml up -d --remove-orphans

# 4. Verify cluster health
docker compose ps
curl -f http://localhost:3000/healthz
curl -f http://localhost:4000/healthz
```

---

**Blueprint verified and documented for the Sovereign Portfolio ecosystem.**