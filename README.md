# Guardian AI — Protecting Rights, Preventing Gaps

**Utah AI Builder Day Hackathon | Civil Commitment Management Prototype**

---

## The Problem

Right now in Utah there are nearly **1,400 people on active civil commitment** — up 42% in seven years. When an officer files an emergency hold at 2 AM, a 72-hour legal clock starts. If a court order doesn't arrive in time, the patient **must** be released — regardless of their condition.

Today that clock is tracked on paper and spreadsheets. Nobody gets an alert. The state is one missed deadline away from a lawsuit — or a tragedy.

## The Challenge

Build **Guardian AI** — a digital commitment management prototype that replaces paper forms with a real-time, AI-monitored system. Your demo should show:

- A mobile-ready form for filing emergency holds (Blue Sheet / Pink Sheet)
- A live 72-hour countdown with proactive escalation alerts
- An AI agent that answers natural-language questions about active cases
- A simulated court-order integration that stops the clock in real time
- A statewide dashboard showing urgency at a glance

**Stack is open.** Use whatever tools move you fastest — "vibe coding" with AI assistants is encouraged. Salesforce mentorship is available if you want Agentforce, MuleSoft, or Lightning, but it's not required.

## Why It Matters

| Stat | Source |
|------|--------|
| ~1,400 active civil commitments in Utah | DHHS/SUMH Oct 2024 report |
| 41.6% growth since 2017 | DHHS/SUMH Oct 2024 report |
| 87% managed in the community, not hospitals | DHHS/SUMH Oct 2024 report |
| 300–400 bed shortage statewide | DHHS capacity analysis |
| 8,000+ behavioral health professionals needed | 2024 state workforce audit |
| 25% of officer-involved shootings involve mental illness | Utah current-state data |
| 29.9% of Utah adults have any mental illness | National survey data |

## Constraints

| Constraint | Detail |
|------------|--------|
| **Time** | ~24 hours of build time |
| **Demo window** | 4 minutes — every second counts |
| **Team size** | 1–6 people |
| **Evaluation** | Vision + feasibility + demo quality. Mock data is fine; judges care about the workflow, not real court connections |
| **Compliance context** | HB 137 and Utah Code 26B-5-331 have 2025/2026 deadlines — your prototype should reference real legal requirements |

## Roles Needed

You don't need all of these — a solo builder can ship a winning demo. But if you have a team, here's how to divide and conquer:

| Role | What You'll Do | Good Fit If You... |
|------|---------------|-------------------|
| **Data Architect** | Design the schema (objects, fields, relationships) that everything else depends on | Think in data models and relationships |
| **Flow / Automation Builder** | Build the interactive forms and behind-the-scenes automation (clock start, escalation alerts) | Like process logic and if/then thinking |
| **UX / Frontend Builder** | Make the dashboard, record pages, and mobile form look polished and demo-ready | Have design sense and care about usability |
| **AI / Agent Config** | Wire up the AI assistant — topics, actions, prompt templates, grounded in live data | Understand LLMs and prompt engineering |
| **Storyteller / Documentarian** | Craft the pitch narrative, architecture diagram, before/after workflow visuals | Are a strong writer and communicator |
| **Demo Lead / Presenter** | Own the live 4-minute demo — rehearse, time, have backup plans | Stay calm under pressure and present well |

### Solo / Small Team?

- **1 person**: Focus on one form + countdown + one AI query. That's a winning demo.
- **2 people**: Split build (forms + automation) vs. polish (UI + agent + presentation).
- **3 people**: Data + Flows | UX + Agent | Demo + Docs.

## What's In This Repo

```
docs/               Background research, legal references, forms, scoping doc
plan/build-plan.md  Detailed technical build plan (phases, deploy commands, architecture)
index.html          Interactive UI mockup (open in browser to see the full prototype vision)
```

## Quick Start

1. **Clone this repo** and open `index.html` in a browser to see the target experience
2. **Read** `plan/build-plan.md` for the full technical breakdown
3. **Pick your stack** — Salesforce, React, Flutter, whatever moves you fastest
4. **Build** the core loop: file a form, start a clock, show urgency, demonstrate AI assistance
5. **Demo** in 4 minutes or less

## Key Legal Context

Utah Code **26B-5-331** governs temporary emergency commitment:
- 72-hour hold (excludes weekends/state holidays)
- Patient must be informed of reason + granted phone call within 3 hours
- Two pathways: Blue Sheet (with physician certification) or Pink Sheet (officer observation only)
- 2024 amendments add criminal-charge pathway (HB 203) and officer release notification requirements

Full legal reference: [`docs/legal-reference.md`](docs/legal-reference.md)

## Links

- **[Live Interactive Mockup](https://drpaulcoleman.github.io/aibuilderday/)** — click through the full prototype vision in your browser
- [Hackathon challenge brief](docs/Guardian%20AI%20-%20Bounty%20_%20Use%20Case%20Challenge.md)
- [Project scoping document](docs/aibuildersday-scoping-doc.md)
- [Civil commitment capacity report (PDF)](docs/civil-report.pdf)
- [Current state data](docs/current-state.txt)
- [Detailed build plan](plan/build-plan.md)

---

*Guardian AI: because a 72-hour clock should never expire in silence.*
