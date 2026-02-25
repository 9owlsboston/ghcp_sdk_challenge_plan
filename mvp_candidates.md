# MVP Candidates — GitHub Copilot SDK Enterprise Challenge

Ranked by challenge-scoring potential, factoring in: background (J-browser-agents, security, Stanford AI agents), judging criteria (agentic behavior, tool use, enterprise scenario, security), and feasibility within the timeline.

> **Scoring key** — Each candidate is scored against the base criteria (100 pts) and bonus opportunities (35 pts). Gaps and concrete mitigations are listed to maximize total score.

## Project 479 

It is the codename for Microsoft’s “ME5 Get to Green” security acceleration campaign.
It is a targeted STU (Specialized Technical Unit) renewal and upsell program focused on Microsoft 365 E5 (ME5) customers whose security posture or licensing has drifted “out of green.”

Project 479 (aka. ME5 Get to Green) is a targeted renewal campaign that provides curated offers, technical proofing, expert coaching, and incentives to accelerate ME5 renewals and security adoption.
 [Security Project 479 | SharePoint], [ME5 Get to...gn H2-FY26 | SharePoint]


### 🎯 Core objectives of Project 479

Project 479 is designed to:

Drive M365 E5 (ME5) renewals
Accelerate security adoption (Purview, Defender, Entra, Copilot security scenarios)
Move customers from interest → intent → close
Reduce discount leakage and restore “green” status on ME5 accounts

 [ISD - Proj...9 Playbook | PowerPoint], [ME5 Get to...gn H2-FY26 | SharePoint]

### 💡 Why Project 479 is a powerful MVP anchor

Project 479 is not just context — it's a **strategic differentiator** for the challenge submission. Building an MVP that directly accelerates a live Microsoft sales motion means:

- **Built-in customer validation** (10 bonus pts) — you can demo to actual Project 479 account teams and customers
- **Deep Microsoft integration is natural** (25 pts) — M365 E5 security stack (Defender, Purview, Entra) + Azure services
- **Security IS the use case** (15 pts) — no need to bolt on a security story
- **Storytelling writes itself** (15 pts) — "This agent helps Microsoft close $X in ME5 renewals by accelerating security adoption"
- **Foundry IQ / Work IQ tie-in** (15 bonus pts) — pull Project 479 playbooks, customer profiles, and adoption benchmarks as agent context

See **Candidate #7 (ME5 Security Posture Agent)** and **Candidate #8 (Account Health & Renewal Agent)** below for Project 479-aligned MVPs.

---

## Tier 1 — High Impact, Plays to Your Strengths

### 1. Secure Enterprise Browser Agent

> J-browser-agents + MCP Server + Copilot SDK

| Aspect | Detail |
| --- | --- |
| **What it does** | An agent that can navigate internal web apps (e.g., ServiceNow, Jira, internal dashboards) on behalf of the user — reading, summarizing, and taking actions — with a security boundary layer |
| **Tools** | `navigate_page`, `extract_content`, `fill_form`, `submit_action` — all via J-browser-agents |
| **Enterprise hook** | Employees interact with dozens of internal web portals daily. This agent automates multi-step workflows across them |
| **Security angle** | Auth delegation, URL allowlisting, action approval gates — RSAC background |
| **Why it wins** | Unique (no one else has browser automation + Copilot SDK), deeply agentic (multi-step navigation loops), clear enterprise value, strong security story |
| **Difficulty** | Medium-High |

**Scoring estimate:**

| Criteria | Max | Est. | Notes |
| --- | --- | --- | --- |
| Enterprise value & reusability | 35 | 30 | Strong — automates cross-portal workflows |
| Azure / Microsoft integration | 25 | **8** | ❌ Weakest area — no Azure services in current design |
| Operational readiness | 15 | **5** | ❌ No deployment, CI/CD, or observability story |
| Security, governance & RAI | 15 | 12 | Strong security; RAI (content filtering, PII) missing |
| Storytelling & amplification | 15 | 13 | High — unique demo, memorable |
| **Base total** | **100** | **68** | |
| Bonus: Work/Fabric/Foundry IQ | 15 | 0 | ❌ Not addressed |
| Bonus: Customer validation | 10 | — | TBD |
| Bonus: SDK feedback | 10 | — | TBD |

