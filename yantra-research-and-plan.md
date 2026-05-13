# Yantra (ProcessSkills) — Research & Project Plan

Anthropic's enterprise adoption playbook, decoded for an Indian-NBFC-focused, productized-services play.

> Working doc, May 2026. Sources cited inline; gaps flagged with `[GAP]`.

---

## 1. Executive Summary

Anthropic has, between Feb–May 2026, made four moves that, taken together, define a new enterprise-AI go-to-market pattern:

1. **Vertical plugin packs** — fully open-source repos (`anthropics/financial-services`, `anthropics/claude-for-legal`) that bundle Skills + MCP connectors + named agents + Managed-Agent cookbooks for an industry. No new SaaS; the IP is in the **prompts, skills, and the connector graph**.
2. **A platform shift in Claude itself** — Memory, Dreaming, Outcomes, Multiagent Orchestration (Code w/ Claude SF, May 6, 2026) and Live Artifacts (Cowork, Apr 20, 2026) turn Claude from a session tool into a long-lived, self-improving agent runtime.
3. **Distribution where work happens** — full Microsoft 365 integration (Word/Excel/PowerPoint/Outlook) carries context across apps; Cowork becomes the "control plane."
4. **A $1.5B "AI-native services" JV** with Blackstone, Hellman & Friedman, Goldman Sachs (and GIC, Apollo, GA, Leonard Green, Sequoia) — Palantir-style forward-deployed engineering, embedded inside PE portfolio companies to rebuild workflows around agents.

The clear template for **Yantra** is: do (1) and (4) for Indian NBFCs (MSME + secured retail), riding on (2) and (3) as the runtime — sell **ProcessSkills** (productized skill packs) bundled with thin forward-deployed delivery, *not* a new SaaS.

---

## 2. What Anthropic Is Actually Building (Construct, Not Marketing)

### 2.1 The plugin pack — anatomy

From `github.com/anthropics/financial-services` and `github.com/anthropics/claude-for-legal`:

```
<plugin-pack-repo>/
├── plugins/
│   ├── agent-plugins/        # Named, self-contained agents (one workflow each)
│   ├── vertical-plugins/     # Skill + slash-command bundles per sub-vertical
│   └── partner-built/        # Vendor-authored plugins (LSEG, S&P, CoCounsel, Solve)
├── managed-agent-cookbooks/  # Headless deployments (agent.yaml + subagents)
├── claude-for-msft-365-install/
└── scripts/
    ├── deploy-managed-agent.sh    # POSTs to /v1/agents
    ├── orchestrate.py             # handoff_request event router
    ├── validate.py / lint-tool-scope.py
    └── sync-agent-skills.py
```

Every plugin folder is the **same shape**:

```
<plugin>/
  .claude-plugin/plugin.json
  CLAUDE.md                  # "practice profile" — house style, jurisdictions, playbooks
  skills/<skill>/SKILL.md    # the actual IP — step-by-step methods + conventions
  agents/                    # scheduled / event-driven workflows
  hooks/                     # pre/post-tool hooks (guardrails)
  .mcp.json                  # MCP connector wiring (shared in core verticals)
```

Key design choices to copy verbatim for Yantra:
- **No build step.** Pure markdown + JSON. The Skills *are* the product.
- **Practice-profile-driven.** A mandatory `/cold-start-interview` slash command learns the firm's playbook, templates, house style, and jurisdictions on day 1.
- **Self-contained agent plugins.** Each agent bundles a *synced copy* of the Skills it needs from the vertical bundle (via `scripts/sync-agent-skills.py`). Installing one agent = one install.
- **Conservative gates.** Privilege, jurisdiction, "send/file/rely" flags are explicit hooks — not vibes.
- **Four deployment surfaces, one repo.** Cowork (UI), Claude Code (CLI), Managed Agents (headless API), Microsoft 365 (sidebar). Same Skill, four surfaces.

### 2.2 Financial Services pack — what it actually contains

**Agent plugins (11):** Pitch Agent, Meeting Prep, Market Researcher, Earnings Reviewer, Model Builder (live in Excel), Valuation Reviewer, GL Reconciler, Month-End Closer, Statement Auditor, KYC Screener.

**Vertical plugins (7):** financial-analysis (core), investment-banking, equity-research, private-equity, wealth-management, fund-admin, operations.

**Partner-built (2):** LSEG, S&P Global.

**MCP connectors (in `.mcp.json` of core):** Daloopa, Morningstar, S&P Global, FactSet, Moody's, MT Newswires, LSEG, PitchBook, Chronograph, Egnyte. Moody's is embedded as a *native app* covering 600M+ companies.

