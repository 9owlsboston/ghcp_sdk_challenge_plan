# SecPostureIQ — 20-Minute Final Presentation Script

> **Team:** Ying-Jung Chen (yjcmsft) + Velen Liang (9owlsboston)
> **Total time:** 20 minutes presentation + demo, then Q&A
> **Goal:** Win. Show this is the most complete, enterprise-ready, production-grade agent in the competition.

---

## Pre-Presentation Checklist

- [ ] **Portal tabs pre-loaded (for pain-point demo):**
  - [ ] Tab 1: Microsoft 365 Security — Secure Score page (`security.microsoft.com/securescore`)
  - [ ] Tab 2: Microsoft 365 Defender — Endpoints > Device inventory (`security.microsoft.com/endpoints/device-inventory`)
  - [ ] Tab 3: Microsoft 365 Defender — Policies > Safe Links or Safe Attachments (`security.microsoft.com/threatpolicy`)
  - [ ] Tab 4: Microsoft Purview — DLP Policies (`compliance.microsoft.com/datalossprevention`)
  - [ ] Tab 5: Microsoft Purview — Sensitivity Labels (`compliance.microsoft.com/informationprotection`)
  - [ ] Tab 6: Microsoft Entra — Conditional Access (`entra.microsoft.com/#view/Microsoft_AAD_ConditionalAccess`)
  - [ ] Tab 7: Microsoft Entra — PIM (`entra.microsoft.com/#view/Microsoft_Azure_PIMCommon`)