**Gaps & Mitigations:**

| Gap | Impact | Mitigation |
| --- | --- | --- |
| **No Azure integration** (−17 pts) | Loses most of a 25-pt category | Use **Azure OpenAI** as the LLM backbone. Authenticate users via **Entra ID** with OAuth2 PKCE. Deploy the agent on **Azure Container Apps** (serverless, scales to zero). Store browser session recordings in **Azure Blob Storage** for audit |
| **No operational readiness** (−10 pts) | 15 pts unclaimed | Add a **GitHub Actions** CI/CD pipeline with Bicep IaC. Wire **App Insights** SDK for distributed tracing (every tool call = a span). Add `/health` and `/ready` endpoints |
| **RAI not addressed** (−3 pts) | Partial score in security/RAI category | Route LLM calls through **Azure AI Content Safety** for prompt-injection detection. Add PII redaction on extracted page content before sending to the model. Log all agent decisions to an immutable audit trail |
| **No Foundry/Fabric/Work IQ** (−15 bonus) | Misses largest bonus bucket | Connect the agent to a **Foundry IQ** knowledge base for enterprise SOPs, or log browsing analytics to **Fabric** lakehouse for usage insights |

---

### 2. Incident Response Agent (SRE/SecOps)

> Copilot SDK + Azure Monitor MCP + PagerDuty/ServiceNow tools

| Aspect | Detail |
| --- | --- |
| **What it does** | When an alert fires, the agent autonomously investigates: queries logs, checks pod status, correlates metrics, drafts an incident summary, and creates a ticket |
| **Tools** | `query_logs`, `get_metrics`, `check_pod_health`, `create_incident`, `notify_oncall` |
| **Enterprise hook** | MTTR reduction — every enterprise cares about this |
| **Security angle** | Read-only by default, write actions require approval |
| **Why it wins** | Classic agentic loop (observe → reason → act), concrete ROI story, demo-friendly in 3 min |
| **Difficulty** | Medium |

**Scoring estimate:**

| Criteria | Max | Est. | Notes |
| --- | --- | --- | --- |
| Enterprise value & reusability | 35 | 32 | Universal SRE problem, reusable across industries |
| Azure / Microsoft integration | 25 | **15** | Azure Monitor/Log Analytics mentioned; can go deeper |
| Operational readiness | 15 | **5** | ❌ Not addressed in current design |
| Security, governance & RAI | 15 | 10 | Read-only default is good; RAI & governance thin |
| Storytelling & amplification | 15 | 13 | Clean demo: alert → investigation → resolution |
| **Base total** | **100** | **75** | |
| Bonus: Work/Fabric/Foundry IQ | 15 | 0 | ❌ Not addressed |
| Bonus: Customer validation | 10 | — | TBD |
| Bonus: SDK feedback | 10 | — | TBD |

**Gaps & Mitigations:**

| Gap | Impact | Mitigation |
| --- | --- | --- |
| **Azure integration is shallow** (−10 pts) | Only mentions Azure Monitor generically | Add **Azure OpenAI** for reasoning + summarization. Use **Log Analytics** KQL queries as a core tool. Pull from **App Insights** for APM correlation. Authenticate via **Entra ID** managed identity. Deploy on **Azure Container Apps** |
| **No operational readiness** (−10 pts) | 15 pts unclaimed | GitHub Actions pipeline deploying Bicep templates. App Insights tracing on every agent step. Structured JSON logging. Health probes for the agent service |
| **RAI & governance gaps** (−5 pts) | Partial score | Add Azure AI Content Safety on LLM outputs (incident summaries could contain sensitive data). Implement a **human-approval gate** for remediation actions (restart pod, scale service). Audit log every decision with reasoning chain |
| **No Foundry/Fabric/Work IQ** (−15 bonus) | Misses largest bonus | Push incident telemetry to a **Fabric** lakehouse for trend analysis dashboards. Or use **Foundry IQ** to pull runbooks as agent context |

---

## Tier 2 — Solid Enterprise Scenarios

### 3. PR Security Review Agent

> Copilot SDK + GitHub API tools + security rule MCP