The IP density is in **finance-analysis** (Comps, DCF, LBO, 3-statement, deck QC, Excel audit) — a single core vertical that all the others import from.

### 2.3 Legal pack — what it actually contains

**12 practice-area plugins:** commercial-legal, corporate-legal, employment-legal, privacy-legal, product-legal, regulatory-legal, ai-governance-legal, ip-legal, litigation-legal, law-student, legal-clinic, legal-builder-hub.

**85+ named agents** across them (e.g. Vendor Agreement Reviewer, Tabular Diligence Review, DSAR Responder, AI Impact Assessor, Claim Chart Builder, Privilege Log Reviewer).

**~20 MCP connectors:** CoCounsel/Westlaw, CourtListener, Descrybe, Trellis, Solve Intelligence, Ironclad, DocuSign, iManage, Everlaw, Definely, Lawve, Courtroom5, TopCounsel, Aurora, Slack, GDrive, Box, Linear, Jira, Asana.

**5 Managed-Agent cookbooks** (always-on): diligence-grid, docket-watcher, launch-radar, reg-monitor, renewal-watcher.

What's worth noting: Anthropic ships not just one *plugin* per industry but a **plugin marketplace** for the industry (`.claude-plugin/marketplace.json`) — an ecosystem play.

### 2.4 The platform features (Code w/ Claude SF, May 6, 2026)

| Feature | Status | What it really is |
|---|---|---|
| **Memory** | Public beta | Persistent, structured agent memory (files-on-disk model) read across sessions |
| **Dreaming** | Research preview | Scheduled job that replays past agent sessions, extracts patterns, *rewrites* memory entries — recurring mistakes, converged workflows, team preferences. Hippocampal consolidation analogy is Anthropic's own |
| **Outcomes** | Public beta | Rubric-graded self-loop: a separate evaluator (its own context window) scores against a written rubric, points at what to fix, agent retries |
| **Multiagent orchestration** | Public beta | A lead agent delegates to specialists with their own model/prompt/tools; they share a filesystem; results stream back to lead context |
| **Opus 4.7** | GA | Auto-mode for several hours; strong visual-design taste; default in planning mode |
| **Live Artifacts** (Cowork, Apr 20) | GA on paid plans | Artifacts that stay bound to data sources and refresh on open — replaces static dashboards/trackers |

**Why this matters for an AI-native NBFC app:** these four features together let a single agentic system *replace* what used to need an LOS + LMS + BRE + BI stack at the workflow level. Memory + Dreaming = the system gets better at *your* portfolio over time. Outcomes = you can deploy without a human-in-the-loop bottleneck for a much wider class of decisions. Multiagent = underwriting, fraud, valuation, and policy-check can run as parallel specialists. Live Artifacts = the LOS/collections dashboard is no longer a separate SaaS — it's a Claude artifact.

### 2.5 The JV — Anthropic + Blackstone + Hellman & Friedman + Goldman Sachs

(Announced May 4, 2026, still unnamed `[GAP: name not public]`)

- **$1.5B.** Anthropic, Blackstone, H&F: $300M each. Goldman: $150M. Apollo, General Atlantic, Leonard Green, GIC, Sequoia also in.
- **Operating model:** standalone entity, **Anthropic engineers embedded inside** (Palantir forward-deployment). Combines advisory + implementation in one P&L.
- **Built-in pipeline:** hundreds of PE portfolio companies across the investors' books.
- **Target:** mid-sized firms whose talent bottleneck is the cost/scarcity of consultants who can both redesign a workflow and ship code. The wedge is *workflow redesign around agents*, not chatbot integration.
- **Implicit thesis:** Claude + Skills as IP > traditional consulting hours.

---

## 3. Indian-NBFC Equivalent — MCP Map

### 3.1 What "MCPs for Indian Financial Services" should be

Anthropic's FS pack hardwires 10 connectors that are the gravity wells of US financial data. The Indian equivalent set is well-defined — it's the **Digital Public Infrastructure (DPI) stack** plus a thin commercial layer on top. None of these exist as official MCP servers today `[GAP — verify community MCP servers as of May 2026]`; building/curating them is itself differentiated IP for Yantra.

**Tier A — DPI primitives (foundational, regulated):**

