# AI Departments Platform - Documentation Index

## Project Overview

**AI Departments Platform** is a web platform that empowers micro-entrepreneurs to launch and manage businesses using virtual departments powered by AI agents. Instead of hiring teams or juggling multiple SaaS tools, users activate specialized "Departments" (Marketing, Customer Service, Design, etc.) with pre-configured AI agents that collaborate to perform business tasks.

## Project Information

- **Project Owner:** Felipe PM
- **Technical Owner:** Claude AI
- **Repository:** `/Users/felipefonseca/Dev/business-ai`
- **Stack:** Next.js + React + Tailwind (Frontend), FastAPI + Postgres + Redis (Backend), GCP Infrastructure
- **Last Updated:** 2025-09-13
- **Current Phase:** Section 0 - Foundation Setup

## Documentation Structure

### 0. Index, Conventions & Repository Setup
| Document | Status | Owner | Last Updated | Description |
|----------|--------|-------|--------------|-------------|
| [INDEX.md](./INDEX.md) | ✅ DONE | Felipe PM | 2025-09-13 | Project overview and documentation index |
| [CONTRIBUTING.md](./CONTRIBUTING.md) | ✅ DONE | Claude | 2025-09-13 | Development workflow and contribution guidelines |
| [CODING_STANDARDS.md](./CODING_STANDARDS.md) | ✅ DONE | Claude | 2025-09-13 | Code quality standards and conventions |
| [ENV_SETUP.md](./ENV_SETUP.md) | ✅ DONE | Claude | 2025-09-13 | Environment setup and deployment guides |

### 1. Strategy & Product (TODO)
| Document | Status | Owner | Description |
|----------|--------|-------|-------------|
| PRD.md | 📋 TODO | Felipe PM | Product Requirements Document (MVP → V2) |
| MRD.md | 📋 TODO | Felipe PM | Market Requirements & Benchmark |
| OKRs_Q1_Q2.md | 📋 TODO | Felipe PM | Quarterly objectives and key results |
| PRICING_PACKAGES.md | 📋 TODO | Felipe PM | Monetization strategy and plans |
| PERSONAS_JTBD.md | 📋 TODO | Felipe PM | User personas and jobs-to-be-done |

### 2. UX & Content (TODO)
| Document | Status | Owner | Description |
|----------|--------|-------|-------------|
| UX_SPEC.md | 📋 TODO | Claude | User experience specifications |
| DESIGN_SYSTEM.md | 📋 TODO | Claude | Design system and component library |
| COPY_TONE_GUIDE.md | 📋 TODO | Felipe PM | Copywriting guidelines and tone of voice |
| LOCALIZATION_I18N.md | 📋 TODO | Claude | Internationalization specifications |
| TEMPLATE_LIBRARY.md | 📋 TODO | Felipe PM | Business template catalog |

### 3. Architecture & Engineering (TODO)
| Document | Status | Owner | Description |
|----------|--------|-------|-------------|
| ARCHITECTURE.md | 📋 TODO | Claude | High-level system architecture |
| SEQUENCE_DIAGRAMS.md | 📋 TODO | Claude | Critical user flow sequences |
| DB_SCHEMA.md | 📋 TODO | Claude | Database schema and relationships |
| API_SPEC_OPENAPI.yaml | 📋 TODO | Claude | OpenAPI specification |
| INTEGRATIONS_REGISTRY.md | 📋 TODO | Claude | External integrations catalog |
| DEPLOYMENT_PIPELINE.md | 📋 TODO | Claude | CI/CD and deployment strategy |
| COST_MODEL.md | 📋 TODO | Claude | Cost optimization and resource planning |

### 4. AI, Agents & Orchestration (TODO)
| Document | Status | Owner | Description |
|----------|--------|-------|-------------|
| AGENT_DEPARTMENT_CATALOG.md | 📋 TODO | Claude | Department and agent specifications |
| ORCHESTRATOR_SPEC.md | 📋 TODO | Claude | Multi-agent orchestration system |
| PROMPT_LIBRARY.md | 📋 TODO | Claude | Prompt templates and system messages |
| MODEL_SELECTION.md | 📋 TODO | Claude | AI model selection and fallback strategies |
| EVAL_HARNESS.md | 📋 TODO | Claude | AI evaluation and testing framework |
| DATASETS_POLICY.md | 📋 TODO | Claude | Data governance and training policies |

## Directory Structure

```
/business-ai/
├── docs/                    # Documentation
├── frontend/               # Next.js application (planned)
├── backend/                # FastAPI application (planned)
├── infra/                  # Infrastructure as Code (planned)
├── scripts/                # Development scripts (planned)
└── tests/                  # End-to-end tests (planned)
```

## Naming Conventions

### Files and Directories
- **Documentation:** UPPERCASE.md (e.g., `PRD.md`, `ARCHITECTURE.md`)
- **Code files:** snake_case for Python, camelCase for JavaScript/TypeScript
- **Directories:** lowercase with hyphens (e.g., `ai-agents`, `user-management`)
- **Environment files:** `.env.example`, `.env.local`, `.env.production`

### Git Conventions
- **Branches:** `feature/description`, `fix/description`, `docs/description`
- **Commits:** Conventional commits format (see CONTRIBUTING.md)
- **Tags:** Semantic versioning (v1.0.0, v1.1.0-beta)

### API Conventions
- **Endpoints:** RESTful, lowercase with hyphens (`/api/v1/user-companies`)
- **Database:** snake_case for tables and columns
- **Environment Variables:** UPPERCASE_WITH_UNDERSCORES

## Status Tracking

### MVP Scope (Phase 1)
**Target:** Two departments - Marketing and Customer Service (CX)

**Core Features:**
- [ ] User onboarding wizard
- [ ] Company dashboard
- [ ] Marketing Department (social posts, basic SEO)
- [ ] Customer Service Department (WhatsApp integration)
- [ ] Billing integration (Stripe/Mercado Pago)
- [ ] Industry templates (2-3 initial)

### Success Metrics
- **North Star:** Companies created and activated
- **Key Metrics:** Time to first output, user retention, CAC/LTV
- **Technical:** Response time (<8s content generation, <3s replies)

## Update Process

1. **Documentation updates** require PR review from project owner
2. **Status changes** should be updated immediately after completion
3. **Last Updated** field must be maintained in YYYY-MM-DD format
4. **Cross-references** between documents should be kept in sync

## Quick Start

1. Review [ENV_SETUP.md](./ENV_SETUP.md) for environment configuration
2. Check [CONTRIBUTING.md](./CONTRIBUTING.md) for development workflow
3. Follow [CODING_STANDARDS.md](./CODING_STANDARDS.md) for code quality
4. Refer to specific section documentation as needed

---

**Note:** This is a living document. As the project evolves, this index will be updated to reflect the current state and priorities.