| Aspect | Detail |
| --- | --- |
| **What it does** | Automatically reviews PRs for security issues: scans diff for secrets, vulnerable patterns, dependency risks. Posts findings as PR comments, can block merge |
| **Tools** | `get_pr_diff`, `scan_for_secrets`, `check_dependencies`, `post_review_comment`, `update_pr_status` |
| **Enterprise hook** | Shift-left security — automated guardrails in the SDLC |
| **Why it wins** | Directly GitHub-native, strong security narrative, multi-tool orchestration |
| **Difficulty** | Medium |

**Scoring estimate:**

| Criteria | Max | Est. | Notes |
| --- | --- | --- | --- |
| Enterprise value & reusability | 35 | 28 | Good — shift-left security, but narrower audience (dev teams only) |
| Azure / Microsoft integration | 25 | **5** | ❌ No Azure services used |
| Operational readiness | 15 | **5** | ❌ Not addressed |
| Security, governance & RAI | 15 | 13 | Core purpose is security — strong |
| Storytelling & amplification | 15 | 10 | Solid but not as flashy as #1 or #2 |
| **Base total** | **100** | **61** | |
| Bonus: Work/Fabric/Foundry IQ | 15 | 0 | ❌ Not addressed |
| Bonus: Customer validation | 10 | — | TBD |
| Bonus: SDK feedback | 10 | — | TBD |

**Gaps & Mitigations:**

| Gap | Impact | Mitigation |
| --- | --- | --- |
| **No Azure integration** (−20 pts) | Biggest weakness — loses most of 25-pt category | Use **Azure OpenAI** for LLM-based code analysis. Store scan results in **Azure Cosmos DB** for historical tracking. Use **Azure Key Vault** for managing scan rule secrets. Deploy the webhook receiver on **Azure Functions** |
| **No operational readiness** (−10 pts) | 15 pts unclaimed | Deploy as an Azure Function triggered by GitHub webhooks. GitHub Actions for CI/CD. App Insights for tracing scan durations and false-positive rates |
| **RAI gaps** (−2 pts) | Minor | Add content filtering on generated review comments to ensure professional, non-biased language. Log all findings with confidence scores |
| **No Foundry/Fabric/Work IQ** (−15 bonus) | Misses bonus | Feed scan results into **Fabric** for org-wide security posture dashboards. Use **Foundry IQ** to pull security policy documents as context for the agent |

---

### 4. Knowledge Base Q&A Agent with Action Execution

> Copilot SDK + internal docs MCP + ticketing tools

| Aspect | Detail |
| --- | --- |
| **What it does** | An IT helpdesk agent that searches internal knowledge bases to answer questions, and can execute remediation actions (reset passwords, provision access, create tickets) |
| **Tools** | `search_kb`, `reset_password`, `provision_access`, `create_ticket`, `escalate_to_human` |
| **Enterprise hook** | IT ticket deflection — measurable cost savings |
| **Why it wins** | Read → reason → act pattern, human-in-the-loop for sensitive actions |
| **Difficulty** | Low-Medium |

**Scoring estimate:**

| Criteria | Max | Est. | Notes |
| --- | --- | --- | --- |
| Enterprise value & reusability | 35 | 30 | IT helpdesk automation — broad applicability |
| Azure / Microsoft integration | 25 | **5** | ❌ No Azure services mentioned |
| Operational readiness | 15 | **5** | ❌ Not addressed |
| Security, governance & RAI | 15 | 10 | Human-in-the-loop is good; needs more |
| Storytelling & amplification | 15 | 10 | Relatable but common — others may build similar |
| **Base total** | **100** | **60** | |
| Bonus: Work/Fabric/Foundry IQ | 15 | 0 | ❌ Not addressed |
| Bonus: Customer validation | 10 | — | TBD |
| Bonus: SDK feedback | 10 | — | TBD |

**Gaps & Mitigations:**