| Capability | Underlying system | Why it matters for NBFC | MCP-build difficulty |
|---|---|---|---|
| Identity / eKYC | Aadhaar (UIDAI), DigiLocker, CKYCR | Customer onboarding, V-CIP, CKYC fetch/push | Medium — auth + regulated |
| Bank-statement / income | **Account Aggregator** (Sahamati, Finvu/OneMoney/CAMSFinServ/NADL) | Consent-based bank, GST, ITR, MF, insurance, demat data — replaces statement upload | High — consent flow, FIU registration |
| Tax / business filings | **GSTN** (via GSPs like Karza/Perfios/ClearTax) | MSME underwriting (GSTR-1/3B, e-invoices, returns) | Medium — GSP partnership |
| Credit bureau | CIBIL TransUnion, Experian India, Equifax India, CRIF Highmark | Bureau pulls — consumer + commercial | Medium — bureau membership |
| e-Sign / NACH / eMandate | NSDL/CDSL e-sign, NPCI NACH, UPI AutoPay | Loan agreement execution, repayment setup | Low–Medium |
| Property records | IGRS state portals (Maharashtra, Karnataka, TN…), CERSAI | LAP underwriting, charge creation | High — fragmented per state |
| Vehicle records | VAHAN, Parivahan | Vehicle-loan KYC, hypothecation, RC verification | Medium |
| Court / litigation | eCourts, NCLT, IBBI | Borrower litigation check, default monitoring | Medium |
| Aadhaar masking / OCR / face match | Hyperverge, Signzy, IDfy, Karza, Setu | KYC compliance (Aadhaar Act, RBI) | Low — wrap existing APIs |
| Regulatory | RBI press releases, Master Directions, SACHET, FIU-IND | Reg-change monitoring, AML/CFT, suspicious-transaction reporting | Low — scrape-able |

