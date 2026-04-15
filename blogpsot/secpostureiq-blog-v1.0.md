# SecPostureIQ: An Agentic Security Posture Assessment Tool Built with GitHub Copilot SDK

*By [Velen Liang](https://www.linkedin.com/in/velenliang) and [Ying-Jung Chen](https://www.linkedin.com/in/yj-elizabeth-chen/)*

---

Four portals. Twelve sub-pages. One hour just to collect raw data — before writing a single recommendation.

That's the reality of assessing a customer's Microsoft 365 E5 security posture today. Account teams running Microsoft's "Project 479 — Get to Green" campaign need to evaluate coverage across Defender, Purview, Entra ID, and Secure Score for every tenant they manage. The process takes one to two weeks per assessment and involves manually navigating admin portals, cross-referencing configurations against best practices, and compiling findings into spreadsheets.

We built SecPostureIQ to compress that into a three-minute conversation.

## The problem: security tools that nobody configured

Microsoft's ME5 security suite is powerful — Defender XDR, Purview information protection, Entra ID P2 identity governance, and Secure Score benchmarking. But purchasing a license doesn't protect anyone. The security tools need to be *configured*.

Internal telemetry shows over 400,000 tenants in the E5 family. Many purchased premium security licensing years ago but never fully activated it. Account teams are responsible for assessing each tenant's posture and guiding customers to green — but the manual assessment process doesn't scale.

The bottleneck isn't the product. It's the *assessment*.

![Manual portal workflow — account teams navigate Secure Score, Defender, Purview, and Entra portals separately](docs/screenshots/microsoft-secure-score.png)

## What we built

SecPostureIQ is an agentic assistant built on the [GitHub Copilot SDK](https://github.com/github/copilot-sdk) that assesses a tenant's ME5 security posture in minutes. It connects to Microsoft Graph Security APIs, evaluates coverage across four workloads, then generates a prioritized remediation plan with executable PowerShell scripts.

The agent doesn't follow a fixed script. It reasons across eight specialized tools and autonomously decides the execution order based on what it finds — if Defender coverage is strong but Purview is absent, it focuses remediation on information protection gaps. The SDK runtime handles the planning; we focus on domain expertise.

## Architecture

```
┌────────────────────────────────────────────────────────┐
│                    User Interfaces                     │
│       CLI (Copilot SDK)  ·  Web Chat (Chainlit)        │
└────────────────────────────┬───────────────────────────┘
                             │
                             ▼
┌────────────────────────────────────────────────────────┐
│           Copilot SDK → Agent Runtime (CLI)            │
│           System Prompt + 8 Registered Tools           │
└────────────────────────────┬───────────────────────────┘
         ┌───────────────────┼───────────────────┐
         ▼                   ▼                   ▼
┌────────────────┐  ┌────────────────┐  ┌────────────────┐
│   Middleware   │  │   Assessment   │  │   AI-Powered   │
│     Layers     │  │     Tools      │  │     Tools      │
├────────────────┤  ├────────────────┤  ├────────────────┤
│ Content Safety │  │ Secure Score   │  │ Remediation    │
│ PII Redaction  │  │ Defender       │  │ Scorecard      │
│ Audit Trail    │  │ Purview        │  │ Foundry IQ     │
│ Tracing        │  │ Entra          │  │ Fabric Push    │
└────────────────┘  └────────────────┘  └────────────────┘
                             │                   │
                         Graph API         Azure OpenAI +
                                         Foundry IQ / Fabric
```

*[High-resolution architecture diagram](https://github.com/9owlsboston/posture-iq/blob/main/docs/diagrams/high-level-architecture.svg)*

### How the SDK fits in

An important nuance: the Copilot SDK itself is a thin client. The real orchestration happens in the Agent Runtime — the Copilot CLI binary, which the SDK spawns as a child process and communicates with via JSON-RPC over stdin/stdout. We register eight tools, provide a system prompt that encodes the assessment methodology, and the runtime handles the rest — deciding execution order, managing context across tool calls, and synthesizing results into a coherent response.

We didn't build a planner, a tool loop, or a context manager. The SDK is the steering wheel; the Agent Runtime is the engine.

### The eight tools

Each tool wraps Microsoft Graph Security API calls or Azure services, returning structured data for the agent to reason over:

1. **`query_secure_score`** — Baseline score + 30-day trend + industry benchmark
2. **`assess_defender_coverage`** — XDR deployment gaps per workload (Endpoint, Office, Identity, Cloud Apps)
3. **`check_purview_policies`** — DLP, sensitivity labels, retention, insider risk coverage
4. **`get_entra_config`** — Conditional Access, PIM, Identity Protection, Access Reviews
5. **`generate_remediation_plan`** — AI-generated prioritized steps (P0/P1/P2) with PowerShell scripts
6. **`create_adoption_scorecard`** — Green/yellow/red overview with estimated days-to-green
7. **`get_green_playbook`** — Maps gaps to Foundry IQ remediation playbooks and recommended offers
8. **`push_posture_snapshot`** — Writes assessment snapshots to Fabric lakehouse for trend dashboards

Four middleware layers wrap every request: Azure AI Content Safety screening, PII redaction, distributed tracing via OpenTelemetry, and an immutable audit log.

### Code: tool registration

```python
@tool(
    name="query_secure_score",
    description="Retrieve the tenant's Microsoft Secure Score "
                "with category breakdown and 30-day trend"
)
async def query_secure_score() -> dict:
    """Calls Microsoft Graph Security API and returns
    structured assessment data."""
    response = await graph_client.get(
        "/security/secureScores?$top=1"
    )
    score = response.json()["value"][0]
    return {
        "current_score": score["currentScore"],
        "max_score": score["maxScore"],
        "percentage": round(
            score["currentScore"] / score["maxScore"] * 100, 1
        ),
        "categories": [
            {
                "name": c["category"],
                "current": c["score"],
                "max": c["maxScore"],
            }
            for c in score["controlScores"]
        ],
        "data_source": "live" if graph_token else "mock",
    }
```

> This is a simplified excerpt. The actual implementation includes PII redaction, App Insights tracing spans, and error handling for expired tokens. See the [full source](https://github.com/9owlsboston/posture-iq/tree/main/src/tools).

## A full assessment in action

Here's what happens when an account team member types: *"Run a full assessment of this tenant's ME5 security posture"*

1. **The agent calls `query_secure_score`** — retrieves the tenant's Secure Score: 40.49% (140.5 / 347 points). Category breakdown reveals Identity at 47%, Data at 0%, Apps at 38%.

2. **The agent calls `assess_defender_coverage`** — discovers Defender for Endpoint has zero onboarded devices, Safe Links and Safe Attachments policies are disabled, and Defender for Identity sensors are not deployed.

3. **The agent calls `check_purview_policies`** — finds no active DLP policies, sensitivity labels published but auto-labeling is off, and retention policies missing for Teams.

4. **The agent calls `get_entra_config`** — Conditional Access policies exist but are in report-only mode. PIM is active with 10 highly privileged identities. Identity Protection risk policies are not configured.

5. **The agent calls `generate_remediation_plan`** — GPT-4o synthesizes all findings into five prioritized steps (P0–P2), each with estimated Secure Score impact, effort level, confidence score, and a copy-paste PowerShell script.

6. **The agent calls `create_adoption_scorecard`** — produces a red/yellow/green table: Defender (**red**), Purview (**red**), Entra (**yellow**), Secure Score (**yellow**). Estimated days to green: 14.

The entire sequence completes in under three minutes. The agent autonomously decided the tool order — we didn't hardcode the chain.

![Web chat and CLI running side-by-side — same tools, same results](docs/screenshots/web-cli-dual-client.png)

## Evaluation and measured impact

### Speed

| Metric | Manual (before) | SecPostureIQ (after) |
|--------|-----------------|----------------------|
| Time to assess one tenant | 1–2 weeks (40+ hours) | Under 3 minutes |
| Portals visited | 4 portals, 12+ sub-pages | 0 — one conversation |
| Output format | Word doc / spreadsheet | Structured scorecard + executable scripts |

### Accuracy

We validated all eight tools against a live M365 E5 tenant — not mock data. Real Secure Score (40.49%), real Defender coverage gaps, real Conditional Access policies. The adoption scorecard correctly identified 16.4% ME5 adoption — the tenant had purchased E5 but barely configured it.

Remediation scripts generated by the agent (`Set-SafeLinksPolicy`, `New-DlpCompliancePolicy`) ran successfully against the test tenant.

### Resilience and safety

- **1,236 tests** (1,195 unit + 41 integration) with an 80% coverage gate — CI fails if coverage drops
- **Content Safety adversarial testing** — prompt injection attempts were blocked by both a heuristic layer and Azure AI Content Safety
- **PII redaction validation** — all tenant GUIDs, user emails, and IP addresses confirmed redacted before reaching Azure OpenAI, verified via App Insights traces
- **Read-only guarantee** — the agent never modifies a customer's tenant; all Graph API permissions are read-only

### Scale estimate

At 40 hours per manual assessment, 1,000 accounts represents 40,000 man-hours saved. Internal telemetry shows over 17,700 ME5 tenants and approximately 412,000 E5-category tenants — the manual approach never could have scaled.

## What we learned

**SDK friction: CLI binary in Docker.** The Copilot SDK runtime requires the GitHub CLI binary as a child process. Packaging this into a Docker image for Azure Container Apps required a multi-stage Dockerfile with explicit `gh` installation — not documented in the getting-started guide. We solved it with a two-stage build (builder + runtime) running as non-root.

**SDK friction: GPT-4o hallucinating base64 images.** When asked for status badges, GPT-4o generated fake base64-encoded PNG images instead of text emoji. We added output validation in the scorecard tool to detect and strip non-text content.

**Why SDK, not Azure OpenAI function calling?** The difference is building a car versus building an engine. Function calling gives you tool dispatch — you still build the planner, retry logic, context compaction, streaming, and session lifecycle. The SDK gives you the complete agent runtime. We register eight tools and the runtime decides when to call them, in what order, and how to synthesize results.

**Graph API scope management.** Getting least-privilege right across eight different API endpoints required careful scoping — five read-only permissions, with specific handling for PIM (which returns 403 for app tokens by design, requiring delegated-only access).

**Two interfaces, one brain.** The same eight tools, system prompt, and middleware power both the Copilot SDK CLI (for developers) and the web chat UI via Chainlit (for account teams). Add a tool once, it appears in both interfaces.

## By the numbers

| Metric | Value |
|--------|-------|
| Assessment tools | 8 |
| Azure services integrated | 9 |
| Total tests | 1,236 |
| CI/CD workflows | 3 |
| Bicep IaC modules | 7 |
| Graph API permissions | 5 (all read-only) |
| Middleware layers | 4 |
| Assessment time | Under 3 min (vs. 1–2 weeks) |

![Azure resources deployed for SecPostureIQ](docs/screenshots/azure-integration.png)

## What's next

Three planned extensions:

1. **Scheduled assessments + regression alerts** — push posture snapshots to Fabric on a schedule and alert account teams when a tenant's Secure Score drops below threshold.
2. **Comparative analytics** — "How does this tenant compare to your other ME5 accounts?" using anonymized aggregate data across assessed tenants.
3. **Auto-generated executive decks** — take the adoption scorecard and produce a PowerPoint ready for the CISO briefing and the Project 479 tracker.

## Try it yourself

SecPostureIQ works out of the box with mock data — no Azure subscription, no credentials, no M365 tenant required:

```bash
git clone https://github.com/9owlsboston/posture-iq.git
cd posture-iq
python3.11 -m venv .venv && source .venv/bin/activate
pip install -r requirements.lock && pip install -e ".[dev]"
python -m uvicorn src.api.app:app --host 0.0.0.0 --port 8000
# Open http://localhost:8000 → Try: "What is our Secure Score?"
```

To assess a real M365 E5 tenant, see the [Setup Guide](https://github.com/9owlsboston/posture-iq/blob/main/SETUP.md).

[Explore the repo on GitHub →](https://github.com/9owlsboston/posture-iq)

---

## About the authors

**[Velen Liang](https://www.linkedin.com/in/velenliang)** is a Cloud & AI Platform Solution Architect at Microsoft with deep experience across large-scale cloud infrastructure and vertical industries. He designed SecPostureIQ's architecture, Azure deployment pipeline, and enterprise security layers.

**[Ying-Jung Chen](https://www.linkedin.com/in/yj-elizabeth-chen/)** is an AI Agent Researcher at Microsoft with a PhD in Computer Science from UC Santa Barbara, focused on autonomous agent systems. She built SecPostureIQ's agentic reasoning layer, tool orchestration, and evaluation framework.