| Gap | Impact | Mitigation |
| --- | --- | --- |
| **No Azure integration** (−20 pts) | Largest point loss | Use **Azure AI Search** (vector + hybrid) for the knowledge base. Use **Azure OpenAI** for embeddings + chat. Store docs in **Azure Blob Storage**. Authenticate via **Entra ID**. Deploy on **Azure App Service** or **Container Apps** |
| **No operational readiness** (−10 pts) | 15 pts unclaimed | GitHub Actions CI/CD. Bicep IaC. App Insights for tracking query latency, answer accuracy, and ticket deflection rate |
| **Security/RAI thin** (−5 pts) | Partial score | PII redaction on KB search results. Azure AI Content Safety on generated answers. RBAC on action execution (only authorized users can reset passwords). Audit log all actions with user identity |
| **No Foundry/Fabric/Work IQ** (−15 bonus) | Misses bonus | Use **Work IQ** to pull employee context (role, team, recent tickets) for personalized answers. Or connect to **Foundry IQ** as the knowledge retrieval layer |
| **Differentiation risk** | Judges may see similar submissions | Add a unique twist — e.g., multi-language support, or proactive suggestions based on trending ticket categories |

---

## Tier 3 — Quick Wins / Simpler MVPs

### 5. Repo Onboarding Agent

> Copilot SDK + GitHub API tools

| Aspect | Detail |
| --- | --- |
| **What it does** | Helps new engineers understand a codebase: explains architecture, finds relevant files, summarizes READMEs, identifies key patterns, generates onboarding guides |
| **Tools** | `read_repo_structure`, `search_code`, `get_readme`, `analyze_dependencies` |
| **Enterprise hook** | Developer productivity — faster ramp-up for new hires |
| **Difficulty** | Low |

**Scoring estimate:**

| Criteria | Max | Est. | Notes |
| --- | --- | --- | --- |
| Enterprise value & reusability | 35 | 22 | Useful but niche — onboarding happens infrequently per person |
| Azure / Microsoft integration | 25 | **3** | ❌ No Azure services at all |
| Operational readiness | 15 | **5** | ❌ Not addressed |
| Security, governance & RAI | 15 | 5 | ❌ Weakest security story of all candidates |
| Storytelling & amplification | 15 | 8 | Useful but not dramatic — hard to make a 3-min demo exciting |
| **Base total** | **100** | **43** | |
| Bonus: Work/Fabric/Foundry IQ | 15 | 0 | ❌ Not addressed |
| Bonus: Customer validation | 10 | — | TBD |
| Bonus: SDK feedback | 10 | — | TBD |

**Gaps & Mitigations:**

| Gap | Impact | Mitigation |
| --- | --- | --- |
| **No Azure integration** (−22 pts) | Near-zero in a 25-pt category | Use **Azure OpenAI** for code understanding. Use **Azure AI Search** to index repo content for semantic code search. Deploy on **Azure Container Apps** |
| **Weak security & RAI story** (−10 pts) | Below average for challenge | Add **Entra ID** auth. Ensure the agent cannot exfiltrate code outside the org boundary. Add content filtering on generated guides (avoid leaking secrets found in code) |
| **No operational readiness** (−10 pts) | 15 pts unclaimed | Standard ops layer (see cross-cutting section below) |
| **No Foundry/Fabric/Work IQ** (−15 bonus) | Misses bonus | Use **Work IQ** to identify the new hire's role/team and tailor the onboarding guide accordingly |
| **Weak enterprise value** (−13 pts) | Hard to show broad ROI | Reframe as "developer productivity platform" — not just onboarding but ongoing codebase exploration. Add metrics tracking (time-to-first-commit) |

---

### 6. Compliance Audit Agent

> Copilot SDK + Azure Policy MCP + GitHub repo tools

| Aspect | Detail |
| --- | --- |
| **What it does** | Scans repos and cloud infra against compliance rules (SOC2, HIPAA, etc.), generates findings, creates remediation PRs |
| **Tools** | `scan_iac_templates`, `check_policy_compliance`, `generate_remediation_pr`, `create_audit_report` |
| **Enterprise hook** | Audit readiness — regulated industries love this |
| **Difficulty** | Medium |

**Scoring estimate:**

| Criteria | Max | Est. | Notes |
| --- | --- | --- | --- |
| Enterprise value & reusability | 35 | 30 | High — every regulated enterprise needs this |
| Azure / Microsoft integration | 25 | **12** | Azure Policy mentioned; can go deeper |
| Operational readiness | 15 | **5** | ❌ Not addressed |
| Security, governance & RAI | 15 | 12 | Compliance is inherently about governance — strong angle |
| Storytelling & amplification | 15 | 10 | Good for regulated verticals; demo needs to be punchy |
| **Base total** | **100** | **69** | |
| Bonus: Work/Fabric/Foundry IQ | 15 | 0 | ❌ Not addressed |
| Bonus: Customer validation | 10 | — | TBD |
| Bonus: SDK feedback | 10 | — | TBD |

