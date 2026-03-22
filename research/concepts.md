# GitHub Copilot SDK — Key Concepts & Architecture

Clarifications from exploring the SDK architecture in depth.

---

## 1. SDK ≠ Runtime: What Does What?

**Common misconception**: "The Copilot SDK is the runtime and orchestrator for agents — it decides when to call tools, which tool to use, and how to route requests securely."

**Correction**: The SDK is a **thin client library**. The actual orchestration happens in the **Agent Runtime** (the Copilot CLI binary). The split is:

| Component | Role |
| --- | --- |
| **Copilot SDK** | Thin client — marshals your calls into JSON-RPC, manages the subprocess lifecycle, surfaces events/streams back to your app |
| **Copilot Agent Runtime** (CLI process) | The actual orchestrator — plans, routes to models, decides when/which tools to call, manages context, enforces safety boundaries |
| **GitHub Cloud** | Model inference, auth, remote tool execution |

The correct framing:

> **Your App → SDK (thin wrapper) → Agent Runtime (the brain) → Models + Tools**

The SDK gives you a **programmatic handle** to the runtime. It's the runtime that does the agentic work — planning, tool selection, multi-model routing, retries, context compaction.

### Why This Distinction Matters

1. **You don't need to implement any orchestration logic** — the runtime handles it
2. **You can't override the planner** — you register tools and the runtime decides when to invoke them
3. **Upgrading the runtime** (via CLI updates) improves your agent without changing your code
4. **Security boundaries are enforced by the runtime**, not your app

> Think of it like: the SDK is the **steering wheel**, the Agent Runtime is the **engine**, and GitHub Cloud is the **fuel**.

---

## 2. Where Each Component Lives

```
┌─────────────────────────────────────────────┐
│              YOUR MACHINE / NODE             │
│                                              │
│   Your App  ←→  SDK (in-process library)     │
│                    ↓ JSON-RPC (stdio)         │
│              Agent Runtime (child process)    │
│                                              │
└──────────────────────┬──────────────────────┘
                       │ HTTPS (outbound)
                       ▼
┌─────────────────────────────────────────────┐
│            GITHUB CLOUD                      │
│                                              │
│   Model Inference (GPT-4o, Claude, etc.)     │
│   Auth / Identity Validation                 │
│   Remote Tool Execution (if applicable)      │
│                                              │
└─────────────────────────────────────────────┘
```

| Component | Where it runs | How it's loaded |
| --- | --- | --- |
| **Your App** | Local process | You build and run it |
| **SDK** | In-process (library) | Imported as a package — lives in your app's memory space |
| **Agent Runtime** | Local child process | SDK spawns the `gh copilot` binary as a subprocess |
| **Models** | GitHub Cloud | Remote — always requires network |
| **MCP Servers** | Depends — can be local or remote | You configure endpoints |

**Your app, SDK, and agent runtime are all local.** Only model inference and auth hit the cloud.

---

## 3. Scaling: The Core Challenge

The architecture is **one runtime process per active agent session**, running locally. This is fundamentally a **scale-up, not scale-out** design:

```
1 user session  = 1 runtime process  = ~50-100 MB RAM + CPU
10 sessions     = 10 processes       = ~500 MB - 1 GB
100 sessions    = 100 processes      = you have a problem
```

### Why It Doesn't Scale Natively

This was designed for **developer-facing scenarios** (one developer, one agent) — not for multi-tenant server workloads. There's no shared runtime, no connection pooling to the agent engine, and no built-in horizontal scaling.

### Scaling Patterns

| Pattern | How | Trade-off |
| --- | --- | --- |
| **Vertical (scale up)** | Bigger machine, more RAM/CPU | Simple but has a ceiling |
| **Horizontal (scale out)** | Multiple nodes, each running N sessions, behind a load balancer | Works but each node still has the local process overhead |
| **Gateway pattern** | Edge devices → HTTP → centralized agent cluster | Decouples the edge from the runtime; best for high concurrency |
| **Session pooling** | Pre-spawn a pool of runtime processes, multiplex sessions across them | Reduces cold start; still bounded by memory |
| **Serverless/container** | Package runtime as a container, spin up per-request, tear down when idle | Pay-per-use; cold start penalty |

### Realistic Architecture for Scale

```
Edge Devices (thin clients, no runtime)
       │ HTTPS
       ▼
┌─────────────────────────┐
│   Agent Gateway Cluster  │  ← Kubernetes / Azure Container Apps
│   ┌───┐ ┌───┐ ┌───┐     │
│   │RT1│ │RT2│ │RT3│ ... │  ← Each pod runs App + SDK + Runtime
│   └───┘ └───┘ └───┘     │
└──────────┬──────────────┘
           │ HTTPS
           ▼
      GitHub Cloud (models)
```

- **Edge devices** are thin HTTP clients — they don't run the runtime at all
- **Agent gateway nodes** run the full stack (app + SDK + runtime) and handle multiple sessions
- A load balancer distributes sessions, and autoscaler adds/removes pods based on demand
- Session affinity ensures multi-turn conversations stick to the same pod

### Concurrency Guidance

| Scale | Approach |
| --- | --- |
| **Low** (few sessions/node) | Works well as-is |
| **Medium** (dozens/node) | Use process pooling + session management |
| **High** (thousands/node) | Use an intermediary gateway rather than running the runtime directly on edge |

