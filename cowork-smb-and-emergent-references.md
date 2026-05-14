# Cowork-for-SMB and Emergent — Two Reference Points for the Yantra Harness UX

Research note synthesising (1) Anthropic's Claude for Small Business announcement (May 13, 2026) as the SMB analogue of what Yantra's harness should look like for large enterprises, and (2) the Anthropic ↔ Emergent Labs / Mukund Jha conversation around Wingman and AI-native application building, as a reference point for how Yantra's authoring stack should think about reach and adoption.

> Cross-references the existing UI mockups in `github.com/gauravomsharma2024/Yantra` (CredMitra cockpit, branch `claude/agent-friendly-fintech-ui-1WF91`).

---

## 1. The Two Things Yantra Solves — Stated Once, Sharply

Before either comparison is useful, the framing has to be unambiguous. **Yantra solves two distinct problems**, with two distinct UX surfaces, two distinct buyers, two distinct go-to-markets:

| | Problem A — Authoring | Problem B — Runtime |
|---|---|---|
| **What** | Author business processes in plain English while keeping them deterministic, audit-grade, and regulator-defensible | Run those processes end-to-end with agents, with humans in the loop where required, on sovereign Indian infrastructure |
| **Yantra product** | YantraAuthor + ProcessMaster (in ProcessRepo) | The Yantra harness (Knowledge Graph + LLM Adapter + Camunda + YantraGateway + Agent Runtime + lifecycle subagents) |
| **Primary user** | Senior policy author, compliance officer, risk head | Operator (underwriter, branch ops, collections, customer service) |
| **Reference point this note compares against** | **Emergent + Wingman** (vibe-build / vibe-operate) | **Claude for Small Business** (Cowork-SMB) |
| **What's authored** | ProcessSkills (the artefact the institution owns) | Live state — applications, accounts, claims, communications |
| **Primary buyer** | CHRO + COO + CRO (the knowledge-author transformation) | CIO + CRO (the operational substrate) |

This bifurcation is the single most important framing decision. When the user says *"Yantra is the large-business equivalent of Cowork-for-SMB"* — they are talking about **Problem B's runtime**. When the user says *"ProcessSkills are a vibe-coding-like authoring construct"* — they are talking about **Problem A's authoring**. Both true; neither one alone is the platform.

---

## 2. Claude for Small Business — The Reference Architecture for SMBs

### 2.1 What Anthropic shipped (May 13, 2026)

**Claude for Small Business** is a packaged set of integrations, agentic workflows, and reusable skills designed to bring Claude into the day-to-day operating fabric of a small business — not as a chatbot bolted on, but as a *coworker* sitting alongside the owner across finance, ops, sales, marketing, HR, and customer service.

The package, as announced:

| Element | Substance |
|---|---|
| **15 ready-to-run agentic workflows** | Across finance, ops, sales, marketing, HR, customer service — payroll planning, month-end close, cash-flow forecasting, invoice chasing, campaign creation, etc. |
| **15 skills** | Built on the "repeatable tasks owners told us slow them down most" |
| **Connectors** | QuickBooks, Canva, DocuSign, HubSpot, PayPal — the SMB stack |
| **Surface** | Claude Cowork — same Cowork UX Anthropic ships to enterprise, with SMB-tuned defaults |
| **Distribution** | Free half-day "AI fluency" workshops in 10 US cities; partnership with LISC + Workday Foundation on Solopreneurship Accelerator |
| **Pricing** | Anthropic has not separately priced the package — bundled into Cowork plans |

### 2.2 Why this is exactly the right reference for the Yantra harness

Strip away the SMB framing and the marketing surface. What Anthropic actually built is **a domain-specific operating environment for a class of organisation**, composed of:

- A **harness** (Cowork) — the runtime where work happens, where humans see what agents are doing, where they intervene, where decisions get made
- A **plugin pack** for that class — the 15 workflows + 15 skills + connectors that come pre-installed
- A **buyer journey** matched to the class — workshops, fluency training, partner-led distribution
- A **deliberate restriction of variance** — owners do not configure their own DMN tables, they pick from packaged workflows that *just work*