- [ ] Web chat UI running locally or on Container Apps (`http://localhost:8000`)
- [ ] Real M365 tenant connected (or mock data if needed — know which mode you're in)
- [ ] App Insights dashboard open in a browser tab (pre-loaded with traffic simulator data)
- [ ] GitHub repo open at README.md in another tab
- [ ] Architecture diagram slide ready
- [ ] Terminal window ready for showing test results and traffic simulation
- [ ] Backup: screenshots/video of each demo step in case of live demo failure

---

## Presentation Flow (20 minutes)

### ACT 1: THE HOOK (2 minutes) — ?

**[Slide: Title + Problem]**

> "Good [morning/afternoon]. I'm Velen, this is Ying-Jung. We built **SecPostureIQ**.
>
> Here's the problem we're solving — and it's not hypothetical.
>
> **Project 479** is Microsoft's internal 'Get to Green' campaign. It covers thousands of M365 E5 accounts where security adoption has drifted. Today, when an account team needs to assess a customer's security posture, here's what happens:
>
> They log into the Security portal. Pull Secure Score manually. Open Defender admin center — check Endpoint onboarding, Office 365 policies, Identity sensors, Cloud App connections — one by one. Then Purview compliance center — DLP policies, sensitivity labels, retention. Then Entra admin center — Conditional Access, PIM, Identity Protection. Then they write it all up in a Word document with recommendations.
>
> **That takes 1 to 2 weeks per customer. We have thousands of customers.**
>
> SecPostureIQ does the entire assessment in under 3 minutes. One conversation. Eight tools. Live data from Graph API. And it generates a remediation plan with actual PowerShell scripts — not just descriptions."

*[Pause. Let that sink in.]*

---

### ACT 1b: THE PAIN — LIVE PORTAL WALKTHROUGH (2 minutes) — Velen

> "Don't take my word for it — let me show you what that manual process actually looks like."

**[Browser is pre-loaded with 7 portal tabs. Flip through them briskly — spend ~15 seconds per portal group. The point is the sheer volume of context-switching, not reading every detail.]**

#### Portal 1: Microsoft Secure Score (security.microsoft.com/securescore)

**[Click tab — show the Secure Score overview page]**

> "Portal number one. This is Secure Score. You can see the number and the category breakdown — Identity, Data, Device, Apps, Infrastructure. But to understand what's actually dragging the score down, you have to click into each category, read each recommendation, figure out which ones matter for this customer's E5 licensing. Just this page — maybe 20 minutes if you're thorough."

#### Portal 2–3: Microsoft Defender (security.microsoft.com)

**[Click tab — show Defender device inventory, then click to threat policies]**

> "Portal two — still security.microsoft.com but now we're in Defender. Device inventory — how many endpoints are onboarded? Are we at 100% or 60%? Then Safe Links, Safe Attachments, anti-phishing policies — different sub-page. Then Defender for Identity — separate section, check sensor health. Defender for Cloud Apps — another page, connected apps count. Four workloads, four different navigation paths, all under 'Defender' but none in a single view."

#### Portal 4–5: Microsoft Purview (compliance.microsoft.com)

**[Click tab — show DLP policies page, then sensitivity labels page]**

> "Portal three — compliance.microsoft.com. Now we're in Purview. DLP policies — how many are active vs test-only? Sensitivity labels — are they published? Auto-labeling configured? Retention policies — yet another sub-page for Exchange, SharePoint, OneDrive, Teams. Each one is a separate click, separate table, separate filter."

#### Portal 6–7: Microsoft Entra (entra.microsoft.com)

**[Click tab — show Conditional Access policies list, then PIM]**

> "Portal four — entra.microsoft.com. Conditional Access — how many policies? Which ones are in report-only vs enforced? Then PIM — active vs eligible role assignments. Identity Protection risk policies. Access Reviews. Each a separate blade.
>
> **That's 4 portals, at least 12 sub-pages, and about an hour just to collect the raw data — before you even start writing the assessment.** And you have to do this for every customer."

**[Pause. Gesture at all the open tabs.]**

> "Now let me show you what happens when I ask SecPostureIQ the same thing in one sentence."

**[Transition to ACT 3 — live demo]**

---

### ACT 2: ARCHITECTURE (2 minutes) — ?

**[Slide: Architecture Diagram — show the Mermaid diagram from docs/architecture.md]**

> "Let me show you what's under the hood.
>
> **[Point to the diagram]**
>
> SecPostureIQ is built on the **GitHub Copilot SDK** — `CopilotClient`, `CopilotSession`, and the `Tool` registration API. The SDK is the thin client. The **Agent Runtime** — the Copilot CLI binary — is the brain. Planning, tool orchestration, multi-model routing, context management.
>
> **8 registered tools** — the same ones that replace those 4 portals and 12 sub-pages you just saw:
> - 4 **assessment tools**: Secure Score, Defender Coverage, Purview Policies, Entra Config — all hitting **Microsoft Graph Security API**. One tool per portal.
> - 1 **remediation tool**: **Azure OpenAI GPT-4o** generates prioritized plans with PowerShell scripts
> - 1 **scorecard tool**: RED/YELLOW/GREEN executive dashboard
> - 1 **Foundry IQ** + 1 **Fabric** tool for playbooks and longitudinal trends
>
> **[Point to middleware layer]** — 4 middleware layers on every request: Content Safety, PII Redaction, Distributed Tracing (App Insights), Immutable Audit Logger.
>
> **[Point to Azure services]** — Container Apps (scale-to-zero), Key Vault (Managed Identity), ACR, Bicep IaC — one `azd up` provisions everything. **688 tests**, **3 CI/CD workflows**, Trivy scanning, OIDC federation — zero stored secrets.
>
> **Two parallel interfaces** sharing the same tool layer:
> - **Copilot SDK CLI** (`CopilotClient` + `Tool` registration) — pure SDK integration
> - **FastAPI Web App** with 3 chat modes (keyword, LLM SSE streaming, Chainlit UI) — Azure OpenAI function calling
>
> Same 8 tools, same system prompt, same middleware, same schemas via `src/tools/definitions.py`."

---

### ACT 3: LIVE DEMO — FULL ASSESSMENT (7 minutes) - ? drives, ? narrates

**[Switch to browser — SecPostureIQ web chat UI]**

#### 3a. The Contrast (30 seconds) — Velen

> "Remember those 7 portal tabs? I'm closing all of them. **[Close the portal tabs dramatically.]** From here on, everything comes from one conversation."

**[Point to the SecPostureIQ chat UI]**

#### 3b. Single Tool — Secure Score (1.5 minutes) — ? types, ? narrates

**Type:** `What is our current secure score?`

**[Wait for response — shows score, category breakdown, 30-day trend, industry comparison]**

> *Ying-Jung:* "The agent just called `query_secure_score` which hits the Graph API endpoint `GET /security/secureScores`. You can see:
> - The **current score** with a percentage
> - **Category breakdown**: Identity, Data, Device, Apps, Infrastructure — each with a current/max score
> - **30-day trend** — all 30 data points, not truncated
> - **Industry comparison** — how this tenant compares to similar organizations
>
> Notice: no PII visible. The tenant ID is redacted. And at the bottom — the AI disclaimer watermark."

#### 3c. Full Assessment (3 minutes) — Velen types, Ying-Jung narrates

**Type:** `Run a full assessment of this tenant's ME5 security posture`

**[Agent chains through all tools — secure score → defender → purview → entra → remediation → scorecard]**

> *Ying-Jung:* "Watch what happens — the agent decides to chain all assessment tools. This is the Copilot SDK runtime making that decision based on the system prompt and tool descriptions. We didn't hardcode the orchestration.
>
> **[As Defender coverage comes in]** Defender for Endpoint shows onboarded device count versus total. Defender for Office shows which policies are active. We're getting real coverage percentages — look, this tenant has gaps in Defender for Identity.
>
> **[As Purview comes in]** DLP policies — some active, some in test mode. Sensitivity labels published. Retention — partial coverage.
>
> **[As Entra comes in]** Conditional Access policies — count, enforcement status. PIM — notice the permissions note: 'PIM data requires admin-level delegated permissions.' The agent is transparently telling you about its access limitations.
>
> **[As remediation plan comes in]** Now GPT-4o synthesizes everything. Five prioritized steps — P0, P1, P2. Each one has: an impact-on-score estimate, an effort level, a **confidence score**, and a **PowerShell script**.
>
> *Velen:* "Look at this P0 step — it says 'Enable Safe Links and Safe Attachments for Defender for Office 365' and provides the exact Exchange Online PowerShell command to do it. That's the killer feature — account teams don't have to figure out the CLI syntax."
>
> **[As scorecard comes in]** *Ying-Jung:* "The adoption scorecard: RED/YELLOW/GREEN per workload. Overall adoption percentage. Top 5 gaps by priority. Estimated days to green. This is the executive summary that goes into the Project 479 tracking."

#### 3d. Foundry Playbook (1 minute) — Velen

**Type:** `Show me the Get to Green playbook for our gaps`

> "The agent calls `get_green_playbook` with the identified gaps as context. It returns the relevant sections of the Project 479 playbook — recommended offers, customer onboarding checklists, and timeline. This is Foundry IQ integration — the agent maps your gaps to Microsoft's actual remediation offers."

#### 3e. Multi-Model Selection (30 seconds) — Velen

**[Click the model dropdown in the header — show GPT-4o and GPT-4o-mini options]**

> "For cost optimization: operators can configure multiple Azure OpenAI deployments. For quick queries, use GPT-4o-mini. For remediation plan generation, GPT-4o. The model is validated server-side against an allowlist — users can't inject arbitrary endpoints."

#### 3f. Quick Flash: What happens with an off-topic question (30 seconds) — Velen

**Type:** `Tell me about the weather`

> "Watch — the agent recognizes this is out of scope and responds with helpful guidance about what it can actually do. It lists its capabilities. No hallucination, no attempt to answer off-topic questions."

---

### ACT 4: OPERATIONAL DEPTH (3 minutes) — Ying-Jung

#### 4a. App Insights Dashboard (1.5 minutes)

**[Switch to browser tab — App Insights]**

> "Here's our observability layer. Every tool call you just saw in the demo generated OpenTelemetry spans.
>
> **[Show the transaction search or end-to-end trace]**
>
> This trace shows the full assessment: 6 tool calls chained, each with duration, Graph API latency, and token usage for the OpenAI call. If an account team reports a slow assessment, your on-call engineer opens this view and immediately sees which tool call took the longest.
>
> **[Show custom metrics if available]**
>
> Custom metrics: assessment duration, content safety blocks, tools called per session."

#### 4b. CI/CD and Tests (1 minute)

**[Switch to terminal or GitHub Actions tab]**

> "Let me show the test suite."

**[Run: `pytest --tb=short -q 2>&1 | tail -10`]**

> "688 tests across 22 test files. 80% coverage gate — build fails if it drops below. Three workflows: `ci-cd.yml` for the main pipeline, `pr-deploy.yml` for PR preview environments, and `rollback.yml` for manual rollback by Git SHA. Trivy scans every Docker image for CRITICAL and HIGH CVEs before push. All with OIDC — no stored secrets."

#### 4c. Infrastructure as Code (30 seconds)

**[Show `infra/main.bicep` briefly]**

> "The entire stack — Container Apps, OpenAI, App Insights, Content Safety, Key Vault, ACR, Managed Identity — all in Bicep. One `azd up` command provisions everything. Parameterized for dev and prod."

---

### ACT 5: SECURITY & RAI DEPTH (2 minutes) — Ying-Jung

> "Let me address the security story because for this agent, **security is the product**.
>
> **[Show PII redaction]** — In App Insights logs, search for any tool call. You'll see the logged parameters have `[TENANT_ID]`, `[USER_EMAIL]` — all PII redacted before it reaches the LLM and before it's logged.
>
> **[Show Content Safety]** — Every LLM input and output goes through Azure AI Content Safety. If a user tries a prompt injection, it's caught at the input gate. If GPT-4o generates something flagged, it's caught at the output gate. Both logged to audit.
>
> **[Show system prompt excerpt]** — The system prompt encodes our security policy: read-only operations only, PII protection, admin consent flagging, mandatory disclaimers, confidence scores on every recommendation. This is the Copilot SDK pattern — policy as prompt, not hard-coded.
>
> **[Show audit log]** — Every action: who asked, what was called, when, redacted I/O, confidence level. RBAC-protected — only security admins can query. 90-day retention.
>
> **Three guarantees we make:**
> 1. The agent **NEVER modifies** a customer's tenant — it's assessment only
> 2. The agent **NEVER sends unredacted PII** to the LLM
> 3. Every action is **immutably audited** with user identity from the Entra ID token"

---

### ACT 6: THE CLOSE (2 minutes) — Velen

> "Let me bring this back to the business impact.
>
> Project 479 is a live Microsoft campaign covering thousands of ME5 accounts. Today, assessing each customer's security posture takes weeks of manual work. SecPostureIQ does it in minutes.
>
> But this isn't just an internal tool. Any M365 E5 customer — any enterprise with E5 licensing — has the same Secure Score, Defender, Purview, and Entra security surface. The agent is **multi-tenant by design**.
>
> What makes this a **Copilot SDK showcase**:
> - We use the SDK's tool registration, session management, and streaming APIs as designed
> - The Agent Runtime handles planning and tool orchestration — we don't hardcode the chain
> - 8 tools with real Graph API integration, not toy demos
> - Multi-model support with BYOM (Bring Your Own Model)
>
> What makes this **enterprise-grade**:
> - 688 tests, 3 CI/CD workflows, Trivy security scanning, PR preview environments
> - Full Bicep IaC — one `azd up` deploys everything
> - Content Safety, PII redaction, audit trail, Managed Identity
> - 9 Azure services integrated, each load-bearing
>
> What makes this **ready to ship**:
> - Verified against a live M365 tenant
> - Traffic simulator and load testing built-in
> - Multi-tenant onboarding with admin consent flows
> - Documented SDK feedback with actionable product insights
>
> **We built the agent that Microsoft account teams need, using the platform that Microsoft is shipping.** Thank you."

*[Pause for applause / transition to Q&A]*

---

## Q&A Battle Cards (Quick Reference)

### If asked "Why Copilot SDK and not just Azure OpenAI function calling?"
> "The SDK gives us the Agent Runtime — planning, context management, multi-model routing, and session lifecycle — for free. We register tools and the runtime decides when and how to call them. If we used raw function calling, we'd have to build the planner, the retry logic, the context compaction, and the streaming coordination ourselves. The SDK is an agent framework, not an API wrapper."

### If asked "What about scale — can this handle 100 concurrent users?"
> "The current architecture is designed for account team use — 5 concurrent assessments. Container Apps can scale to 5 replicas. For higher scale, we'd add a session pooling layer or move to a gateway pattern. But honestly, the use case doesn't need it — there aren't 100 account teams assessing customers simultaneously."

### If asked "How do you handle Graph API rate limiting?"
> "Graph API has per-tenant throttling. Our tools make a small number of read calls per assessment — well within limits. If we hit a 429, the tool returns a retry-after header and the response includes a note. We don't auto-retry aggressively — one assessment, one set of API calls."

### If asked "What if the customer doesn't want their data going to Azure OpenAI?"
> "Only the remediation plan tool calls Azure OpenAI — and it receives PII-redacted assessment data. The 4 assessment tools and the scorecard tool never touch the LLM. A customer could use SecPostureIQ in assessment-only mode (tools 1–4 + scorecard) with zero LLM dependency. The remediation plan is opt-in."

### If asked "How does this compare to Microsoft Secure Score built-in recommendations?"
> "Secure Score gives you recommendations but not prioritized execution plans. It doesn't tell you 'do this first because it gives you 8 points with 2 hours of effort.' And it doesn't give you PowerShell scripts. SecPostureIQ sits on top of Secure Score data and adds the intelligence layer — cross-workload analysis, prioritization, and executable remediation."

### If asked "What was the hardest part of building this?"
> "Two things: (1) The Copilot SDK runtime is a child process — it needs the GitHub CLI binary, which adds complexity to containerization and Docker image size. We documented this in our SDK feedback. (2) GPT-4o hallucinating base64 images for status badges instead of using emoji text. We solved it with explicit output formatting rules in the system prompt. These are real production issues, not theoretical."

### If asked "What would you build next?"
> "Three things: (1) Automated periodic assessments — scheduled runs that push results to Fabric and alert on score regression. (2) Comparative analysis — 'How does this tenant compare to your other ME5 customers?' using anonymized aggregate data from the lakehouse. (3) Executive reporting — auto-generate a PowerPoint deck from the scorecard for the customer's CISO."

### If asked about the team's background
> "Ying-Jung has a PhD in Computer Science from Stanford, where she researched AI agents. She built J-browser-agents, a headless browser automation framework. Velen is a Cloud and AI Platform Solution Architect who's worked across vertical industries and large-scale Azure infrastructure. Between us, we have deep AI agent research and enterprise cloud architecture expertise."

### If asked "Why two interfaces — CLI and Web? Isn't that redundant?"
> "They serve different users. The **Copilot SDK CLI** is for developers and power users who want a terminal experience — it demonstrates the pure Copilot SDK integration with `CopilotClient` and `Tool` registration. The **Web App** is for account teams and customers who want a browser-based experience — it has Entra ID login, session management, and a polished chat UI via Chainlit. Both interfaces share the same 8 tools, system prompt, middleware, and tool schemas — adding a tool to one automatically makes it available to the other. The web app's LLM streaming mode uses Azure OpenAI function calling for intelligent tool selection — so both paths are LLM-powered."

### If asked "Tell me about your CI/CD pipeline depth"
> "Three workflows: `ci-cd.yml` is the main pipeline — lint, test (80% coverage gate), Bicep validate, Docker build, Trivy vulnerability scan (fails on CRITICAL/HIGH CVEs), ACR push, Bicep deploy to Container Apps, post-deploy health check. `pr-deploy.yml` creates ephemeral preview environments for every PR — its own resource group, Container App, preview URL posted as a comment, auto-teardown on PR close. `rollback.yml` is a manual dispatch — enter a Git SHA, it verifies the image exists in ACR, deploys via Bicep, and health-checks. All three use OIDC federation — zero stored secrets."

---

## Timing Targets

| Segment | Duration | Cumulative | Speaker |
|---------|----------|------------|---------|
| ACT 1: The Hook | 2:00 | 2:00 | Velen |
| ACT 1b: The Pain — Portal Walkthrough | 2:00 | 4:00 | Velen |
| ACT 2: Architecture | 2:00 | 6:00 | Ying-Jung |
| ACT 3: Live Demo | 7:00 | 13:00 | Both |
| ACT 4: Ops Depth | 3:00 | 16:00 | Ying-Jung |
| ACT 5: Security & RAI | 2:00 | 18:00 | Ying-Jung |
| ACT 6: The Close | 2:00 | 20:00 | Velen |
| Q&A | ~10:00 | 30:00 | Both |

---

## Demo Failure Contingency Plan

If the live demo fails:

1. **Graph API auth expired**: Switch to mock data mode — say "Let me show you the same assessment with our built-in demo data" and continue. The agent works identically with mocks.
2. **Container Apps down**: Run locally — `python -m src.agent.main` for CLI or `uvicorn src.api.app:app` for web
3. **Azure OpenAI timeout**: Skip remediation plan in demo — show Secure Score, Defender, Purview, Entra, and Scorecard (which don't need LLM). Say "The remediation plan would normally generate here — let me show you a recorded example."
4. **Total failure**: Show the pre-recorded video walkthrough of each demo step. Have screenshots ready.

**Rule: Never apologize for more than 5 seconds. Pivot to the backup immediately.**

---

## Key Numbers to Memorize

| Metric | Number |
|--------|--------|
| Assessment tools | 8 |
| Azure services integrated | 9 (Graph, OpenAI, Content Safety, Entra ID, Key Vault, Container Apps, ACR, App Insights, Fabric) |
| Total tests | 688 (687 unit + 1 integration e2e smoke), 80% coverage gate |
| Test files | 22 (21 unit + 1 integration) |
| CI/CD workflows | 3 (ci-cd, pr-deploy, rollback) |
| CI pipeline stages | 5 (lint → test → bicep-validate → build+trivy → deploy+healthcheck) |
| Bicep modules | 7 (Container App, ACR, OpenAI, Content Safety, Key Vault, App Insights, Availability Test) |
| Operational scripts | 11 (setup, deploy, cleanup, testing) |
| API endpoints | 14 (`/`, `/chat`, `/chat/stream`, `/health`, `/ready`, `/version`, `/auth/login`, `/auth/callback`, `/auth/me`, `/auth/revoke-consent`, `/config`, `/assess`, `/audit/logs`, `/chat-ui`) |
| Chat interfaces | 3 (keyword `/chat`, LLM streaming `/chat/stream`, Chainlit `/chat-ui`) |
| Assessment time | Under 3 minutes (vs 1–2 weeks manual) |
| Graph API permissions | 5 (all read-only) |
| Middleware layers | 4 (Content Safety, PII, Tracing, Audit) |
| Container Apps replicas | 0–5 (scale-to-zero) |
| Dockerfile stages | 2 (builder + runtime), runs as non-root user |
| Docs | 18 files (architecture, setup, SDK feedback, multi-tenant strategy, lessons learned, etc.) |
