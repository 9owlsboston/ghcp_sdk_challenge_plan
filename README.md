# SecPostureIQ — Challenge Planning Repo

Planning, research, and presentation materials for **SecPostureIQ**, an ME5 security posture assessment agent built for the [GitHub Copilot SDK Enterprise Challenge](challenge/ghcp_sdk_challenge.md).

**Implementation repo:** [9owlsboston/posture-iq](https://github.com/9owlsboston/posture-iq)

**Team:** Ying-Jung Chen (yjcmsft) + Velen Liang (9owlsboston)

---

## What SecPostureIQ Does

Replaces a 1–2 week manual ME5 security posture assessment with a 3-minute AI-driven conversation. One prompt triggers 8 tools against the Microsoft Graph Security API, producing a prioritized remediation plan with executable PowerShell scripts.

Built to accelerate **Project 479 ("ME5 Get to Green")** — Microsoft's internal campaign to drive security adoption across thousands of M365 E5 accounts.

---

## Repo Structure

```
├── challenge/          # Challenge rules, judging criteria, SDK reference
├── research/           # Problem space research, MVP candidates, Project 479
├── planning/           # Implementation plan for the chosen agent
├── presentation/       # Final demo script, Q&A prep, judge questions
└── ideation/           # Early exploration & alternative agent ideas
```

| Folder | Contents |
|--------|----------|
| **challenge/** | Challenge brief, judging criteria (100 base + 35 bonus pts), Copilot SDK architecture overview |
| **research/** | Key concepts, MVP candidate evaluation, Project 479 overview |
| **planning/** | PostureIQ implementation plan — phases, tools, Graph API scopes, Azure services |
| **presentation/** | 20-minute demo script (Hero's Journey arc), Q&A battle cards for all 5 judge categories, sample judge questions |
| **ideation/** | Alternative agent concepts (SEBAS browser agent), team background notes |

---

## Key Documents

- [Demo Script](presentation/final_presentation_demo_script.md) — 7-act presentation with Hero's Journey narrative
- [Q&A Prep](presentation/final_presentation_qa_prep.md) — Answers for all 21 judge sample questions across 5 categories
- [Agent Plan](planning/posture_iq_agent_plan.md) — Full implementation plan with phases, tools, and Azure architecture
- [Project 479 Overview](research/project-479_overview.md) — The business context driving the agent
- [Challenge Rules](challenge/ghcp_sdk_challenge.md) — Submission criteria and judging rubric