That is structurally identical to what Yantra builds for large Indian financial institutions:

| Anthropic's choice (SMB) | Yantra's choice (large India-FS) |
|---|---|
| Cowork as the harness | Yantra harness (Knowledge Graph + adapter + Camunda + gateway + runtime UI) |
| 15 workflows for SMB tasks | 7 core verticals + 10 product-lines + 11 cross-cutting functions of ProcessRepo |
| QuickBooks / Canva / DocuSign / HubSpot / PayPal | AA / GSTN / Bureau×3 / CKYCR / DigiLocker / NACH / VAHAN / IGRS / Karza / Perfios / CredGenics (via YantraGateway) |
| Pre-packaged skills SMB owners pick from | Pre-packaged ProcessSkills institutions install + customise |
| Owners cannot edit underlying logic | Institutions *do* edit underlying logic — but through ProcessMaster, in plain language |
| AI Fluency workshops + Solopreneur Accelerator | Yantra Discover engagements + author-elevation training |
| Anthropic-hosted, Cowork-bundled | Sovereign Indian cloud (Yotta / Airtel / Ashoka / NTT) by default |

**The deepest insight: Anthropic did not invent a new product category for SMBs.** They took their existing harness (Cowork) and *opinionated it for the class*. That is precisely the move Yantra makes for Indian financial services. Most of the design effort goes into the opinionation, not into the underlying runtime — same pattern.

### 2.3 What's deliberately different for Yantra

Five structural differences from Cowork-SMB, all driven by the customer profile (large regulated institution, not small business owner):

1. **Variance is not restricted — it is captured.** An SMB owner picks the "month-end close" workflow as-shipped. A bank's CFO has *its* month-end close, which has 30 institution-specific edge cases. Yantra cannot ship the institution's version pre-baked, so it ships the *scaffold*, and the institution authors the rest — in plain English, via ProcessMaster.
2. **Roles are separated.** Cowork-SMB is one user (the owner). A bank operator dashboard has eight distinct roles seeing eight distinct slices of the same workflow — underwriter, compliance, fraud, branch ops, supervisor, credit committee, customer service, audit. The UI is multi-role from day one.
3. **Determinism is non-negotiable.** Cowork can paraphrase customer emails. A KFS letter cannot be paraphrased; it has to render exactly the compliance-approved template with the borrower's name substituted. The Yantra harness exposes deterministic engines (DMN, Camunda, helpers, templates) as first-class UI citizens, not hidden behind LLM judgement.
4. **Audit is a primary view.** RBI inspections happen; SMBs don't get inspected. Every action a Yantra agent takes has to be replayable to a regulator's standard, with version-pinned policies, signed reviewers, WORM logs. The UI exposes this provenance, not hides it.
5. **Multi-LLM is structural, not aesthetic.** Cowork is Claude-only by design. Yantra cannot be — sensitivity tiers, sovereignty constraints, and language requirements (Hindi + 6 vernaculars) force a router. The UI surface "which model handled this step" needs to exist for operator-trust reasons.

---

## 3. The Existing Yantra UI Mockups — What They Already Got Right

The CredMitra cockpit mockups in the Yantra repo (`claude/agent-friendly-fintech-ui-1WF91` branch) already encode most of the right answers. Reviewed against the Cowork-SMB reference, the design is on a strong trajectory.

### 3.1 The four mockups and what each represents

| File | Paradigm | Best for |
|---|---|---|
| `option-1-channel.html` | Slack-style channels per process; loan applications as cards with nested agent rows; right panel for memory stores + dreams | Ops teams who already live in Slack/Teams |
| `option-2-terminal.html` | Dark monospace terminal; histogram + dense trace logs | High-volume estates where information density matters |
| `option-3-pipeline.html` | Kanban swim-lanes — Intake → Underwriting → Fraud → HITL → Decided | COOs / supervisors focused on flow metrics |
| `process-repo.html` | The ProcessRepo browser with 5 operational modes (Execute / Audit / Test / Support / Train) for each ProcessSkill | Authors, compliance, audit, training |