**Gaps & Mitigations:**

| Gap | Impact | Mitigation |
| --- | --- | --- |
| **Azure integration is shallow** (−13 pts) | Only Azure Policy mentioned | Add **Azure OpenAI** for reasoning over compliance rules. Use **Azure Resource Graph** for querying cloud resource state. Store findings in **Azure Cosmos DB**. Use **Entra ID** for RBAC on audit reports. Deploy on **Azure Container Apps** |
| **No operational readiness** (−10 pts) | 15 pts unclaimed | Standard ops layer (see cross-cutting section below) |
| **RAI gaps** (−3 pts) | Minor but present | Ensure generated remediation PRs don't introduce new issues — add validation step. Content-filter audit reports for sensitive data before sharing |
| **No Foundry/Fabric/Work IQ** (−15 bonus) | Misses bonus | Push compliance findings to **Fabric** lakehouse for trend dashboards (compliance posture over time). Use **Foundry IQ** to pull latest regulatory guidance as agent context |

---

## Tier 1b — Project 479-Aligned (High Impact + Built-in Sales Motion)

### 7. ME5 Security Posture Assessment Agent

> Copilot SDK + Microsoft Graph Security API + M365 Defender MCP + Azure OpenAI

| Aspect | Detail |
| --- | --- |
| **What it does** | Assesses a customer's M365 E5 security deployment status across the full ME5 stack (Defender XDR, Purview, Entra ID P2), identifies adoption gaps that put the account "out of green," generates prioritized remediation runbooks with configuration scripts, and tracks progress toward green status |
| **Tools** | `query_secure_score`, `assess_defender_coverage`, `check_purview_policies`, `get_entra_config`, `generate_remediation_plan`, `create_adoption_scorecard` |
| **Enterprise hook** | Directly accelerates the Project 479 sales motion — every ME5 account team needs this. Reduces time-to-green from weeks to hours |
| **Security angle** | Security IS the product — the entire purpose is security posture improvement. Auth via Entra ID with delegated permissions, least-privilege Graph API scopes |
| **Why it wins** | Unique combination: solves a real, active Microsoft sales problem with thousands of target accounts. Judges see an agent that generates revenue, not just saves time. No other submission will be anchored to a live sales campaign |
| **Difficulty** | Medium |

**Scoring estimate:**

| Criteria | Max | Est. | Notes |
| --- | --- | --- | --- |
| Enterprise value & reusability | 35 | 33 | Live sales motion with thousands of accounts, reusable across all ME5 customers |
| Azure / Microsoft integration | 25 | 22 | Native — M365 Defender, Purview, Entra ID, Microsoft Graph, Azure OpenAI, Azure Monitor |
| Operational readiness | 15 | **5** | ❌ Not addressed in current design (standard ops layer fixes this) |
| Security, governance & RAI | 15 | 14 | Security is the core purpose; needs minor RAI additions |
| Storytelling & amplification | 15 | 14 | "This agent helps close ME5 renewals" — judges love revenue impact |
| **Base total** | **100** | **88** | Highest raw base score of all candidates |
| Bonus: Work/Fabric/Foundry IQ | 15 | 12 | Pull Project 479 playbooks from Foundry IQ; push adoption telemetry to Fabric |
| Bonus: Customer validation | 10 | 8 | Can demo to actual Project 479 account teams for written endorsement |
| Bonus: SDK feedback | 10 | — | TBD |

**Gaps & Mitigations:**

| Gap | Impact | Mitigation |
| --- | --- | --- |
| **Ops layer missing** (−10 pts) | 15 pts unclaimed | Apply standard ops layer: GitHub Actions CI/CD, Bicep IaC, App Insights tracing, health probes. Deploy on **Azure Container Apps** |
| **RAI not explicitly addressed** (−1 pt) | Minor | Route LLM outputs through **Azure AI Content Safety**. Redact customer-sensitive data (tenant IDs, user lists) before model processing. Add confidence scores to remediation recommendations |
| **Graph API permissions complexity** | Implementation risk | Use **delegated permissions** with minimal scopes. Document required admin consent. Provide a setup script for permission grants |
| **Foundry/Fabric integration** (−3 bonus pts) | Partial bonus capture | Push security posture snapshots to a **Fabric** lakehouse for longitudinal dashboards. Pull Project 479 playbooks and offer catalogs from **Foundry IQ** as agent context |

