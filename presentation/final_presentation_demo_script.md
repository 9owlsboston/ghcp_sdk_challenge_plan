# SecPostureIQ — 20-Minute Final Presentation Script

> **Team:** Ying-Jung Chen (yjcmsft) + Velen Liang (9owlsboston)
> **Total time:** 20 minutes presentation + demo, then Q&A
> **Narrative arc:** Hero's Journey (The Call → The Ordeal → The Transformation → The Return)
> **Framing:** Golden Circle (Why → How → What)

---

## Pre-Presentation Checklist

- [ ] **Portal tabs pre-loaded (for "The Ordeal" demo):**
  - [ ] Tab 1: Microsoft 365 Security — Secure Score (`security.microsoft.com/securescore`)
  - [ ] Tab 2: Microsoft 365 Defender — Device inventory (`security.microsoft.com/endpoints/device-inventory`)
  - [ ] Tab 3: Microsoft 365 Defender — Threat policies (`security.microsoft.com/threatpolicy`)
  - [ ] Tab 4: Microsoft Purview — DLP Policies (`compliance.microsoft.com/datalossprevention`)
  - [ ] Tab 5: Microsoft Purview — Sensitivity Labels (`compliance.microsoft.com/informationprotection`)
  - [ ] Tab 6: Microsoft Entra — Conditional Access (`entra.microsoft.com/#view/Microsoft_AAD_ConditionalAccess`)
  - [ ] Tab 7: Microsoft Entra — PIM (`entra.microsoft.com/#view/Microsoft_Azure_PIMCommon`)
- [ ] Web chat UI running (`http://localhost:8000`)
- [ ] Real M365 tenant connected (or mock data — know which mode)
- [ ] App Insights dashboard open (pre-loaded with traffic data)
- [ ] GitHub repo at README.md in a tab
- [ ] Architecture diagram slide ready
- [ ] Terminal window ready for test results
- [ ] **Backup**: screenshots/video of every demo step for live-demo failure

---

## Presentation Flow (20 minutes)

---

### ACT 1: THE CALL TO ADVENTURE (2 min) — Velen / YJ

*Narrative beat: The hero (account team) receives a mission they can't refuse — but the world they're entering is hostile.*

**[Slide: Single number on black background — "$4.44 million"]**

*[Hold silence for 3 seconds. Let them read the number.]*

> "Four point four four million dollars. That's the global average cost of a data breach in 2025 — IBM's latest report. In the US, it's ten million.
>
> Now — imagine you're a Microsoft account manager. You just got your Project 479 assignment. One of your ME5 customers — a Fortune 500 company — bought E5 security two years ago. Premium licensing. Defender. Purview. Entra. The full stack.
>
> But nobody checked whether they actually *turned it on*.
>
> Internal telemetry shows over **400,000 tenants** in the E5 family. Even conservatively, **tens of thousands** are on the full M365 E5 suite. Each one has the same security surface. Each one needs a posture assessment.
>
> Your job: assess their security posture, find the gaps, and get them to green before renewal. You have dozens of accounts. Renewal deadline is approaching.
>
> **Where do you even start?**"