### 3.2 Concepts the mockups already nail

- **Three-column layout** (process navigator / live workstream / memory + dream panel) — the right basic shape; mirrors Cowork's left-rail / centre / right-rail with the *right* substitutions for an NBFC ops floor
- **Loan application as a card with multi-agent execution rows** — agent-as-child-of-work-item is exactly the model Anthropic's Multiagent Orchestration produces; the UI exposes it correctly
- **Memory stores with permission tiers** (org-policy RO, org-bureau RO, team-uw RW, team-fraud RW) — directly maps to Yantra Knowledge Graph + sensitivity tier model
- **Dream summaries** (e.g. "team-uw · 12 May 02:41 IST · 218 sessions reviewed · 4 edits proposed · 2 pending CRO review") — operationalises Anthropic's Dreaming feature with named human review, which is what a regulated environment requires
- **Severity-coded execution states** (paused/HITL amber, escalated red, live teal, approved gray) — readable at a glance; consistent across views
- **Trace logs inline in cards** (READ org-policy, RAN dmn:eligibility, WROTE team-uw) — every action is auditable on the spot; no separate "audit view" trip required
- **Sticky footer with SLA + cost telemetry** (P50, P95, auto-decision rate, HITL queue depth, memory write velocity, token cost ₹/day) — this is what a credit head actually wants to see; analogous to but more rigorous than Cowork's surface KPIs
- **The 5-mode tabbed view on each ProcessSkill** (Execute / Audit / Test / Support / Train) — directly maps to the four lifecycle subagents + Execute (the runtime itself). **The mockup was prescient** — it anticipated the construct documented in Chapter 3 of the platform spec. This validates both directions.

### 3.3 Gaps in the existing mockups — where the Cowork-SMB comparison points

Five additions worth making to the UI to complete the harness story:

1. **A `Compose` affordance modelled on Cowork's task box.** Cowork-SMB lets the owner type "send invoice reminders to all customers > 30 days late" and the agent dispatches. Yantra's equivalent for an operator is *"escalate to credit committee any application stuck in HITL > 4h"* or *"draft the RBI inspection pack for the last quarter's secured-retail declines"* — a Compose surface that creates an ad-hoc agent invocation within the current ProcessSkill scope. Existing mockups show structured cards but no compose-style command. Add one.
2. **Plain-English drill-down on any agent step.** Click any READ/RAN/WROTE line; the right panel shows ProcessMaster-rendered prose explaining what the agent did in business language. Cowork-SMB has this implicitly because the agent narrates its actions; Yantra needs it explicitly for regulator-grade auditability.
3. **Live Artifacts as a first-class view.** Anthropic's Live Artifacts (Cowork, Apr 2026) are dashboards bound to live data sources that refresh on open. The CredMitra "Dream Summary" and "Lane Health" panels are *very close* to Live Artifacts in spirit but presented as cards rather than as the bound, refreshing artefact pattern. Promote them. NPA-EWS, collections daily board, fraud-cluster watcher should live here.
4. **Role switcher in the header.** Priya K is a senior underwriter. The same UI accessed by Vivek M (compliance officer), Rohan S (CRO), or an external RBI inspector should show *different* facets of the same workflow — same data, different lens. Cowork-SMB is single-user; Yantra is multi-role within one tenant. Make role explicit, with named permission boundaries.
5. **An author-handoff button.** When an operator sees a recurring pain point — "this DMN rule keeps tripping HITL because of an edge case the policy didn't anticipate" — there should be a one-click *"propose policy refinement"* button that opens YantraAuthor on the relevant ProcessSkill with the offending case pre-loaded for the senior author to review. The operator-to-author feedback loop is what makes Dreams actionable.

### 3.4 The unified harness UI — a synthesis recommendation