---

### 8. Account Health & Renewal Intelligence Agent

> Copilot SDK + Microsoft Graph + Licensing APIs + CRM MCP + Azure OpenAI

| Aspect | Detail |
| --- | --- |
| **What it does** | A field-enablement agent for account teams running the Project 479 motion. Pulls customer licensing, usage telemetry, and security adoption data, then generates renewal readiness scorecards, identifies churn risk signals, recommends specific Project 479 offers, and produces customer-ready talking points |
| **Tools** | `get_license_usage`, `assess_adoption_health`, `detect_churn_signals`, `recommend_offers`, `generate_talking_points`, `create_renewal_scorecard` |
| **Enterprise hook** | Enables thousands of account teams to execute the Project 479 playbook faster and more consistently. Directly tied to renewal revenue |
| **Security angle** | Customer data isolation (multi-tenant), RBAC on sensitive licensing data, audit trail of all recommendations generated |
| **Why it wins** | Field-enablement tools are judged favorably because they scale impact multiplicatively — one agent enables hundreds of sellers |
| **Difficulty** | Medium |

**Scoring estimate:**

| Criteria | Max | Est. | Notes |
| --- | --- | --- | --- |
| Enterprise value & reusability | 35 | 30 | High — reusable for any renewal campaign, not just Project 479 |
| Azure / Microsoft integration | 25 | 18 | Microsoft Graph, Azure OpenAI, Entra ID, Azure Cosmos DB for scorecard persistence |
| Operational readiness | 15 | **5** | ❌ Not addressed in current design |
| Security, governance & RAI | 15 | 11 | Multi-tenant data isolation good; needs RAI layer on generated recommendations |
| Storytelling & amplification | 15 | 13 | "This agent helps our sellers close renewals" — strong internal amplification story |
| **Base total** | **100** | **77** | |
| Bonus: Work/Fabric/Foundry IQ | 15 | 10 | Use **Work IQ** for account team context; push deal analytics to **Fabric** |
| Bonus: Customer validation | 10 | 6 | Demo to an account team running Project 479 |
| Bonus: SDK feedback | 10 | — | TBD |

**Gaps & Mitigations:**

| Gap | Impact | Mitigation |
| --- | --- | --- |
| **Ops layer missing** (−10 pts) | 15 pts unclaimed | Standard ops layer: CI/CD, Bicep, App Insights, Container Apps |
| **Azure integration could be deeper** (−7 pts) | Loses points in 25-pt category | Add **Azure AI Search** for semantic search over historical deal notes. Use **Azure Key Vault** for CRM credentials. Store scorecards in **Azure Cosmos DB**. Add **Azure Monitor** alerts for churn-risk thresholds |
| **RAI gaps** (−4 pts) | Moderate | Content-filter generated talking points to avoid aggressive or misleading sales language. Add disclaimer watermarks on AI-generated scorecards. Log all recommendations with reasoning chains for auditability |
| **Customer data sensitivity** | Implementation risk | Implement strict tenant isolation. Use **Entra ID** managed identities for service-to-service auth. Encrypt scorecards at rest with customer-managed keys. Add data retention policies |
| **Foundry/Fabric/Work IQ** (−5 bonus pts) | Partial bonus capture | Use **Work IQ** to pull account team structure and seller context. Push deal velocity and adoption metrics to **Fabric** for executive dashboards. Pull Project 479 offer catalog from **Foundry IQ** |

---

## Cross-Cutting: Standard Ops Layer (applies to all candidates)

Every candidate should include these to capture the **15 operational readiness points**:

