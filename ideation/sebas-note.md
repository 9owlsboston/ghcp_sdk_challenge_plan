# Security Enterprise Browser Agentic System (SEBAS) Notes

A Microsoft 365 Copilot declarative agent that can autonomously navigate, read, and take actions across multiple enterprise web applications on behalf of users.

Key capabilities:

Browser automation via J-browser-agents (headless browser pool) — navigating pages, extracting content, filling forms, submitting actions
Dual-path interaction — prefers native REST/GraphQL APIs when available, falls back to DOM scraping
Multi-app orchestration — handles workflows spanning apps like ServiceNow, Jira, Workday, Grafana, SEC filings sites, etc., all from a single natural language prompt
Enterprise security — Azure Entra ID SSO, URL allowlisting, human-in-the-loop approval gates for destructive actions, full audit logging
AI safety — Azure AI Content Safety screening on inputs/outputs
Azure-native infrastructure — Azure OpenAI (GPT-4o), Azure Container Apps, Cosmos DB, Key Vault, Application Insights, deployed via Bicep IaC
In short: a user types one prompt like "Pull competitor financials, check the P1 outage status, and onboard the new VP" and the agent decomposes it into sub-tasks, authenticates into the relevant apps, executes the work (with approval for write actions), and delivers a compiled result — all within the M365 Copilot surface (Teams, Outlook, etc.).

---

# Secure Enterprise Browser Agentic System — Setup & Test Guide

## Overview

This project is a **Microsoft 365 Copilot declarative agent** that securely navigates, reads, and acts across enterprise web applications. It uses Azure OpenAI for planning, Playwright for browser automation, and a dual-path strategy (native REST/GraphQL APIs preferred, DOM scraping as fallback).

---

## Prerequisites

| Requirement | Minimum Version | Notes |
|---|---|---|
| **Node.js** | >= 20.0.0 | Required by `engines` in package.json |
| **npm** | >= 10 | Comes with Node 20+ |
| **Azure CLI** | Latest | Only needed for Azure deployment |
| **Docker** | Latest | Only needed for containerized runs |
| **Azure Subscription** | — | Only needed for full deployment |
| **M365 Admin Access** | — | Only needed to sideload the Copilot agent |

---

## 1. Install Dependencies

```bash
cd /path/to/Secure-Enterprise-Browser-Agentic-System
npm install
```

This installs all runtime and dev dependencies including TypeScript, Vitest, ESLint, Playwright, Express, Azure SDKs, etc.

---

## 2. Run Tests

The test suite uses **Vitest** and is organized into three tiers. Unit and smoke tests run fully offline — no Azure credentials or services required.

### Run all tests

```bash
npm test
```

### Run tests in watch mode (re-runs on file changes)

```bash
npm run test:watch
```

### Run tests with coverage report

```bash
npm run test:coverage
```

Coverage output formats: text (console), JSON, and HTML (in the `coverage/` directory).

### Test structure

```
tests/
├── e2e/
│   └── smoke.test.ts              # Smoke tests (planner keyword routing)
├── integration/
│   ├── security-flows/
│   │   ├── approval-gate.test.ts  # Approval gate integration
│   │   └── security-gate.test.ts  # Security pipeline integration
│   └── workflows/
│       ├── cross-skill-chain.test.ts
│       └── orchestrate-workflow.test.ts
└── unit/
    ├── api/                       # REST/GraphQL connector tests
    ├── browser/                   # Browser pool, DOM parser tests
    ├── orchestrator/              # Task planner, tool router tests
    ├── security/                  # Auth, audit, content safety tests
    └── skills/                    # Individual skill tests
```

---

## 3. Type-Check & Lint

```bash
# Type-check (no emit)
npm run typecheck

# Lint
npm run lint

# Lint with auto-fix
npm run lint:fix

# Format code with Prettier
npm run format
```

---

## 4. Build

Compiles TypeScript source (`src/`) to JavaScript (`dist/`):

```bash
npm run build
```

Output goes to `dist/` with source maps and declaration files.

---

## 5. Run Locally (Dev Mode)

The Express server starts on port 3000 by default. All Azure service endpoints are **optional** — the app boots without them for local development.

```bash
npm run dev
```

This uses `tsx watch` for hot-reload during development.

### Verify it's running

```bash
curl http://localhost:3000/health
# → {"requestId":"<uuid>","status":"healthy"}

curl http://localhost:3000/ready
# → readiness check (may vary depending on configured services)
```

---

## 6. Environment Variables

All configuration is validated via Zod schema in `src/config.ts`. Every Azure-related variable is optional for local dev.