Given the four mockups are alternatives, not necessarily one-of-each-for-a-role, my recommendation is to **make Pipeline (option 3) the default operator view, Channel (option 1) the comms-and-collaboration view, and Terminal (option 2) a power-user toggle**. The 5-mode ProcessSkill view (`process-repo.html`) is the *meta* layer — accessible from any view by clicking a ProcessSkill name. Five views, one layout grammar, one composition:

```
┌────────────────────────────────────────────────────────────────────────┐
│  Yantra · CredMitra            [pipeline ▼] [👤 Priya K · sr uw]  ⚙   │  ← header (view switcher + role)
├──────────┬─────────────────────────────────────────────┬────────────────┤
│ Channels │  Live Workstream  (current view)            │  Right Panel   │
│ - origin │  ──────────────────────────────────────     │  - Memory      │
│   ation  │  [Compose: "escalate stalled..."]   ⏎       │  - Live        │
│ - kyc    │                                              │    Artifacts   │
│ - fraud  │  app_4711  P2  ⚠  ProcessSkill: msme-uw-v3 │  - Dreams      │
│ - colls  │   └ orchestrator-α  ●●●○○○                   │  - Health      │
│          │   └ uw-agent       ●●●●●○ READ ... RAN dmn  │                │
│ Policies │   └ fraud-agent    ●●○ READ ... amber       │  Lifecycle:    │
│ - credit │     [Propose policy refinement →]           │  Execute       │
│ - kyc    │                                              │  Audit         │
│ - fpc    │  app_4712  P3  ✓                            │  Test          │
│          │   └ summary: approved · ₹22L                │  Support       │
│ Discover │   └ trace · audit pack · explain (en/hi)    │  Train         │
├──────────┴─────────────────────────────────────────────┴────────────────┤
│ P50 3m41s · P95 11m02s · auto 71% · HITL q 14 · ₹2.41L/day · 3 models  │  ← sticky footer
└────────────────────────────────────────────────────────────────────────┘
```

Existing mockups give you 90% of this; the remaining 10% is the Compose box, role switcher, audit-pack-on-card shortcut, language toggle (English/Hindi for customer-facing artefacts), and Lifecycle-mode tabs on the right panel.

---

## 4. Emergent + Wingman — The Second Reference Point

### 4.1 What Emergent is, in one paragraph

**Emergent Labs** (Bengaluru, co-founded by Mukund Jha — Dunzo co-founder) is an India-headquartered vibe-coding platform. It lets non-coders and developers build *full-stack web and mobile applications* by describing them in natural language. The platform runs a hybrid stack — its own fine-tuned models for engineering specialisations, plus Claude / GPT / Gemini for general reasoning. It has raised ~$100M total (latest: $70M Series B from Khosla Ventures + SoftBank Vision Fund 2). Mukund's framing: *"making it easy for entrepreneurs to build AI-native applications, then operate them."*

In April 2026, Emergent launched **Wingman** — a *messaging-first autonomous agent* embedded in WhatsApp, Telegram, and iMessage. Wingman connects to Gmail, Outlook, Google Calendar, Slack, CRMs, GitHub via Emergent's integration hub, executes routine tasks autonomously, and asks for approval on consequential ones via "trust boundaries." Mukund's quote: *"The obvious next step for us was, can we help them not just build the software, but actually operate more autonomously through it?"*

### 4.2 Two threads from the Anthropic conversation, weighed for Yantra

#### Thread A — "Emergent makes it easier to build AI-native applications. Does this help us build Yantra?"

Honest answer: **mostly no, with one specific exception.**

Emergent's platform is purpose-built for one shape of output — a deployable web/mobile application with a CRUD-style schema and conventional UI patterns. Yantra is a different shape of output: a regulated, multi-tenant, deterministically-orchestrated agentic platform with sovereign hosting, RBI-grade audit, and a multi-notation configuration format. The technical primitives Emergent optimises for (rapid scaffolding, opinionated stack choices, hosted deployment, design-via-prompt) do not map onto what Yantra needs (regulated runtime, deterministic engines, multi-LLM router, India DPI fleet, foundation-governed standard).