**Tier B — Commercial data (analogs to Daloopa/Moody's/PitchBook):**

| Capability | Indian analog |
|---|---|
| Company financials & MCA filings | MCA21, Tofler, Probe42, Tracxn, AltInfo, Zauba Corp |
| MSME data & GST analytics | Karza, Perfios, FinBox, Scoreme, Bureau (the company), Lentra-affiliated |
| Credit risk / fraud | CRIF Bureau scores, Bureau (bureau.id), Hyperverge fraud, Bridge Fintech |
| Collateral valuation | gold rate feeds (IBJA), used-car (CarTrade, Cars24, IMV vendors), property (PropEquity, Liases Foras, MagicBricks) |
| Account/payments rails | NPCI APIs (UPI, NACH, AePS, BBPS, IMPS, RuPay), RTGS/NEFT |
| Co-lending / DLG | Yubi (CredAvenue), Lendingkart-co-lend, OfBusiness, FACE |
| Communications | WhatsApp Business API (Gupshup, AiSensy), Exotel/Knowlarity (voice), Karix/MSG91 (SMS) |

**Tier C — Internal NBFC systems (the connectors that win deals):**

LOS (Lentra, Pennant, Nucleus FinnOne, Newgen, Cloud-Lending/Q2, in-house) · LMS (same vendors) · core banking (TCS BaNCS / Finacle / Flexcube) · BRE (FICO, Lentra DecisionMatrix, in-house) · collections (CredGenics, Spocto, Creditas Solutions) · communications (Exotel/Ozonetel/MyOperator) · CRM (Salesforce FSC, LeadSquared, in-house). For Yantra these are the **per-customer** MCP wrappers — like Ironclad/iManage in the legal pack.

### 3.2 NBFC plugin design — analogous to the Legal pack

Take the Legal pack's "12 practice-area plugins, one core vertical, managed-agent cookbooks for always-on jobs" pattern and apply it to Indian NBFC lending.

**Proposed structure: `yantra/nbfc-india/`**

```
yantra/nbfc-india/
├── plugins/
│   ├── vertical-plugins/
│   │   ├── credit-core/                    # the financial-analysis equivalent
│   │   │   └── skills/
│   │   │       ├── bsa-aa/                 # bank statement analysis via AA
│   │   │       ├── gst-analytics/          # GSTR-1/3B/e-invoice cash-flow
│   │   │       ├── bureau-read/            # CIBIL/Experian/CRIF interpretation
│   │   │       ├── policy-check/           # RBI Master Directions hits
│   │   │       ├── ftnpa-ecl/              # IRACP, ECL computation
│   │   │       ├── fraud-triangulation/    # device, KYC, bureau, AA cross-check
│   │   │       └── customer-fit-memo/      # underwriter memo writeup
│   │   ├── msme-lending/                   # working cap, secured/unsecured MSME
│   │   ├── secured-gold/
│   │   ├── secured-vehicle/                # new, used, CV, 2W
│   │   ├── secured-lap/                    # property as collateral
│   │   ├── co-lending-fldg/                # partnership economics, DLG
│   │   └── collections-recovery/
│   ├── agent-plugins/
│   │   ├── kyc-vcip-screener/              # Aadhaar/DigiLocker/face-match/PEP
│   │   ├── aa-underwriter/                 # pulls AA consent, runs BSA + bureau + GST
│   │   ├── gst-cashflow-underwriter/       # MSME-specific
│   │   ├── gold-valuer/                    # IBJA rate, purity-tagged loan-to-value
│   │   ├── vehicle-valuer/                 # VAHAN + IMV + market band
│   │   ├── property-diligence/             # IGRS + title + valuation + charge
│   │   ├── credit-memo-writer/             # final memo, OD/limit recommendation
│   │   ├── collections-strategist/         # bucket-wise strategy, channel mix
│   │   ├── nach-emandate-orchestrator/
│   │   ├── reg-change-watcher/             # RBI/SEBI/FIU-IND/state stamp duty
│   │   └── co-lending-reconciler/          # partner-bank reconciliation, FLDG
│   └── partner-built/                      # Karza, Perfios, Hyperverge, CredGenics
├── managed-agent-cookbooks/                # always-on
│   ├── reg-monitor/                        # RBI circulars → policy diff
│   ├── npa-early-warning/                  # AA + bureau + repayment signals
│   ├── collections-router/                 # bucket triage, dial / WA / field
│   ├── fraud-cluster-watcher/              # device, KYC, branch clusters
│   └── audit-trail-generator/              # for RBI inspections
└── scripts/  (same pattern as Anthropic)
```

**Customer-facing slash commands** (illustrative): `/aa-underwriter:run`, `/credit-memo-writer:draft`, `/property-diligence:title-check`, `/reg-monitor:since YYYY-MM-DD`, `/collections-strategist:bucket-plan`.

**Always-on cookbooks** are deployed via Managed Agents and are the recurring-revenue surface — pricing per-loan or per-portfolio.

**MSME-specific notes (priority segment 1):**
- The killer skill is *cash-flow underwriting* — `bsa-aa` + `gst-analytics` + `bureau-read` triangulated. This is the wedge where AA + Claude beats template-based scorecards.
- E-invoice + GSTR matching detects revenue inflation tricks that scorecards miss.
- Customer-stickiness is high once `policy-check` and `credit-memo-writer` are tuned to the NBFC's own playbook (their `CLAUDE.md`).

**Secured retail notes (priority segment 2):**
- Gold loan: low underwriting complexity, high ops complexity → automate **branch ops** (purity, weight, photo-evidence, top-up, auction) more than underwriting. Live Artifacts as a "branch dashboard" is the right surface.
- Vehicle: VAHAN + IMV + insurance + RC verification is a sequenced workflow → ideal multiagent orchestration (parallel specialists per source).
- LAP: title + valuation + charge are the long pole; jurisdiction-aware (state stamp duty, IGRS variance) — mirrors the Legal pack's "jurisdiction-aware" employment-legal pattern almost exactly.

### 3.3 Compliance gates — Indian regulatory hooks

The Legal pack uses *pre/post-tool hooks* for privilege and jurisdiction. The NBFC equivalents:

- `consent-gate.py` — block any data access without a valid AA consent token or borrower e-sign
- `kyc-tier-gate.py` — enforce CKYC tier (Aadhaar OTP / OVD / V-CIP) thresholds for loan amount
- `aadhaar-mask-gate.py` — strip Aadhaar numbers before any tool returns content to user
- `dpdp-gate.py` — DPDP Act 2023: purpose-limit, retention-limit, data-fiduciary obligations
- `rbi-fpc-gate.py` — Fair Practices Code: cooling-off, language-of-borrower, recovery-agent conduct
- `digital-lending-gate.py` — RBI Digital Lending Guidelines (Sep 2022 + updates): LSP disclosure, KFS, cooling-off
- `audit-trail-hook.py` — every agent action goes into an immutable log for RBI inspection (mirrors `corporate-legal` audit pattern)

---

## 4. Implications of New Claude Features for an AI-Native NBFC App

Translating Memory/Dreaming/Outcomes/Multiagent/Live Artifacts into concrete NBFC app design choices:

### 4.1 Memory + Dreaming → underwriting that learns *your* portfolio

- **What changes:** today a credit policy lives in a BRE config. With persistent memory + dreaming, the *agent* maintains a versioned, evolving understanding of "what loans went bad and why" specific to this NBFC's portfolio, geography, vintages.
- **Concrete design:** every closed loan (default *or* repaid) becomes a memory entry. A weekly Dream job re-reads them, surfaces patterns ("`auto-debit-bounce + low GSTR-1 m/m + < 2km distance from PIN with high concentration of similar defaults` → 80% NPA"), and writes new memory entries the credit-memo agent reads next time.
- **Why it's defensible:** the memory store is the IP. After 12 months it cannot be reproduced by a competitor with the same Skills.
- **Risk:** the agent can drift into **proxy discrimination**. Need an outcomes rubric for fairness + RBI Fair Practices alongside the credit-quality rubric.

### 4.2 Outcomes → underwriter productivity, not headcount cut

- **Right rubric design** (per loan): (a) bureau-fact-correctness, (b) AA-data-traceability, (c) policy-hit-completeness, (d) memo-recommendation-justified-by-evidence, (e) borrower-explainability ≥ Class-X reading level, (f) RBI-FPC-compliance.
- The credit officer becomes a **reviewer + edge-case escalation** — the agent self-loops until rubric passes.
- Throughput: 3–5× per officer is realistic; the binding constraint moves to AA consent latency and customer responsiveness, not to underwriter time.

### 4.3 Multiagent orchestration → underwriting is naturally parallel

- Lead agent = the credit-memo writer.
- Subagents in parallel: bureau-puller, AA-analyser, GST-analyser, fraud-triangulator, property/vehicle-valuer, policy-checker, RBI-reg-checker, collections-history-checker.
- Shared filesystem holds the borrower-case folder; specialists drop structured findings; lead composes the memo.
- This matches the Anthropic example (deploy history / error logs / metrics / support tickets) almost 1:1 — the NBFC analog is bureau / AA / GST / property.

### 4.4 Live Artifacts → kill the LOS/LMS dashboard

- Branch operations dashboard for gold loan, NPA early-warning tracker, collections daily board, co-lending P&L reconciliation — all become live artifacts in Cowork, data-bound to the underlying tools via MCP.
- This is the right answer to "do we build a frontend?" — for internal operators, **don't**. For borrower-facing flows, you still need a custom mobile/web app, but the *internal control plane* is Cowork + Live Artifacts.

### 4.5 Opus 4.7 in auto-mode → batch & always-on jobs

- The Managed-Agent cookbooks pattern (reg-monitor, NPA-early-warning, fraud-cluster-watcher, collections-router) is the natural fit for Opus 4.7's multi-hour auto runs.
- Pricing implication: these are billable per-portfolio per-month, not per-loan.

### 4.6 What this means for the "AI-native lending NBFC" *application* architecture

```
┌── Borrower-facing (custom React Native / web — has to exist) ──┐
                                │
┌── Internal control plane ─────────────────────────────────────┐
│   Cowork + Live Artifacts (branch ops, EWS, collections, P&L) │
│   Claude Code for ops engineers + credit analysts             │
└────────────────────┬──────────────────────────────────────────┘
                     │
┌── Yantra plugin pack (yantra/nbfc-india) ────────────────────┐
│   vertical-plugins · agent-plugins · partner-built           │
│   .mcp.json wires to Tier A/B/C connectors                   │
│   compliance hooks (consent, KYC, DPDP, FPC, audit)          │
└────────────────────┬──────────────────────────────────────────┘
                     │
┌── Claude Managed Agents (headless) ──────────────────────────┐
│   Memory + Dreaming + Outcomes + Multiagent runtime          │
└────────────────────┬──────────────────────────────────────────┘
                     │
┌── MCP servers (Yantra-built + partner-built) ────────────────┐
│   AA · GSTN · Bureau · Karza · Perfios · CredGenics ·         │
│   IGRS · VAHAN · NACH · NPCI · WhatsApp · existing LOS/LMS    │
└──────────────────────────────────────────────────────────────┘
```

The *only* code we'd build that isn't Skills + MCP wrappers is the borrower-facing app and a thin event-router (if `orchestrate.py` proves insufficient).

---

## 5. Indian Equivalent of the Anthropic JV — Lean Services Company Model

### 5.1 What the Anthropic/Blackstone JV is actually doing

Three things, in order of importance:
1. **Workflow redesign** — re-architect a process so an agent can do most of it (the consulting bit).
2. **Forward-deployed engineering** — embed engineers to build/wire Skills, MCP connectors, hooks, rubrics inside the customer (the Palantir bit).
3. **Owning the underlying model** — captive AI cost + product feedback loop (the Anthropic bit).

What it is **not**:
- Not an SI / body-shop. Margin model is product, not T&M.
- Not a product company. It does *not* ship a SaaS.

### 5.2 Indian-NBFC version — design constraints

- **Capital efficiency.** The right team size for India is 8–20 people, not 200. Mid-sized NBFCs cannot absorb US-consulting prices.
- **Distribution partner advantage.** Anthropic has Blackstone/H&F's portfolios. The Indian analogs are PE/VC NBFC investors (Premji Invest, Multiples, Norwest, Lightrock, A91, Westbridge, Faering, ChrysCapital) and DFIs (SIDBI, NABARD via the new digital push) — pick one consortium partner with 4–8 NBFC portcos.
- **No JV needed.** Indian regulatory + capital structure makes a JV slow. A clean private-limited services company with a **strategic agreement** with one PE house (right-of-first-refusal on portcos for 24 months in exchange for portfolio-wide discount) achieves 80% of the JV value.
- **Captive model?** No — use Anthropic + Bedrock; keep optionality. The differentiation is *Skills + MCP IP*, not weights.
- **Sister-IP company.** Keep `yantra-skills` (the plugin pack repo) as an Apache-2.0 open-source project owned by a separate IP-holding entity that licenses to the services co. Mirrors how Anthropic split the open repo from the JV.

### 5.3 Proposed structure for Yantra Services Pvt Ltd

```
Yantra Skills IP (Section 8 / LLP)         Yantra Services Pvt Ltd (operating)
─────────────────────────                  ─────────────────────────
- owns yantra/nbfc-india repo              - 8–12 forward-deployed engineers
- Apache-2.0 license to public             - 2–3 credit/risk SMEs
- enterprise license to Yantra Services    - 1 RBI/compliance specialist
- governance: founder + 1 advisor          - delivery: 4–6 weeks per NBFC pilot
                                           - revenue: setup fee + per-loan / per-portfolio
                                           - margin: 60%+ once Skill library matures
```

### 5.4 Engagement template (the "Palantir 6-week wedge")

| Week | Deliverable |
|---|---|
| 0 | Mutual NDA + RBI/DPDP-ready data-handling MoU; pick one workflow (MSME unsecured underwriting or gold-loan branch ops) |
| 1 | Cold-start interview → `CLAUDE.md` for the NBFC; map LOS/LMS/BRE/comms stack; identify 5 MCPs needed |
| 2 | Build/wire MCPs; install `yantra/nbfc-india` plugins; instrument outcomes rubric drafted with credit head |
| 3 | Shadow-mode: agent runs alongside human on real cases; outcomes loop tuning |
| 4 | A/B mode: 20% of loans routed to agent-first; measure throughput, NPA proxy, override rate |
| 5 | Hand-off: NBFC ops + credit team trained on Cowork; ongoing managed-agent cookbooks live |
| 6 | Convert to recurring: per-loan fee for active workflows, per-portfolio for cookbooks, retainer for new-Skill additions |

A 12-person team can run 6–8 such engagements simultaneously after the first three (Skills get reused).

### 5.5 Pricing model

- **Setup:** ₹40–80L per NBFC depending on stack complexity (one-time, six-week wedge).
- **Per-loan:** ₹50–250 per disbursed loan that touched the agent flow (variable; aligns incentives with throughput).
- **Per-portfolio cookbooks:** ₹5–15L/month per always-on cookbook (reg-monitor, EWS, collections-router…).
- **New-Skill retainer:** ₹3–6L/month for ongoing playbook tuning + new workflows.

Target: 10 NBFC customers in Y2 → ~₹40–60Cr ARR at 60% margins. This is the lean, IP-rich path; not the 200-person SI path.

---

## 6. Yantra (ProcessSkills) — Phased Project Plan

### Phase 0 — Define the wedge (Weeks 1–4)

- [ ] Pick **one** of: MSME unsecured cash-flow underwriting *or* gold-loan branch ops. Pick the one where you have 1 named NBFC willing to be Pilot Customer #0.
- [ ] Write the `yantra-skills/nbfc-india/README.md` (mirror `anthropics/claude-for-legal/README.md` line-for-line; this forces clarity on the construct).
- [ ] Draft the `CLAUDE.md` practice-profile template — the cold-start interview script is the most valuable artifact in Phase 0.
- [ ] Decide IP-vs-services entity structure with a CA + lawyer (1 week).

### Phase 1 — Build the minimum viable plugin pack (Weeks 5–14)

- [ ] Stand up `credit-core` vertical with 4 Skills: `bsa-aa`, `gst-analytics`, `bureau-read`, `policy-check`. Each is a markdown SKILL.md + few-shot examples + structured-output schema.
- [ ] Build MCP wrappers for 5 connectors: one AA TSP (Finvu *or* OneMoney), one GSP (Karza *or* Perfios), one bureau (start with Experian — easiest commercial terms `[GAP: verify]`), CKYCR, DigiLocker.
- [ ] One agent plugin end-to-end: `aa-underwriter` for unsecured MSME *or* `gold-valuer` + branch-ops dashboard for gold-loan.
- [ ] Compliance hooks: consent-gate, aadhaar-mask-gate, dpdp-gate, audit-trail-hook.
- [ ] Outcomes rubric written with Pilot Customer #0's credit head.
- [ ] Internal smoke-test on 100 anonymized historical cases from Pilot.

### Phase 2 — Pilot deployment (Weeks 15–22)

- [ ] Run the 6-week wedge inside Pilot Customer #0.
- [ ] Measure: throughput per underwriter, override rate, time-to-decision, NPA-proxy at 30/60/90 DPD (will need months to mature — track leading indicators).
- [ ] Publish the IP repo publicly (Apache-2.0) — this is the *distribution* mechanism, not a giveaway. Anthropic's repos are public for the same reason: ecosystem gravity.
- [ ] Sign 1 PE consortium partner.

### Phase 3 — Scale to 3 NBFC customers (Months 7–12)

- [ ] Add `collections-recovery` vertical (CredGenics MCP).
- [ ] Add `reg-monitor` Managed-Agent cookbook (RBI/SEBI/FIU/state) — sellable as standalone product to NBFCs not yet ready for full underwriting.
- [ ] Add `secured-vehicle` or `secured-lap` vertical depending on customer pull.
- [ ] Hire: 2 more FDE, 1 risk SME, 1 RBI-compliance lead.

### Phase 4 — Productize the cookbook layer (Months 13–24)

- [ ] Each Managed-Agent cookbook becomes a per-portfolio monthly subscription with a quantified ROI per cookbook.
- [ ] Start a "Yantra Builder Hub" (mirror Anthropic's `legal-builder-hub`) — let NBFC in-house teams contribute Skills with a trust layer (security scan, allowlist, freshness). Ecosystem flywheel.
- [ ] Evaluate a captive credit-bureau or AA TSP partnership for exclusive data terms.

### Success metrics

| Phase | Metric |
|---|---|
| 0 | 1 signed pilot LOI, IP entity formed |
| 1 | 1 working agent plugin, 5 MCPs live, 100-case smoke test |
| 2 | Pilot live in prod, ≥ 2× underwriter throughput at ≤ baseline NPA-proxy |
| 3 | 3 paying NBFCs, ₹6–10Cr ARR run-rate |
| 4 | 10 NBFCs, ₹40–60Cr ARR, 60% margin |

---

## 7. Open Questions for You

1. **Pilot Customer #0.** Do you already have a named NBFC willing to be Pilot? If yes, MSME or secured-retail focus is decided by *their* P&L, not by us.
2. **Capital.** Is Yantra bootstrapped, founder-funded, or do you want a seed round? The 8–20 person model can be bootstrapped to break-even in 18 months with one pilot + one PE consortium partner; the seed-round path is faster but compresses optionality.
3. **PE consortium partner.** Do you have a relationship with any of: Premji Invest / Multiples / Norwest / Lightrock / A91 / Westbridge / Faering / ChrysCapital? The right one cuts 12 months off distribution.
4. **Founder skills mix.** Is the founding team credit-side, tech-side, or both? The 12-person services-co operating model needs *one* of each — if missing, that's the first hire.
5. **Open-sourcing the pack.** Apache-2.0 license like Anthropic does, or keep it private until 3 customers? Open accelerates ecosystem, may reduce capture-able value; private is slower but defensible. (Recommendation: open, but with a one-quarter delay on each Skill release.)
6. **Anthropic relationship.** Have you applied to Anthropic's Builder Program or Startup credits? The JV pattern suggests Anthropic actively partners with verticalized builders.

---

## 8. Gaps & Caveats

- `[GAP]` — JV name not yet public as of May 6, 2026.
- `[GAP]` — Live-Artifacts API surface details (custom data sources via MCP) not fully documented in accessible sources; verify before designing cookbooks around it.
- `[GAP]` — Dreaming developer-facing config/CLI details inaccessible to this research (Anthropic primary docs returned 403); fill in via `platform.claude.com/docs` directly.
- `[GAP]` — Several Indian MCP servers (AA, GSTN, bureau) may already exist in community repos — search `github.com` and Anthropic MCP directory before building from scratch.
- `[GAP]` — Existing private competitors in this space (Lentra, Pennant, Kuliza, Nucleus + their AI offshoots; Karza/Perfios moving up-stack) — competitive map not done; warranted before Phase 0 commitment.
- The "no SaaS, only Skills + Services" thesis is **a bet** — it can be wrong if NBFCs strongly prefer turnkey product over embedded engineering. The 6-week wedge is the test of that bet.

---

## 9. Sources

**Anthropic FS plugins**
- [Agents for financial services — Anthropic](https://www.anthropic.com/news/finance-agents)
- [anthropics/financial-services — GitHub](https://github.com/anthropics/financial-services)
- [Anthropic launches financial services plugins for Claude Cowork — Finextra](https://www.finextra.com/newsarticle/47353/anthropic-launches-financial-services-plugins-for-claude-cowork)
- [Anthropic deepens push into Wall Street… Moody's data partnership — Fortune](https://fortune.com/2026/05/05/anthropic-wall-street-financial-services-agents-jamie-dimon/)
- [Anthropic Launches Ten Finance Agent Templates for Claude — Let's Data Science](https://letsdatascience.com/news/anthropic-launches-ten-finance-agent-templates-for-claude-6516f048)

**Anthropic Legal plugins**
- [anthropics/claude-for-legal — GitHub](https://github.com/anthropics/claude-for-legal)
- [Anthropic Goes All-In on Legal — LawSites](https://www.lawnext.com/2026/05/anthropic-goes-all-in-on-legal-releasing-more-than-20-connectors-and-12-practice-area-plugins-for-claude.html)
- [Claude For Legal Launches — Artificial Lawyer](https://www.artificiallawyer.com/2026/05/12/claude-for-legal-launches-may-reshape-the-legal-tech-world/)
- [Anthropic Unveils 'Claude for Legal' — Legaltech Hub](https://www.legaltechnologyhub.com/contents/anthropic-unveils-claude-for-legal-with-12-new-plugins-20-mcp-connectors-and-more/)
- [The AI legal services industry is heating up — TechCrunch](https://techcrunch.com/2026/05/12/the-ai-legal-services-industry-is-heating-up-anthropic-is-getting-in-on-the-action/)

**Code w/ Claude SF 2026 features**
- [New in Claude Managed Agents: dreaming, outcomes, and multiagent orchestration — Claude](https://claude.com/blog/new-in-claude-managed-agents)
- [Code w/ Claude SF 2026: Building on the AI exponential — Claude](https://claude.com/blog/code-w-claude-sf-2026-sf)
- [Live blog: Code w/ Claude 2026 — Simon Willison](https://simonwillison.net/2026/May/6/code-w-claude-2026/)
- [Claude Code Dreams: Anthropic's New Memory Feature — claudefa.st](https://claudefa.st/blog/guide/mechanics/auto-dream)
- [Anthropic Launches Dreaming for Claude Agents — Let's Data Science](https://letsdatascience.com/blog/anthropic-dreaming-claude-managed-agents-self-improving-may-6)
- [Code with Claude 2026: 5 New Agent Features — MindStudio](https://www.mindstudio.ai/blog/code-with-claude-2026-new-agent-features)

**Live Artifacts**
- [Claude Live Artifacts: Persistent AI Workspace Guide — Eigent](https://www.eigent.ai/blog/claude-live-artifacts-guide)
- [Claude Cowork Update: Live Artifacts for Real Time Dashboards — Blockchain.news](https://blockchain.news/ainews/claude-cowork-update-live-artifacts-for-real-time-dashboards-and-trackers-2026-analysis)
- [Anthropic Claude Cowork is replacing dashboards with live artifacts — YourStory](https://yourstory.com/ai-story/claude-cowork-live-dashboards-ai-bi-disruption)

**Anthropic + Blackstone/H&F/Goldman JV**
- [Anthropic Partners with Blackstone, Hellman & Friedman, and Goldman Sachs — Blackstone](https://www.blackstone.com/news/press/anthropic-partners-with-blackstone-hellman-friedman-and-goldman-sachs-to-launch-enterprise-ai-services-firm/)
- [Anthropic takes shot at consulting industry in joint venture — Fortune](https://fortune.com/2026/05/04/anthropic-claude-consulting-industry-joint-venture-blackstone-goldman-sachs/)
- [Anthropic teams with Goldman, Blackstone… $1.5 billion AI venture — CNBC](https://www.cnbc.com/2026/05/04/anthropic-goldman-blackstone-ai-venture.html)
- [Anthropic and OpenAI are both launching joint ventures for enterprise AI services — TechCrunch](https://techcrunch.com/2026/05/04/anthropic-and-openai-are-both-launching-joint-ventures-for-enterprise-ai-services/)
- [Anthropic Partners with GIC to Launch Enterprise AI Services Firm — GIC](https://www.gic.com.sg/newsroom/all/anthropic-partners-with-blackstone-hellman-friedman-and-goldman-sachs-to-launch-enterprise-ai-services-firm/)