| Variable | Purpose | Default |
|---|---|---|
| `NODE_ENV` | Environment mode | `development` |
| `PORT` | Server listen port | `3000` |
| `AZURE_OPENAI_ENDPOINT` | Azure OpenAI service URL | *(optional)* |
| `AZURE_OPENAI_MODEL` | Model deployment name | `gpt-4o` |
| `CONTENT_SAFETY_ENDPOINT` | Azure AI Content Safety URL | *(optional)* |
| `CONTENT_SAFETY_KEY` | Content Safety API key | *(optional)* |
| `CONTENT_SAFETY_BLOCK_THRESHOLD` | Block threshold (1–7) | `4` |
| `COSMOS_ENDPOINT` | Cosmos DB endpoint | *(optional)* |
| `COSMOS_KEY` | Cosmos DB key | *(optional)* |
| `COSMOS_DATABASE` | Database name | `browser-agent-db` |
| `COSMOS_AUDIT_CONTAINER` | Audit logs container | `audit-logs` |
| `COSMOS_WORKFLOW_CONTAINER` | Workflow state container | `workflow-state` |
| `COSMOS_MEMORY_CONTAINER` | Conversation memory container | `conversation-memory` |
| `KEY_VAULT_URL` | Azure Key Vault URL | *(optional)* |
| `ALLOWED_URL_PATTERNS` | Comma-separated URL allowlist | `https://*.microsoft.com/*` |
| `APPROVAL_TIMEOUT_MS` | Timeout for human approval | `300000` (5 min) |
| `MAX_BROWSER_CONCURRENCY` | Max parallel browser sessions | `10` |
| `SESSION_TTL_MS` | Session time-to-live | `1800000` (30 min) |
| `GRAPH_BASE_URL` | Microsoft Graph base URL | `https://graph.microsoft.com/v1.0` |
| `TARGET_APP_SCOPE` | OAuth scope for target apps | `api://browser-agent/.default` |
| `GRAPH_DEFAULT_SCOPE` | OAuth scope for Graph API | `https://graph.microsoft.com/.default` |

To use a `.env` file, you can add a package like `dotenv` or export variables in your shell:

```bash
export PORT=3000
export ALLOWED_URL_PATTERNS="https://*.microsoft.com/*,https://*.servicenow.com/*"
npm run dev
```

---

## 7. Docker

Build and run in a container (includes Playwright Chromium):

```bash
# Build
docker build -t browser-agent .

# Run
docker run -p 3000:3000 \
  -e AZURE_OPENAI_ENDPOINT=https://your-instance.openai.azure.com \
  browser-agent

# Health check
curl http://localhost:3000/health
```

---

## 8. Deploy to Azure

### Option A: Azure Developer CLI (azd)

The project includes an `azure.yaml` configured for Azure Container Apps:

```bash
# Login
azd auth login

# Deploy everything (infra + app)
azd up
```

### Option B: Bicep directly

```bash
# Deploy dev environment
az deployment group create \
  --resource-group <your-rg> \
  --template-file infra/main.bicep \
  --parameters infra/parameters/dev.bicepparam

# Deploy staging
az deployment group create \
  --resource-group <your-rg> \
  --template-file infra/main.bicep \
  --parameters infra/parameters/staging.bicepparam

# Deploy production
az deployment group create \
  --resource-group <your-rg> \
  --template-file infra/main.bicep \
  --parameters infra/parameters/prod.bicepparam
```

### Azure resources provisioned

| Resource | Purpose |
|---|---|
| Azure Container Apps | Hosts the agent runtime |
| Azure OpenAI Service | GPT-4o for task planning |
| Azure Cosmos DB | Audit logs, workflow state, conversation memory |
| Azure Key Vault | Secrets and certificates |
| Azure AI Content Safety | Input/output screening |
| Application Insights + Azure Monitor | Observability and alerting |

---

## 9. Sideload the M365 Copilot Agent

After deploying the backend, package and upload the agent to your M365 tenant:

1. Zip the `app-package/` directory (contains `manifest.json`, `declarativeAgent.json`, `browserPlugin.json`, icons, and OpenAPI specs)
2. Upload via M365 Admin Center or Teams Developer Portal
3. The agent appears as `@BrowserAgent` in Copilot Chat, Teams, and Outlook

---

## Quick Start Summary

```bash
# Clone and enter the repo
cd Secure-Enterprise-Browser-Agentic-System

# Install
npm install

# Test (no Azure needed)
npm test

# Build
npm run build

# Run locally
npm run dev

# Verify
curl http://localhost:3000/health
```