*[Pause. Don't answer yet. Let the question hang.]*

---

### ACT 2: THE ORDEAL — The Manual World (2 min) — Velen / YJ

*Narrative beat: The hero enters the "underworld" — a maze of portals and spreadsheets. The audience must **feel** the pain, not just hear about it.*

> "Let me show you."

**[Browser pre-loaded with 7 tabs. Click through briskly — 15 seconds per portal group. The point is the *overwhelming volume*, not reading every detail.]**

**[Tab 1 — Secure Score]**

> "Portal one. Secure Score. A number, five categories, dozens of recommendations. Which ones actually matter for this customer's licensing? Twenty minutes — minimum — just to parse this page."

**[Tabs 2–3 — Defender]**

> "Portal two. Defender. Device inventory — are endpoints onboarded? Safe Links, Safe Attachments — different sub-page. Defender for Identity — another section. Cloud Apps — another page. Four workloads, four navigation paths, zero unified view."

**[Tabs 4–5 — Purview]**

> "Portal three. Purview. DLP policies — active or test? Sensitivity labels — published? Auto-labeling? Retention? Each one a separate click, separate table."

**[Tabs 6–7 — Entra]**

> "Portal four. Entra. Conditional Access — how many policies? Report-only or enforced? PIM — role assignments. Identity Protection. Access Reviews. Each a separate blade."

**[Gesture at all 7 tabs. Pause.]**

> "**Four portals. Twelve sub-pages. One hour just to collect raw data — before writing a single recommendation.** Then you compile it into a Word document. Manually. For every customer.
>
> That's 1 to 2 weeks per assessment. And Project 479 covers **thousands** of accounts."

**[Close laptop lid on the tabs — or minimize them all in one gesture. Clean break.]**

> "That's the world before. Now let me show you the world after."

---

### ACT 3: THE MENTOR — Why We Built This (1 min) — Velen / YJ

*Narrative beat: Golden Circle "WHY" — the deeper purpose, not the features. The audience should nod in recognition.*

> "We didn't build SecPostureIQ because we wanted to build a cool agent.
>
> We built it because **the security tools our customers bought aren't protecting them if nobody configures them**. The gap isn't the product — the gap is the *assessment*. The human bottleneck between 'licensed' and 'protected.'
>
> Project 479 exists to close that gap. SecPostureIQ is the weapon that makes it possible at scale.
>
> **One conversation. Under three minutes. Live data. Executable remediation plans.**
>
> Let me show you."

---

### ACT 4: THE TRANSFORMATION — Live Demo (7 min) — Velen / YJ

*Narrative beat: The hero wields a new weapon and everything changes. This is the "wow" moment — the demo must feel effortless compared to Act 2.*

**[Switch to browser — SecPostureIQ web chat UI. Clean screen. No clutter.]**

#### 4a. The Contrast (20 sec) — Velen / YJ

> "Remember those 7 portal tabs? Gone. From here — one conversation."

#### 4b. Single Question (1.5 min) — Velen / YJ

**Type:** `What is our current secure score?`

**[Wait for response — score, categories, 30-day trend, industry benchmark]**

> *[Narrator]:* "One sentence. The agent called `query_secure_score` — live data from the Graph Security API. Current score with percentage. Category breakdown — Identity, Data, Device, Apps, Infrastructure. Thirty-day trend. Industry comparison.
>
> Notice: tenant ID is redacted. AI disclaimer at the bottom. That took about five seconds. The manual version? Twenty minutes."

#### 4c. Full Assessment — The Whole Story (3 min) — Velen / YJ

**Type:** `Run a full assessment of this tenant's ME5 security posture`

**[Agent chains all tools — the audience watches the assessment unfold in real-time]**

> *[Narrator]:* "Now watch. The agent decides to chain all assessment tools — Secure Score, Defender, Purview, Entra, remediation, scorecard. **We didn't hardcode this sequence.** The Copilot SDK runtime reads the system prompt, reads the tool descriptions, and plans the chain autonomously.
>
> **[As Defender results stream in]** Defender for Endpoint — onboarded devices versus total. Defender for Office — active policies. Look — this tenant has gaps in Defender for Identity. Real data, real gaps, found in seconds.
>
> **[As Purview streams in]** DLP policies — some active, some test-only. Sensitivity labels — published but auto-labeling is off.
>
> **[As Entra streams in]** Conditional Access — count and enforcement status. PIM — and notice: the agent says 'PIM data requires admin-level permissions.' It tells you transparently what it *can't* see. No hallucination, no guessing.
>
> **[As remediation plan appears]** Here's where it gets real. GPT-4o synthesizes everything into five prioritized steps — P0, P1, P2. Each step has: impact-on-score estimate, effort level, a confidence score — and a **PowerShell script you can copy-paste and run**."
>
> *[Presenter]:* "This is the killer feature. P0 says 'Enable Safe Links for Defender for Office 365' and gives you the exact Exchange Online command. Your account team doesn't need to be a security expert. They hand this to the customer's IT admin and say: *run this*."
>
> **[As scorecard appears]** *[Narrator]:* "The adoption scorecard — RED, YELLOW, GREEN per workload. Top 5 gaps ranked. Estimated days to green. **This is the one-page executive summary** for the Project 479 tracker."

#### 4d. Playbook Integration (45 sec) — Velen / YJ

**Type:** `Show me the Get to Green playbook for our gaps`

> "The agent maps the gaps it just found to Microsoft's actual remediation playbooks via Foundry IQ. Recommended offers, customer onboarding checklists, timeline. This isn't a generic recommendation — it's **personalized to exactly what this tenant is missing**."

#### 4e. Guardrails (30 sec) — Velen / YJ

**Type:** `Tell me about the weather`

> "Off-topic. Watch — the agent stays in its lane. Lists what it *can* do. No hallucination, no attempt to please. Because for a security tool, making things up isn't charming — it's dangerous."

#### 4f. Model Flexibility (15 sec) — Velen / YJ

**[Click model dropdown — show GPT-4o and GPT-4o-mini options]**

> "Operators configure models per use case. Quick queries: GPT-4o-mini. Remediation plans: GPT-4o. Server-validated against an allowlist."

---

### ACT 5: THE ROAD OF TRIALS — How We Built It (2 min) — Velen / YJ

*Narrative beat: Golden Circle "HOW" — the architecture and engineering rigor. Keep it visual — point at the diagram, don't recite specs.*

**[Slide: Architecture Diagram]**

> "How does this work?
>
> **[Point to SDK layer]** GitHub Copilot SDK — the thin client. It registers 8 tools. The Agent Runtime — Copilot's own execution engine — handles planning, orchestration, and context management. We don't build the planner. We declare intent and the runtime figures out tool sequencing.
>
> **[Point to tools]** 8 tools. Four assessment tools hit the Microsoft Graph Security API — one tool per portal you saw earlier. One remediation tool backed by Azure OpenAI GPT-4o. One scorecard. One Foundry IQ playbook. One Fabric lakehouse for longitudinal trends.
>
> **[Point to middleware]** Every request passes through four layers: Content Safety screening, PII redaction, distributed tracing, and an immutable audit log. This isn't security bolted on — it's security built in.
>
> **[Point to Azure services]** Nine Azure services — Container Apps, OpenAI, Content Safety, Entra ID, Key Vault, ACR, App Insights, Fabric, Graph API. Every one load-bearing. One `azd up` deploys the entire stack from Bicep IaC.
>
> Two interfaces share the same tooling layer — Copilot SDK CLI for developers, web chat for account teams. Same tools, same system prompt, same middleware."

---

### ACT 6: THE ORDEAL OVERCOME — Ops, Security & Trust (3 min) — Velen / YJ

*Narrative beat: The hero proves the weapon works under fire — observability, testing, and security guarantees.*

#### 6a. Observability (1 min)

**[Switch to App Insights dashboard]**

> "Every tool call generates OpenTelemetry spans. Here's the trace from the assessment you just watched — six tool calls, each with duration, Graph API latency, token usage. If something is slow, you see exactly where in seconds.
>
> Custom metrics: assessment duration, content safety blocks, tools per session. This is production-grade observability, not print statements."

#### 6b. Test Suite & CI/CD (1 min)

**[Switch to terminal]**

**[Run: `pytest --tb=short -q 2>&1 | tail -10`]**

> "688 tests. 22 test files. 80% coverage gate — the build fails if it drops. Three CI/CD workflows: main pipeline, PR preview environments, and a manual rollback by Git SHA. Every Docker image is scanned by Trivy for critical vulnerabilities. All authentication uses OIDC federation — **zero stored secrets** in the entire pipeline."

#### 6c. Security Guarantees (1 min)

> "This agent assesses security posture. If the agent itself isn't secure, we have no credibility. So we make **three guarantees**:
>
> **One** — the agent NEVER modifies a customer's tenant. Read-only. Assessment only.
>
> **Two** — the agent NEVER sends unredacted PII to the LLM. Tenant IDs, emails — redacted before the model sees them, redacted before they're logged.
>
> **Three** — every action is immutably audited. Who asked, what was called, when, redacted I/O, confidence level. RBAC-protected. 90-day retention.
>
> Content Safety screens every input and output. Prompt injection attempts are caught at the gate. The system prompt enforces read-only policy, mandatory disclaimers, and confidence scores on every recommendation. **For this agent, security isn't a feature — it's the entire point.**"

---

### ACT 7: THE RETURN — Business Impact (2 min) — Velen / YJ

*Narrative beat: The hero returns with the elixir — transformation is complete. Zoom out from the demo to the business case. End with a sentence they'll repeat in the hallway.*

> "Let me bring this home.
>
> **The problem**: Project 479 covers thousands of ME5 accounts. Manual posture assessment takes 1–2 weeks per customer.
>
> **The math**: At 40 hours per manual assessment, even 1,000 accounts is **40,000 man-hours**. Internal telemetry shows over 400,000 E5-category tenants — tens of thousands on full M365 E5. The manual approach doesn't scale. It never could.
>
> **The transformation**: SecPostureIQ does it in under 3 minutes. One conversation. Personalized remediation with executable scripts. A scorecard ready for the executive report.
>
> But this isn't just an internal tool. Every M365 E5 customer on the planet has the same security surface — Secure Score, Defender, Purview, Entra. The agent is **multi-tenant by design**. One admin consent click and it works for any enterprise.
>
> **[Slide: Three pillars side by side]**
>
> What makes this a **Copilot SDK showcase**:
> - 8 tools with live Graph API integration — not toy demos
> - Agent Runtime plans and orchestrates — we don't hardcode the chain
> - Multi-model support, Bring Your Own Model ready
>
> What makes this **enterprise-grade**:
> - 688 tests, 3 CI/CD pipelines, Trivy scanning, zero stored secrets
> - Full Bicep IaC — one command deploys 9 Azure services
> - Content Safety, PII redaction, audit trail baked in
>
> What makes this **ready to ship**:
> - Verified against a live M365 tenant
> - Multi-tenant onboarding with admin consent flows
> - SDK product feedback with actionable insights documented
>
> **[Final slide: One sentence, large type]**
>
> *'We turned a two-week assessment into a three-minute conversation — and made it safe, auditable, and ready to deploy.'*
>
> *'For the E5 footprint alone, that's tens of thousands of hours given back to account teams — every cycle.'*
>
> Thank you."

*[Pause. Hold the slide for 3 seconds before Q&A.]*

---

## Q&A Battle Cards (Quick Reference)

### If asked "Why Copilot SDK and not just Azure OpenAI function calling?"
> "Great question — it's the difference between building a car and building an engine. Azure OpenAI function calling gives you the engine — you call a model, it picks a function. But you still have to build the planner, the retry logic, context compaction across turns, streaming coordination, and session lifecycle. The Copilot SDK gives you the *car*. We register 8 tools, describe what they do, and the Agent Runtime — the same engine that powers Copilot CLI — decides when to call them, in what order, and how to synthesize the results. We focus on domain expertise. The SDK focuses on agent orchestration."

### If asked "What about scale — can this handle 100 concurrent users?"
> "The honest answer: we designed for 5 concurrent assessments because that matches the real usage pattern of account teams. Container Apps can scale to 5 replicas. For 100 concurrent users, we'd add session pooling and a gateway layer — but that's an architecture decision, not a limitation. The use case doesn't need it. We optimized for correctness and depth over throughput."

### If asked "How do you handle Graph API rate limiting?"
> "Each assessment makes a small set of read calls — well within Graph API's per-tenant limits. If we do hit a 429, the tool returns the retry-after header in its response and the agent transparently tells the user. We don't auto-retry aggressively. One assessment, one set of API calls. Clean."

### If asked "What if the customer doesn't want their data going to Azure OpenAI?"
> "Four of our eight tools — the assessment tools — never touch the LLM. They hit Graph API, format the data, and return it directly. The scorecard tool is also LLM-free. Only the remediation plan calls Azure OpenAI — and it receives PII-redacted data. A customer can run in assessment-only mode with zero LLM dependency. The remediation plan is opt-in."

### If asked "How does this compare to Secure Score's built-in recommendations?"
> "Secure Score tells you *what* to fix. We tell you *what to fix first, why, and how*. Secure Score doesn't prioritize by effort-to-impact ratio. It doesn't give you PowerShell scripts. It doesn't cross-correlate Purview gaps with Entra misconfigurations. SecPostureIQ sits on top of Secure Score data and adds the intelligence layer — cross-workload analysis, prioritization, and executable remediation."

### If asked "What was the hardest part?"
> "Two production-grade problems, not textbook problems. First: the Copilot SDK runtime runs as a child process requiring the GitHub CLI binary — that adds Docker image complexity we had to solve for Container Apps. Second: GPT-4o hallucinating base64 images for status badges instead of text emoji. We fixed both — and documented them in our SDK product feedback. These are the kinds of issues that only surface when you build for real."

### If asked "What would you build next?"
> "Three things: (1) Scheduled assessments that push to Fabric and alert on score regression — so customers know the moment their posture degrades. (2) Comparative analytics — 'How does this tenant compare to your other ME5 accounts?' using anonymized aggregate data. (3) Auto-generated executive decks — take the scorecard and produce a PowerPoint for the CISO meeting."

### If asked about the team's background
> "Ying-Jung has a PhD in Computer Science from Stanford focused on AI agents — she built J-browser-agents, a headless browser automation framework. Velen is a Cloud and AI Platform Solution Architect with deep experience across large-scale Azure infrastructure and vertical industries. Between us: AI agent research meets enterprise cloud architecture."

### If asked "Why two interfaces — CLI and Web?"
> "Different users, same brain. The CLI demonstrates the pure Copilot SDK integration — `CopilotClient`, `Tool` registration, streaming. It's for developers and power users. The web app is for account teams — Entra ID login, chat UI via Chainlit, session management. Both share the same 8 tools, system prompt, middleware, and schemas. Add a tool once, it appears in both."

### If asked "Tell me about your CI/CD pipeline depth"
> "Three workflows, zero stored secrets. Main pipeline: lint, test (688 tests, 80% coverage gate), Bicep validate, Docker build, Trivy vulnerability scan, ACR push, deploy to Container Apps, post-deploy health check. PR pipeline: creates an ephemeral preview environment per PR with its own Container App and auto-teardown on merge. Rollback workflow: manual trigger by Git SHA — verifies the image exists in ACR, deploys via Bicep, health-checks. All three use OIDC federation."

---

## Timing Targets

| Segment | Duration | Cumulative | Speaker | Narrative Beat |
|---------|----------|------------|---------|----------------|
| ACT 1: The Call to Adventure | 2:00 | 2:00 | Velen / YJ | Hook — "$4.44M" stat, hero receives mission |
| ACT 2: The Ordeal — Portal Maze | 2:00 | 4:00 | Velen / YJ | Pain — audience *feels* the manual grind |
| ACT 3: The Mentor — Why | 1:00 | 5:00 | Velen / YJ | Golden Circle WHY — deeper purpose |
| ACT 4: The Transformation — Demo | 7:00 | 12:00 | Velen / YJ | Wow — one conversation replaces 4 portals |
| ACT 5: Road of Trials — How | 2:00 | 14:00 | Velen / YJ | Golden Circle HOW — architecture |
| ACT 6: Ordeal Overcome — Ops/Sec | 3:00 | 17:00 | Velen / YJ | Trust — observability, tests, guarantees |
| ACT 7: The Return — Impact | 2:00 | 19:00 | Velen / YJ | Golden Circle WHAT — business case |
| Buffer | 1:00 | 20:00 | — | Flex time / transition to Q&A |
| Q&A | ~10:00 | 30:00 | Velen / YJ | Battle cards ready |

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
| Man-hours saved per 1K assessments | ~40,000 hours (at 40 hrs/manual assessment) |
| E5-category tenants (internal telemetry) | ~412K (non-test, directional) |
| ME5 tenants in ACR (lower bound) | ~17,700 (from subscription snapshot) |
| Graph API permissions | 5 (all read-only) |
| Middleware layers | 4 (Content Safety, PII, Tracing, Audit) |
| Container Apps replicas | 0–5 (scale-to-zero) |
| Dockerfile stages | 2 (builder + runtime), runs as non-root user |
| Docs | 18 files (architecture, setup, SDK feedback, multi-tenant strategy, lessons learned, etc.) |

---

## Portal Pain-Point Reference — What SecPostureIQ Replaces

The core value proposition: **4 portals, 12+ sub-pages, ~1 hour of manual clicking → 1 sentence, 8 tools, under 3 minutes.**

This section maps every Microsoft admin portal that an account team must manually visit today to the SecPostureIQ tool that replaces it.

### Portal 1: Microsoft Secure Score

| | |
|---|---|
| **Portal URL** | `security.microsoft.com/securescore` |
| **What account teams do manually** | Open the Secure Score overview → read overall score → click into each category (Identity, Data, Device, Apps, Infrastructure) → read individual recommendations → note which ones apply to E5 licensing → export to spreadsheet |
| **Time** | ~20 minutes per tenant |
| **SecPostureIQ tool** | `query_secure_score` |
| **What the tool returns** | Current score, category breakdown with current/max per category, 30-day trend (all 30 data points), industry comparison — in one response |
| **Graph API endpoint** | `GET /security/secureScores` |

### Portal 2: Microsoft Defender — Device Inventory

| | |
|---|---|
| **Portal URL** | `security.microsoft.com/endpoints/device-inventory` |
| **What account teams do manually** | Navigate to Defender > Endpoints > Device inventory → count onboarded vs total devices → calculate coverage percentage → note OS distribution and health status |
| **Time** | ~10 minutes |
| **SecPostureIQ tool** | `assess_defender_coverage` (Endpoint workload) |
| **What the tool returns** | Onboarded device count vs total, coverage percentage, gap identification |

### Portal 3: Microsoft Defender — Threat Policies (Office 365)

| | |
|---|---|
| **Portal URL** | `security.microsoft.com/threatpolicy` |
| **What account teams do manually** | Navigate to Policies > Threat policies → check Safe Links status → check Safe Attachments policies → check anti-phishing policies → check anti-spam → note which are active vs disabled |
| **Time** | ~10 minutes |
| **SecPostureIQ tool** | `assess_defender_coverage` (Office 365 workload) |
| **What the tool returns** | Active/inactive policy counts per type, coverage gap list for Safe Links, Safe Attachments, anti-phishing |

### Portal 4: Microsoft Defender — Identity & Cloud Apps

| | |
|---|---|
| **Portal URLs** | `security.microsoft.com/identities` (sensors), `security.microsoft.com/cloudapps` (connected apps) |
| **What account teams do manually** | Defender for Identity → check sensor health and deployment status. Defender for Cloud Apps → count connected apps, review policy alerts. Two separate navigation paths within the same portal. |
| **Time** | ~10 minutes |
| **SecPostureIQ tool** | `assess_defender_coverage` (Identity + Cloud Apps workloads) |
| **What the tool returns** | Sensor health, connected app count, coverage percentage per workload — combined into a single response |

### Portal 5: Microsoft Purview — DLP Policies

| | |
|---|---|
| **Portal URL** | `compliance.microsoft.com/datalossprevention` |
| **What account teams do manually** | Open Purview > Data loss prevention > Policies → count active vs test-only policies → note scope (Exchange, SharePoint, Teams, OneDrive, Endpoint DLP) → check if policies cover sensitive info types → check for custom policies |
| **Time** | ~15 minutes |
| **SecPostureIQ tool** | `check_purview_policies` (DLP section) |
| **What the tool returns** | Policy count, active vs test-mode status, scope coverage, sensitive info type coverage |

### Portal 6: Microsoft Purview — Sensitivity Labels & Retention

| | |
|---|---|
| **Portal URLs** | `compliance.microsoft.com/informationprotection` (labels), `compliance.microsoft.com/recordsmanagement` (retention) |
| **What account teams do manually** | Information Protection → check published sensitivity labels → check auto-labeling → check default labels. Records Management → check retention policies per workload (Exchange, SharePoint, OneDrive, Teams) → check retention labels. Insider Risk Management → check if enabled. |
| **Time** | ~15 minutes |
| **SecPostureIQ tool** | `check_purview_policies` (Labels + Retention + Insider Risk sections) |
| **What the tool returns** | Published label count, auto-labeling status, retention policy coverage by workload, Insider Risk Management status — all in one response |

### Portal 7: Microsoft Entra — Conditional Access

| | |
|---|---|
| **Portal URL** | `entra.microsoft.com/#view/Microsoft_AAD_ConditionalAccess` |
| **What account teams do manually** | Entra > Protection > Conditional Access → count policies → categorize: enforced vs report-only vs disabled → check for MFA policies → check for device compliance policies → check named locations → check session controls |
| **Time** | ~15 minutes |
| **SecPostureIQ tool** | `get_entra_config` (Conditional Access section) |
| **What the tool returns** | Policy count, enforcement status breakdown, MFA coverage, device compliance requirements, gap identification |

### Portal 8: Microsoft Entra — PIM & Identity Protection

| | |
|---|---|
| **Portal URLs** | `entra.microsoft.com/#view/Microsoft_Azure_PIMCommon` (PIM), `entra.microsoft.com/#view/Microsoft_AAD_IAM/IdentityProtectionMenuBlade` (Identity Protection) |
| **What account teams do manually** | PIM → count active vs eligible role assignments → check for Global Admin over-provisioning → review access review config. Identity Protection → check user risk policy → check sign-in risk policy → review risky users/sign-ins. |
| **Time** | ~15 minutes |
| **SecPostureIQ tool** | `get_entra_config` (PIM + Identity Protection sections) |
| **What the tool returns** | Active vs eligible assignments, over-privileged role alerts, risk policy status, access review configuration. Note: PIM data requires admin-level delegated permissions — the agent transparently flags this limitation. |

### Summary: The Before/After

| Manual Process | SecPostureIQ |
|---|---|
| 4 portals | 1 chat interface |
| 8+ portal URLs to navigate | 1 sentence: "Run a full assessment" |
| 12+ sub-pages to click through | 8 tools auto-chained by the agent |
| ~60–90 minutes to collect raw data | Under 3 minutes |
| Copy-paste into Word/Excel | Structured markdown with emoji status |
| No PowerShell scripts | Executable remediation scripts included |
| Manual prioritization | AI-prioritized P0/P1/P2 with effort estimates |
| Repeat for every customer | Same conversation, any tenant |

### Demo Tips for ACT 1b

- **Speed over detail**: Flip through tabs briskly (~15 sec each). The point is the *volume* of context-switching, not reading every field.
- **Narrate the friction**: "This is portal number three... and we're only halfway through." Count out loud.
- **Don't minimize the tabs beforehand**: Keep all 7 visible in the tab bar so the audience sees the wall of tabs.
- **The close gesture**: When transitioning to ACT 3, dramatically close all portal tabs one by one (or Ctrl+W rapidly) and say "Now let me show you one sentence that replaces all of that."
- **Fallback if portals are slow**: Have screenshots of each portal page ready — same visual impact without the load-time risk.