| Component | Implementation |
| --- | --- |
| **CI/CD** | GitHub Actions workflow: lint → test → build → deploy. Triggered on push to `main` |
| **IaC** | Bicep templates (or Terraform) for all Azure resources. Checked into the repo |
| **Observability** | Azure App Insights SDK integrated. Every tool call = a distributed trace span. Structured JSON logging |
| **Health probes** | `/health` and `/ready` endpoints for container orchestration |
| **Deployment target** | Azure Container Apps (recommended — serverless, scales to zero, easy to demo) |
| **RAI baseline** | Azure AI Content Safety on all LLM inputs/outputs. PII detection/redaction. Prompt-injection guardrails |
| **Auth** | Entra ID (for user auth) + Managed Identity (for service-to-service) |

---

## Recommendation (Revised) (Revised)

### Score comparison (with mitigations applied)

| Rank | MVP | Base (current) | Base (with mitigations) | Bonus potential | Total ceiling | Risk |
| --- | --- | --- | --- | --- | --- | --- |
| **1st** | **ME5 Security Posture Agent** (#7) 🆕 | 88 | ~98 | +30 | **128** | Low — native Microsoft stack, built-in customer validation via Project 479 |
| **2nd** | **Incident Response Agent** (#2) | 75 | ~92 | +25 | **117** | Low — easiest Azure surface, straightforward demo |
| **3rd** | **Secure Browser Agent** (#1) | 68 | ~88 | +25 | **113** | Medium — browser automation adds complexity |
| **4th** | **Compliance Audit Agent** (#6) | 69 | ~87 | +25 | **112** | Medium — needs punchy demo for storytelling |
| 5th | Account Health & Renewal Agent (#8) 🆕 | 77 | ~90 | +26 | 116 | Medium — CRM integration complexity, less technical demo |
| 6th | PR Security Review (#3) | 61 | ~82 | +25 | 107 | Low — fast to build but less differentiated |
| 7th | KB Q&A Agent (#4) | 60 | ~80 | +25 | 105 | Medium — common idea, differentiation risk |
| 8th | Repo Onboarding (#5) | 43 | ~68 | +20 | 88 | High — weak enterprise value, hard to score well |

### Strategic recommendation

**Primary pick: ME5 Security Posture Agent (#7)** — This is the new top candidate after factoring in the Project 479 sales motion. It has the highest raw base score (88) of any candidate because security is inherent rather than bolted on, Microsoft/Azure integration is native (Graph Security API, M365 Defender, Purview, Entra, Azure OpenAI), and customer validation is built-in — you can demo to actual Project 479 accounts for a written endorsement. The total ceiling of ~128 pts is the highest of all candidates. The narrative is uniquely powerful: "This agent directly accelerates a live Microsoft renewal campaign affecting thousands of ME5 accounts."

**Strong alternative: Incident Response Agent (#2)** — Still a top contender with the lowest execution risk. If the Project 479 data access (Graph Security API, Secure Score) proves difficult to scope within the timeline, this is the safest fallback with a clean demo and universal appeal.

**Dark horse: Secure Browser Agent (#1)** — Still the most *memorable* project and the one judges won't have seen before. If you have bandwidth to add the Azure integration layer (OpenAI, Entra ID, Container Apps, Content Safety), it scores nearly as high and has a stronger "wow factor." The risk is that browser automation adds implementation complexity that could eat into time for the ops/Azure integration work that actually scores points.

**Combo strategy: #7 + #2** — The ME5 Security Posture Agent and the Incident Response Agent share significant infrastructure overlap (Azure OpenAI, Entra ID, App Insights, Container Apps, Fabric). You could build a shared agent platform and implement both as "skills," creating a unified security operations agent that covers both proactive posture assessment (Project 479) and reactive incident handling. This is higher effort but the storytelling is exceptional.

**Recommended strategy:** Pick one and apply *all* mitigations from the gap analysis above, plus the cross-cutting ops layer. A fully polished single project scores far higher than a partially complete more ambitious one. The mitigations are where the points are — not in the idea itself.

### Quick-win bonus points

| Bonus | Points | Effort | How |
| --- | --- | --- | --- |
| SDK product feedback | 10 | Low | Keep a running log of SDK pain points, API gaps, and docs issues. Submit as a separate doc |
| Customer validation | 10 | Medium | Demo to a customer contact and get a written endorsement |
| Foundry/Fabric/Work IQ | 15 | Medium | Even a lightweight integration counts — e.g., push telemetry to Fabric, pull SOPs from Foundry |