The **one specific exception**: Emergent is a credible reference for what *ProcessMaster's authoring loop should feel like*. Emergent has put enormous effort into the conversational-build UX — the moment where a non-engineer describes intent and gets a working artefact back. That UX work, the cadence of clarification questions, the live preview pattern, the "see-it-build-itself" feedback — is directly applicable. **Yantra should hire someone who has run Emergent's UX, even if Yantra builds nothing on Emergent's platform.**

Stronger framing: ProcessMaster is the *equivalent of Emergent's vibe-coding loop, but for business processes instead of full-stack applications*. The output looks completely different (a ProcessSkill folder, not a Next.js repo). The UX of getting there should look very similar.

#### Thread B — "Wingman is a mini-ERP for small business. Is it relevant?"

More relevant, but on the *opposite end* of Yantra's market. Wingman is what an SMB owner gets; the **harness pattern** that Wingman represents is what Yantra ports to large institutions.

What Wingman gets right that Yantra should adopt directly:

1. **Trust boundaries as a declared, named UI concept.** Wingman explicitly distinguishes "routine tasks the agent does autonomously" from "consequential tasks that require user approval." This maps 1:1 to Yantra's HITL posture per ProcessSkill shape (Decisional / Review / Reconciliation / Generative / Retrieval / Orchestration). The CredMitra mockups already gate at HITL boundaries, but the *language* could be sharpened: every Yantra ProcessSkill should declare in its manifest a `trust-boundary:` field that the UI surfaces explicitly to the operator. *"This agent will not draft a sanction letter without your approval. It will, however, pull bureau data autonomously."* That clarity builds operator trust faster than abstract gating.

2. **Messaging-first integration.** Wingman lives in WhatsApp, Telegram, iMessage — where SMBs already operate. Yantra's analogue: a *Yantra-on-Teams* and *Yantra-on-WhatsApp-Business* surface for specific role-actions. The credit-committee approver doesn't need to log into the Yantra web cockpit at 11pm to approve a marginal case; a WhatsApp Business message with a "Approve / Decline / Send back" trio, properly signed, replicates the moment without forcing a context switch. This is plausibly Phase 2 work for the harness UI; the mockups don't show it yet.

3. **Integration hub as a first-class product surface.** Emergent ships Wingman with a hub of pre-built integrations. Yantra ships with YantraGateway and the Tier-A/B MCP fleet — same idea, larger scope. The user-facing exposure of this hub matters: a customer should be able to browse "what can Yantra connect to" the way they browse a Wingman integration page. The CredMitra mockup currently shows ProcessConnectors (CIBIL, AA, CKYC, Finacle, UPI/eNACH) as a sidebar link; promote it to a full-page browsable directory à la Anthropic's MCP server marketplace or Emergent's integration hub.

What Wingman gets *wrong* for Yantra's customer:

1. **Single-user mental model.** Wingman is a personal AI assistant. A bank is a multi-role, multi-team, multi-policy environment. Yantra cannot inherit Wingman's owner-centric assumptions.
2. **No deterministic spine.** Wingman is LLM-driven end-to-end. That's fine for "remind Sarah about the meeting." It is not fine for "decide eligibility for this loan." Yantra's deterministic-where-enumerable principle is non-negotiable and is *not* what Wingman models.
3. **No regulator angle.** Wingman has no inspection-pack-builder, no version-pinned audit trail, no signed reviewers. Yantra's harness is built around those.

### 4.3 The strategic read on Mukund's positioning

Mukund's framing — *"build the software, then operate more autonomously through it"* — is exactly the trajectory Yantra is on, applied to a different customer class.