The bottleneck is more likely the **outbound network latency to GitHub's cloud** from the edge than the local process overhead.

---

## 4. "Your App" ≠ The Agent

Another common misconception: "Your App is actually an AI agent."

**Correction**: Your app is the **agent host**, not the agent itself. The **agent** is the emergent combination of several pieces working together:

```
┌─ "The Agent" ──────────────────────────────────┐
│                                                 │
│   Your App (host)                               │
│     - Defines what the agent CAN do             │
│     - Registers tools (e.g., "query_logs",      │
│       "create_ticket", "read_repo")             │
│     - Sets system prompt / persona              │
│     - Manages session lifecycle                 │
│     - Handles UX (chat UI, CLI, API, etc.)      │
│                                                 │
│   Agent Runtime (engine)                        │
│     - Decides WHEN to call tools                │
│     - Decides WHICH model to use                │
│     - Plans multi-step sequences                │
│     - Manages context / memory                  │
│     - Enforces safety boundaries                │
│                                                 │
│   Models (brain)                                │
│     - Reasoning, language understanding         │
│                                                 │
│   Tools / MCP Servers (hands)                   │
│     - Actually do things in the world           │
│                                                 │
└─────────────────────────────────────────────────┘
```

| Analogy | Component | Role |
| --- | --- | --- |
| **Job description** | Your App | Defines what the agent is for, what tools it has access to, what its boundaries are |
| **Brain** | Runtime + Models | Decides what to do, plans, reasons |
| **Hands** | Tools / MCP | Executes actions |
| **Body** | SDK | Wiring that connects everything |

### What Your App Actually Does

Your app is a **regular application** (web server, CLI tool, background service, etc.) that **hosts and configures** an agent by:

1. Importing the SDK
2. Registering tools (functions the agent can call)
3. Setting a system prompt (the agent's persona/instructions)
4. Starting a session and forwarding user input
5. Handling the agent's responses (rendering in a UI, sending as an API response, etc.)

### Concrete Example

```python
# Your App — this is NOT an agent, it's an agent HOST
from copilot_sdk import CopilotClient

client = CopilotClient()

# You define the agent's capabilities
client.register_tool("search_logs", search_logs_fn)
client.register_tool("create_incident", create_incident_fn)

# You set the agent's identity
client.set_system_prompt("You are an SRE assistant...")

# You start a session and hand off to the runtime
session = client.create_session()
response = session.send("Why is latency spiking?")
# ↑ From here, the RUNTIME decides to call search_logs,
#   analyze results, maybe call create_incident, etc.
#   Your app just waits for the response.
```

**Your app builds the agent. The runtime runs the agent. The models power the agent. The tools are the agent's capabilities.**

---

## 5. What Constitutes an "Agent"?

In the Copilot SDK context, an agent is not a single component — it's the **behavior that emerges** when you combine four pieces:

```
Agent = Configuration + Engine + Intelligence + Capabilities
        (Your App)     (Runtime)  (Models)       (Tools/MCP)
```

### Agent vs. Chatbot

| Property | Chatbot | Agent |
| --- | --- | --- |
| **Takes actions** | No — only generates text | Yes — calls tools, writes files, creates tickets |
| **Plans multi-step** | No — single request/response | Yes — breaks a goal into steps, executes sequentially |
| **Uses tools autonomously** | No — human picks the tool | Yes — decides which tool, when, in what order |
| **Loops** | No — one shot | Yes — observe → reason → act → observe again |
| **Has memory** | Minimal — maybe chat history | Yes — context across turns, compaction, state |

### The Minimum Viable Agent (in Copilot SDK terms)

An "agent" exists the moment you have:

1. **A system prompt** — defines the agent's identity, purpose, and constraints
2. **At least one tool** — something the agent can *do* beyond talking
3. **A session** — a persistent context where the runtime can plan and act across multiple turns

Without tools, it's just a chatbot. Without a session, it's just a one-shot completion. Without a system prompt, it has no purpose.

### Concrete Example

```
"SRE Agent" =
   System prompt: "You are an SRE assistant for Contoso's Kubernetes clusters"
 + Tools: [search_logs, get_pod_status, create_incident, restart_pod]
 + Session: Multi-turn, remembers what it already investigated
 + Runtime: Copilot's planner decides tool order
 + Model: GPT-4o for reasoning
```

When a user says "Why is the checkout service slow?", this combination **autonomously**:

1. Calls `search_logs("checkout-service", last_30min)`
2. Sees high latency in pod-3
3. Calls `get_pod_status("pod-3")`
4. Sees OOMKilled
5. Calls `create_incident(severity="P2", summary="checkout-service pod-3 OOM")`
6. Responds: "Pod-3 is OOM-killed. I've created a P2 incident. Want me to restart it?"

**No single component is the agent.** Your app didn't plan those steps. The runtime did. The model reasoned about what to do next. The tools executed. Together — that's the agent.

### The Key Insight

> An agent is a **loop**, not a **thing**.
>
> `Observe → Reason → Act → Observe → Reason → Act → ...`
>
> The Copilot SDK gives you the loop for free. You just supply the tools and the identity.