- Phase 1 (Emergent's vibe coding) = "let entrepreneurs build AI-native apps without writing code." Yantra equivalent = "let senior policy authors build agentic operations without writing DMN/BPMN." Same conceptual move, different output.
- Phase 2 (Wingman) = "let the agent operate the business on the founder's behalf." Yantra equivalent = "let the agent run the operational workflow with operator oversight." Already where Yantra is heading.

Two takeaways:

1. **The two-product structure Yantra has settled on (authoring + harness) is the *right* structure** — independently arrived at by Emergent, validated by Anthropic's own Cowork-SMB ship. This is unusual confirmation that the conceptual decomposition is robust.
2. **Yantra's defensibility is "for large regulated FS"**, not "for everyone." Wingman is going to capture the SMB / solopreneur end of the market; Cowork-SMB is going to capture US small business; Yantra captures Indian financial services. Three platforms, three customer classes, same underlying pattern. The boundary lines are clean.

### 4.4 Hypothetical: Could Yantra partner with Emergent?

Worth considering, even if the answer is "not now." Two angles:

- **For ProcessMaster's authoring UX**: an Emergent partnership where Emergent's UX expertise informs (but does not own) ProcessMaster's authoring loop. Plausible value; complicated equity dynamics.
- **For SMB lending**: if Yantra ever wants an SMB-lending wedge (white-labeled Yantra for the small NBFCs the top-50 won't onboard for years), partnering with Emergent's distribution (millions of small businesses using their builder) could provide reach. Specifically, *Emergent customers who are also small lenders* are a real segment. Probably year-3+.

For now: keep Emergent in view; do not anchor on them.

---

## 5. What This Means for the Yantra Harness UI — Concrete Recommendations

Pulling the two reference points together into actionable items for the harness UX:

### 5.1 Adopt from Cowork-SMB

- Promote **Compose** to a first-class affordance in every channel/lane view — an "ask the agent" box that scopes to the current ProcessSkill
- Promote **Live Artifacts** to a named right-panel section — bind dashboards (NPA-EWS, collections, lane health) to live data; refresh on open
- Apply the **Workshops-as-onboarding** pattern to Yantra Discover and author elevation — productised in-person + virtual trainings, not just docs
- Carry the **5-tab lifecycle view** on every ProcessSkill (Execute / Audit / Test / Support / Train) — already in the mockup; keep it

### 5.2 Adopt from Wingman / Emergent

- Make **`trust-boundary:`** an explicit field in the ProcessSkill manifest and a visible badge on every operator card — *"this agent acts autonomously up to here; beyond here, it asks"*
- Build a **Yantra-on-Teams** + **Yantra-on-WhatsApp-Business** surface for role-actions (approvals, escalations, on-call) — defer to Phase 2 but architect for it from Phase 1
- Treat the **YantraGateway connector directory** as a marketed surface — public-facing page that customers and partners can browse, mirror Anthropic's MCP marketplace pattern
- Borrow **Emergent's conversational build cadence** as ProcessMaster's reference UX — clarification rhythm, live preview, see-it-build pattern; not as architecture, as polish

### 5.3 Keep Yantra-specific (do not converge)

- **Multi-role UI** with named permission boundaries — Cowork-SMB does not have this
- **Deterministic engine visibility** — DMN rule that fired, Camunda activity that ran, helper function that computed — surfaced inline, not buried
- **Audit Pack one-click** on every card — not buried in an audit screen
- **Multi-LLM model badge** — "this step ran on Claude-Opus-on-Bedrock-Mumbai" — visible per step, builds operator trust over time
- **Bilingual customer-artefact rendering** — every customer-facing template renders in English and the customer's preferred language side by side for operator review
- **Inspection-pack-builder** as a single accessible button — not a separate workflow

---

## 6. Where the UI Mockup Branch Goes Next — A Suggestion

The existing `claude/agent-friendly-fintech-ui-1WF91` branch is an excellent starting point. The next iteration should converge on one primary view (pipeline / kanban) with the other paradigms as toggleable, and add the five gaps from §3.3:

1. Compose box at the top of each lane
2. Plain-English drill-down on agent trace lines
3. Live Artifacts panel (right rail, replacing static Dream / Health cards)
4. Role switcher in the header
5. Author-handoff button on every operator card

Branch suggestion for the next pass: `claude/yantra-harness-ui-v2-unified` — single converged operator UI, with the 5-tab ProcessSkill view from `process-repo.html` linked as a deep dive.

Separately, **YantraAuthor's UI is not yet mocked** (the existing branch is the operator/runtime cockpit, not the authoring workbench). The three-panel ProcessMaster authoring loop described in Chapter 3.8 of the platform spec is the next UI to mock — Prompt + Preview + Canvas. That work is materially different in feel from the operator cockpit and should be a separate branch / repo subtree.

---

## 7. Open Questions

1. **Which view to make default?** Recommendation above is Pipeline (option 3). Worth validating with one underwriter and one credit head before committing.
2. **Compose-vs-structured-card balance.** SMB owners benefit from Compose because their work is unstructured. Bank underwriters work in structured cards 95% of the time and need Compose for the 5% edge cases. Is that ratio right? Test in Discover engagement.
3. **Native mobile vs WhatsApp-first for the messaging surface.** Wingman bet on WhatsApp; a bank's compliance officer might not want approvals on personal WhatsApp. Teams + a Yantra mobile app may be the right cut. Decide after talking to one customer's compliance head.
4. **Emergent for ProcessMaster UX inspiration — direct engagement?** A 1-hour conversation with Mukund or his head of UX would compress months of design iteration. Worth requesting via Anthropic's network if accessible.
5. **The branch name.** The current branch is `agent-friendly-fintech-ui` — useful for the spike, but the convergence branch should reflect the platform identity: `yantra-harness-cockpit-v1` or similar.

---

## 8. Sources

**Claude for Small Business**
- [Introducing Claude for Small Business — Anthropic](https://www.anthropic.com/news/claude-for-small-business)
- [Anthropic courts a new kind of customer: small business owners — TechCrunch](https://techcrunch.com/2026/05/13/anthropic-courts-a-new-kind-of-customer-small-business-owners/)
- [Anthropic offers new Claude Code tools for small businesses — Axios](https://www.axios.com/2026/05/13/anthropic-claude-small-business-smb)
- [Anthropic debuts Claude for Small Business — Yahoo Finance](https://finance.yahoo.com/news/anthropic-debuts-claude-for-small-business-as-it-continues-its-enterprise-software-push-160500355.html)
- [Anthropic launches Claude for Small Business with new automation workflows — SiliconANGLE](https://siliconangle.com/2026/05/13/anthropic-launches-claude-small-business-new-automation-workflows/)

**Emergent + Wingman + Mukund Jha**
- [Emergent Launches Wingman, an Autonomous AI Agent — Emergent](https://emergent.sh/news/emergent-launches-wingman-autonomous-ai-agent)
- [India's vibe-coding startup Emergent enters OpenClaw-like AI agent space — TechCrunch](https://techcrunch.com/2026/04/15/indias-vibe-coding-startup-emergent-enters-openclaw-like-ai-agent-space/)
- [Emergent launches Wingman: a personal AI agent for everyone — SiliconANGLE](https://siliconangle.com/2026/04/15/emergent-launches-wingman-personal-ai-agent-everyone/)
- [Emergent Labs AI | NDTV Ind.AI Summit: Mukund Jha — YouTube](https://www.youtube.com/watch?v=GwFSpey6Fvo)
- [Dunzo co-founder's vibe-coding startup Emergent raises Series B — YourStory](https://yourstory.com/2026/01/dunzo-co-founder-vibe-coding-startup-emergent-series-b-khosla-ventures-softbank-vision)
- [Mukund Jha of Emergent: The CEO Redefining Who Gets To Build Software — Founders Magazine](https://www.thefoundersmagazine.com/leadership/mukund-jha-of-emergent-the-ceo-redefining-who-gets-to-build-software/)
- [Wingman by Emergent: A New Step in Practical AI | Vibecon ft. Mukund Jha — YouTube](https://www.youtube.com/shorts/jPJG4zFx7cg)

**Existing Yantra UI mockups (referenced)**
- [github.com/gauravomsharma2024/Yantra (branch `claude/agent-friendly-fintech-ui-1WF91`)](https://github.com/gauravomsharma2024/Yantra/tree/claude/agent-friendly-fintech-ui-1WF91)
