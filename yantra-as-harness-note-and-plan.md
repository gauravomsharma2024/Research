# Yantra as Harness — Research Note II & Revised Plan

A response to *From Documents to Skills* (May 2026), reconciling the harness reframing with the prior research note and the May-2026 competitive landscape.

> Companion to `yantra-research-and-plan.md`. Where this contradicts the prior note, this supersedes it. Open questions in §11.

---

## 1. What Changed After Reading the Document

My prior note framed Yantra as a **productized-services play** anchored on Anthropic Skills + MCP, modeled on the Anthropic/Blackstone JV. After reading *From Documents to Skills*, three reframings are necessary:

1. **Yantra is a harness, not a curated plugin set.** ProcessRepo (the curated plugins) is one of three products. The harness — an India-FS-specific agentic platform with identity, knowledge, connectors, workbenches, standards, and controls — is the architectural product. Services is the third leg.
2. **ProcessSkill is a stricter format than Anthropic's Skill.** The doc proposes a multi-notation skill pack: markdown manifest + DMN + BPMN + CMMN + Gherkin + state machines + OpenAPI + parameterised templates + Python helpers + regulatory references. This is *deterministic by construction*. Anthropic's `SKILL.md` is loose markdown by design — LLM-friendly, audit-weak. The two are not the same artefact.
3. **The wedge is the credit-policy-as-skill-pack workbench (L4 + parts of L2/L6), not the underwriting agent.** The doc is right; the workbench is the moat. The underwriting agent is downstream.

What survives from the prior note: the Indian DPI MCP map (§3.1), the six-week wedge engagement template, the lean services-co operating model. What gets demoted: the assumption that a curated plugin pack on Anthropic's runtime is enough.

I'll be candid where I disagree with the doc — §7. Where I agree but want to add specificity — §3 to §6. Where I think the doc underweights a real threat — §8 (IBM-Yotta).

---

## 2. The Three-Product Yantra (Synthesizing Your Frame)

```
                    ┌──────────────────────────────────────────┐
                    │           YANTRA (the brand)             │
                    └──────────────────────────────────────────┘
                                       │
        ┌──────────────────────┬──────┴───────┬──────────────────────┐
        │                      │              │                      │
   ┌────▼─────┐         ┌──────▼──────┐  ┌────▼────────────┐
   │ Process- │         │   Harness   │  │ Implementation  │
   │   Repo   │         │             │  │    Services     │
   └──────────┘         └─────────────┘  └─────────────────┘
   The "what":           The "how":         The "who":
   curated India-FS      India-FS-          forward-deployed
   ProcessSkills.        opinionated        engineering, six-
   Open standard         agentic platform   week wedge per
   (ASL-2.0).            on top of MS       NBFC, training
   Marketplace +         Agent Framework /  knowledge writers.
   trust layer.          Camunda /
                         OSS components.
```

The three reinforce each other: ProcessRepo is the gravity well that drags institutions onto the Harness; Harness is the runtime that makes ProcessRepo deployable defensibly; Services is the conversion engine and the source of new ProcessSkills (anonymised back to the Repo with the customer's permission). Pricing model in §10.

---

## 3. ProcessSkill — A Concrete Specification Proposal

The doc names the components but stops short of a folder layout. Proposing this as the v0 spec, mirroring `anthropics/claude-for-legal` discipline (markdown + JSON, no build step) but with the deterministic notations you require:

```
<process-skill>/
├── manifest.yaml                       # name, owner, reviewers, regulatory refs,
│                                         when-to-use, version, semver compat,
│                                         compliance gates, sensitivity tier
├── README.md                           # human prose
├── decisions/
│   └── *.dmn                           # DMN 1.5, FEEL expressions, hit policies
├── process/
│   └── *.bpmn                          # BPMN 2.0
├── case/                               # CMMN where work has no fixed sequence
│   └── *.cmmn
├── state/
│   └── lifecycle.yaml                  # state machine — states, transitions, guards
├── contracts/
│   ├── inputs.schema.json              # JSON Schema for inputs
│   ├── outputs.schema.json             # JSON Schema for outputs
│   └── tools.openapi.yaml              # tools the skill calls (via MCP gateway)
├── helpers/                            # deterministic compute
│   └── *.py                            # pure functions, unit-tested
├── templates/                          # compliance-approved customer comms
│   ├── kfs.j2
│   ├── sanction-letter-en.j2
│   ├── sanction-letter-hi.j2
│   └── ...
├── tests/
│   ├── *.feature                       # Gherkin
│   └── fixtures/
├── regulatory/
│   ├── refs.yaml                       # cite RBI/IRDAI/SEBI master directions w/ dates
│   └── annotations.md                  # how the skill interprets each ref
├── prompts/                            # the LLM-facing surface
│   ├── SKILL.md                        # auto-generated from manifest+DMN+BPMN
│   │                                     for Anthropic-Skill-compatible runtimes
│   └── system.txt
├── eval/
│   ├── rubric.yaml                     # outcomes-style rubric
│   └── adversarial.feature             # red-team scenarios
└── .yantra/
    ├── compiled/                       # cached compiled artefacts
    └── signatures/                     # signed by author + reviewers (Ed25519)
```

### 3.1 Why this works on Anthropic's runtime *and* the Harness

The `prompts/SKILL.md` is auto-generated from the deterministic artefacts. That means:
- **On Claude Cowork / Claude Code / Anthropic Managed Agents**: install the ProcessSkill as if it were an Anthropic Skill — it is a superset, the markdown surface is what the agent sees.
- **On the Yantra Harness**: the runtime reads the DMN/BPMN/CMMN directly and invokes the LLM only for the *parts that are not enumerable*. Determinism stays in code; LLM stays in judgement.

This is the most important architectural decision in the entire stack: **never compete with Anthropic's runtime — be a strict superset that can run there too**. Same insight as building on top of Postgres rather than forking it.

### 3.2 The validator (what makes "live aid" real)

The `live-aid` system in §16 of your doc becomes a single CLI + LSP server:

```
yantrac validate ./process-skills/personal-loan-topup
```

Checks:
- Manifest required fields, semver, regulatory refs not repealed
- DMN: completeness (no missing rules under Unique hit policy), no overlaps, FEEL parses, decision tables have ≥ 1 test per row
- BPMN: each task has owner, error boundaries on long-waits, named gateways
- Contracts ↔ DMN inputs ↔ Gherkin Givens are consistent
- Templates: every variable bound, sender-jurisdiction consistent with `manifest.compliance.jurisdictions`
- Regulatory: every `[ref:RBI/...]` resolves; no reference older than X months without explicit acknowledgement
- Eval: ≥ N adversarial scenarios per skill shape (decisional → 5, customer-touching → 10)

Same validator runs in three places: author IDE (LSP), CI (GitHub Action), runtime admission control (the Harness refuses to load an invalid skill). One spec, three enforcement surfaces.

### 3.3 ProcessSkill ↔ Anthropic Skill ↔ MS / Google primitives — the compatibility matrix

| ProcessSkill component | Anthropic Skill | Microsoft Agent Framework | Google ADK |
|---|---|---|---|
| `prompts/SKILL.md` | `SKILL.md` (1:1) | Skill plugin metadata | ADK skill descriptor |
| `decisions/*.dmn` | — (no native) | call out to BRMS via tool | call out to BRMS via tool |
| `process/*.bpmn` | — | call Camunda 8 / Logic Apps | call Camunda 8 / Workflows |
| `contracts/tools.openapi.yaml` | MCP server discovery | Agent Framework tool registration | ADK tool registration |
| `templates/*.j2` | inline in prompts | content-safety-approved | content-safety-approved |
| `eval/rubric.yaml` | Outcomes rubric (1:1) | App Insights eval | Vertex Eval |
| `regulatory/refs.yaml` | — | Purview labels | DLP / catalog |

The ProcessSkill is the union; each runtime consumes the subset it understands. Yantra Harness is the only runtime that consumes all of it.

---

## 4. The Harness — Concrete Build/Buy/Partner per Layer

Your §14 is right in spirit but should be tightened for the Indian-FS-NBFC profile. My take, layer-by-layer, distinguishing what the **Harness ships** vs what the **customer brings**:

| Layer | Yantra Harness ships | Customer brings | Open-source posture |
|---|---|---|---|
| **L1 Models** | Model router (LiteLLM-derived) with sensitivity-tier policy; Anthropic / OpenAI / Bedrock-Mumbai / Foundry-India / Sarvam / Krutrim / on-prem Llama adapters; cost & latency telemetry; PII-scrub middleware | Choice of models per tier; commercial contracts | OSS router config; commercial managed control plane |
| **L2 Knowledge** | Knowledge ingestion pipeline tuned for RBI/IRDAI/SEBI corpora (master directions, circulars, FAQs, press releases); Indic-language chunking; regulatory-version-aware retrieval; "as-of date" semantics | Internal policy corpus, SOPs, training material | OSS ingestion adapters; commercial RBI/IRDAI/SEBI corpus subscription with weekly delta |
| **L3 Connectors** | **YantraGateway** — MCP gateway pre-wired with India-DPI Tier-A MCPs (AA, GSTN, CKYCR, DigiLocker, bureau×3, VAHAN, IGRS, NACH, NPCI, eSign) and Tier-B (Karza, Perfios, CredGenics, IBJA, Hyperverge, Signzy) per the prior note §3.1 | Internal LOS/LMS/CBS/BRE adapters (Tier C — built per customer) | OSS gateway (fork Kong + plugins); commercial India-DPI MCP fleet w/ uptime SLA |
| **L4 Product Workbench** | **YantraAuthor** for ProcessSkills (the wedge) — DMN/BPMN/CMMN editor, validator, simulator with anonymised industry scenarios, AI co-authoring, regulatory-grounding panel | Their actual policies | OSS spec + validator + LSP + CLI; commercial cloud authoring + scenario library |
| **L5 Process Workbench** | Camunda 8 Self-Managed embedded; preconfigured BPMN templates for retail-loan-orig, gold-branch-ops, MSME-cashflow-uw, claims-FNOL; sensitivity-aware trace viewer | Customer-specific process variants | Camunda is OSS; Yantra adds the BFSI templates |
| **L6 Standards** | Brand/tone/disclosure framework templates per RBI Fair Practices Code, RBI Digital Lending Guidelines, RBI FREE-AI Sutras, IRDAI policyholder protection, DPDP 2023; OPA policy bundles; Indic content safety filters | Bank-specific brand/tone | OSS policy bundles; commercial managed updates as regs change |
| **L7 Cross-cutting Controls** | Identity adapter (Entra / Okta / on-prem AD); **per-skill cryptographic identity (Ed25519)** mirroring Google ADK Agent Identity + Microsoft Agent Governance Toolkit; WORM audit log to S3-compatible storage in India region; outcomes harness; Langfuse-derived observability with India-region option; lifecycle (canary, rollback) | IDP, SIEM, ticketing | OSS control plane; commercial managed observability + audit |

### 4.1 What we are *not* building

- **Not a foundation model.** Use Claude / GPT / Gemini / Sarvam / Krutrim. Yantra's IP is in the deterministic surface around the model, not the model.
- **Not a workflow engine.** Camunda 8 is excellent and has BPMN/DMN/CMMN coverage. Embed it; do not reinvent it. Camunda 8.9 already implements the "BPMN activity = LLM tool" pattern.
- **Not an agent framework.** MS Agent Framework / Google ADK / Anthropic SDK are converging on similar primitives. Yantra integrates with all three rather than picking one and losing optionality.
- **Not a hyperscaler.** Yotta / E2E Networks / AWS Mumbai / Azure India do compute. Yantra opinionates on the layer above.

### 4.2 What we are building (and why it's defensible)

- **The ProcessSkill spec + validator + authoring experience** (L4). This is the standard. If this becomes how Indian FS authors policies, the rest is gravity.
- **The India-FS knowledge engine** (L2) — RBI/IRDAI/SEBI corpus with version-aware retrieval. Boring, indispensable, hard to do well.
- **YantraGateway with India-DPI MCP fleet** (L3). Each MCP is small; the *fleet* is hard.
- **The compliance hooks + outcomes rubrics tuned to Indian regulation** (L6/L7). Differentiated by depth, not breadth.
- **The opinionated assembly** — Microsoft Agent Framework + Camunda + the gateway + the workbench, pre-integrated, with one identity model and one audit trail. The opinionation is the product.

This is a 12–25 person engineering org over 24 months for v1. Not 200, not 5.

---

## 5. ProcessRepo — Organising the Curated Plugins

Mirror the structure of `anthropics/claude-for-legal` (proven to work) but with the multi-notation discipline of §3:

```
yantra/process-repo/
├── .yantra-marketplace.json
├── docs/
├── core-verticals/
│   ├── credit-core/                    # equivalent of "financial-analysis" core
│   ├── compliance-core/                # KYC tiers, FPC, Digital Lending, DPDP, AML
│   └── communications-core/            # KFS, sanction letters, recovery comms
├── product-lines/
│   ├── msme-unsecured/
│   ├── msme-secured/
│   ├── gold-loan/
│   ├── vehicle-loan-new/
│   ├── vehicle-loan-used/
│   ├── lap/
│   ├── personal-loan/
│   └── co-lending/
├── functions/                          # cross-product
│   ├── kyc-vcip/
│   ├── underwriting/
│   ├── pricing-fees/
│   ├── disbursal/
│   ├── collections-early/
│   ├── collections-late/
│   ├── recovery/
│   ├── npa-classification/
│   ├── fraud/
│   └── reg-filing/
├── always-on/                          # Managed Agent cookbooks (your Phase-3 wedge)
│   ├── reg-monitor/                    # RBI / IRDAI / SEBI / state stamp duty
│   ├── npa-early-warning/
│   ├── collections-router/
│   ├── fraud-cluster-watcher/
│   ├── audit-trail-generator/
│   └── inspection-pack-builder/        # bundles WORM evidence for RBI inspection
├── partner-built/                      # vendor-authored ProcessSkills
│   ├── karza/
│   ├── perfios/
│   ├── credgenics/
│   ├── hyperverge/
│   └── ...
└── scripts/
    ├── validate.py
    ├── compile-skill-md.py             # auto-generates the Anthropic-compatible surface
    ├── deploy-managed-agent.sh
    └── publish-marketplace.py
```

### 5.1 Governance of ProcessRepo

Three classes of artefact, three governance regimes:

1. **Core verticals** — owned by Yantra, ASL-2.0, accepts PRs through a Skills Steering Committee with ≥ 1 RBI/IDRBT-credentialed reviewer. These define the *standard*.
2. **Partner-built** — vendor-authored, vendor-maintained, signed by vendor key, runs through the same validator. This is how Karza/Perfios/CredGenics keep their economics and Yantra gets distribution. Mirror Anthropic's `partner-built/lseg`.
3. **Customer private** — never enter ProcessRepo unless explicitly contributed back (anonymised). Held in customer's own Git fork; the Harness loads them with the same trust layer.

A **trust layer** — security scan, license check, signature verify, freshness check (no skills with > 6 month old regulatory refs without acknowledgement) — gates every install in customer environments. Mirror `legal-builder-hub`'s skill installer.

---

## 6. The "Live Aid" Authoring Discipline — As a Product

The doc treats this as supporting tooling. I think it's the single most defensible product surface in the entire harness, and should be marketed as such. Three audiences:

| Audience | Surface | Pricing |
|---|---|---|
| Senior policy author at NBFC | VS Code extension + Logseq/Obsidian plugin + web composer | Per-author seat |
| External BFSI consultant / SI | Same + multi-tenant workspace | Per-seat + per-customer |
| Customer's CI/CD | CLI + GitHub Action + admission webhook | Per-skill / unlimited |

The validator is OSS (drives adoption); the **scenario library + AI co-authoring + regulatory grounding panel** are commercial. This mirrors GitHub's free-CLI / paid-Copilot split exactly.

> **Naming proposal:** `YantraAuthor` (the workbench) sits on top of `processkill` (the OSS spec + validator). The latter becomes the noun the industry uses. *"We author our credit policy in processkill format."* If that sentence becomes natural in three years, Yantra has won.

---

## 7. Where I Push Back on the Document

Five honest disagreements / sharpenings, not minor:

### 7.1 "Standards rarely get displaced" is half-true

In regulated industries, **standards are set by regulators or by dominant incumbents that the regulator deferred to**. ProcessSkill becoming a standard requires:

- (a) RBI / IDRBT formal endorsement or, weaker, "adoption pattern" reference in a circular,
- (b) ≥ 3 anchor banks/NBFCs publicly authoring in the format,
- (c) Open governance — a foundation, not a private repo (Apache or Linux Foundation sub-project, with seats for IBA, FACE, NPCI, IDRBT, plus 1 Yantra seat).

Without (a) and (c), it's a popular framework, not a standard. Treat (a)–(c) as *Phase 0 outputs*, not Phase 4.

### 7.2 "Position complementary in Y1–2, competitive in Y3–5" is too Microsoft-centric

Your §18 anchors to MS + Anthropic. But the May-2026 reality is **multi-pole**: Microsoft + Anthropic on one axis, Google ADK on another, Anthropic standalone on a third (Cowork is increasingly a Microsoft alternative for SMBs), IBM-Yotta sovereign on a fourth, AWS Bedrock as the neutral runtime. Indian institutions will not pick one; they will run two or three.

Implication: the harness must be **runtime-neutral from day 1**, not Microsoft-anchored. The Microsoft Agent Governance Toolkit (Apr 2026) — which hooks both MS Agent Framework's middleware *and* Google ADK's plugin system — is the model. Yantra's identity, audit, and policy enforcement should ride on Microsoft's toolkit *as a consumer*, not be a Microsoft-bundled feature.

### 7.3 The CMMN bet may be too cautious

CMMN has poor tooling support and weak vendor adoption. For the BFSI use cases you list (wealth management, trade finance, complex grievance), most teams in 2026 are doing one of:
- BPMN with ad-hoc sub-processes (Camunda 8.9's pattern), or
- LangGraph-style state graphs with a durable workflow underneath.

I'd drop CMMN from the v0 spec and add it back in v2 if customer demand materialises. Reduces the cognitive load on authors significantly.

### 7.4 Knowledge-writers-as-first-class-citizens needs a different *buyer*

Your §15 is correct. But the buyer this implies is the **CHRO partnered with the COO/CRO**, not the CIO. The CIO will buy a Harness; the CHRO/COO will buy the *knowledge-author transformation*. Two motions, two buyers, two pricing models. Yantra's services arm should learn to sell to both — most "AI" services firms only know how to sell to the CIO.

### 7.5 The Indian-anchored platform thesis underweights IBM-Yotta

IBM + Yotta announced a **sovereign agentic AI platform for Indian enterprises** on May 7, 2026 — watsonx Orchestrate on Shakti Cloud, BFSI listed first. That's real distribution (IBM's BFSI book in India is large) and real sovereignty (Yotta is Indian-owned). They are 6–9 months ahead on *announcement*, behind on *opinionation*.

Yantra's wedge against them: **(a) ProcessSkill as a publishable standard** (IBM will not open this); **(b) authoring experience for senior knowledge writers** (watsonx Orchestrate is engineer-first); **(c) NBFC focus** (IBM goes for top-15 banks). IBM-Yotta is the closest competitor; not a reason to stop, a reason to specialise.

---

## 8. Competitive Landscape (May 2026 Snapshot)

| Player | What they have | What they don't | Yantra's positioning |
|---|---|---|---|
| **Microsoft Agent Framework + Cowork + M365 + Agent Governance Toolkit** | Identity, distribution, governance, M365 surface, OSS toolkit covers OWASP Agentic Top-10 | India-FS opinionation, regulatory grounding, ProcessSkill format, MSME+secured-retail playbooks | Yantra rides on top — uses Toolkit, integrates with Framework |
| **Google ADK + Agent Identity + Agent Registry** | Per-agent crypto identity, central registry, Vertex Eval | India FS regulation, Indic models tuning depth, knowledge-author UX | Yantra interoperates via Toolkit's ADK plugin hook |
| **Anthropic Skills + Cowork + Managed Agents + the JV** | Open repos as template, Memory/Dreaming/Outcomes runtime, $1.5B JV with PE | No Indian customers in roster, no Indian rails MCPs, no Indian regulatory corpus | ProcessSkill is a superset of Anthropic Skill; can run there too |
| **IBM-Yotta sovereign agentic AI** | watsonx Orchestrate, Shakti Cloud (Indian sovereign), IBM's BFSI distribution, top-of-mind for top banks | Open standard, knowledge-author experience, NBFC focus, deterministic skill format | Standard + workbench + NBFC depth |
| **Camunda 8.9** | Best BPMN/DMN runtime, "BPMN activity = LLM tool" pattern, Copilot generates BPMN, OSS | India FS opinionation, knowledge corpus, ProcessSkill format | Yantra **embeds** Camunda; does not compete |
| **Indian incumbents — Lentra, Pennant, Kuliza, Nucleus FinnOne, Newgen** | Existing LOS/LMS/BRE in NBFCs, sales relationships | Agentic substrate, knowledge-author experience, ProcessSkill format | Wrap their products as MCPs in YantraGateway; partner-built ProcessSkills |
| **AA TSPs — Finvu, OneMoney, CAMSFinServ, NADL** | Regulated AA pipes | Agent runtime, skill format | Partner — OSS MCP wrappers; commercial uptime SLA |
| **Karza / Perfios / Hyperverge / Signzy / CredGenics** | Indian commercial data + ops APIs | Agent runtime | Partner — partner-built ProcessSkills, revenue share |

The *crowded* space is L1 (models), L5 (workflow), L7 (controls). The *uncrowded* spaces specifically for India-FS are **L2 (knowledge engine for regulators), L4 (workbench for authors), and the ProcessSkill spec itself**. Yantra concentrates there.

---

## 9. Open-Source / Commercial / Standards Strategy

The defensible posture is the **Confluent / HashiCorp / Camunda commercial-OSS** pattern, with one twist (the standards body):

| Asset | License | Why |
|---|---|---|
| ProcessSkill specification | Apache-2.0, governed by a foundation | Adoption requires giving up control; a private spec dies |
| `yantrac` validator + LSP + CLI | Apache-2.0 | Author tooling has to be free or it doesn't get used |
| Reference Harness (single-tenant, self-hosted) | BSL → Apache-2.0 after 4 years (Camunda model) | Defensible for 4 years, then community |
| ProcessRepo core verticals | Apache-2.0 | Standard-defining; must be inspectable |
| Partner-built ProcessSkills | Vendor's choice (typically commercial) | Karza/Perfios revenue model |
| YantraAuthor (cloud) | Commercial SaaS | Per-seat; this is where Yantra makes money on authors |
| YantraGateway managed | Commercial SaaS | Per-call after free tier; uptime SLA is the value |
| RBI/IRDAI/SEBI corpus (live updated) | Commercial subscription | Daily delta, version semantics, weekly briefings |
| Implementation services | T&M with success fees | Six-week wedge from prior note §5.4 |
| Outcomes-tuned-to-portfolio engagement | Commercial retainer | Per-month; this is the recurring layer |

**The standards body**: target a sub-project under a recognised foundation (LF AI & Data, OASIS, Linux Foundation Decentralized Trust, or a fresh BIAN-adjacent body). Seed governance with seats for IBA + FACE + IDRBT + NPCI + 2 banks + 2 NBFCs + Yantra. Yantra is one of nine seats, not the chair. Counterintuitive, correct.

---

## 10. Pricing — Three SKUs

| SKU | Buyer | Price metric | Indicative |
|---|---|---|---|
| **YantraAuthor** | CHRO + COO + CRO (the knowledge author org) | per-seat / month | ₹15K–40K per author per month |
| **Yantra Harness Cloud** | CIO / CTO | per-portfolio per month + per-tool-call after free tier | ₹15–40L per NBFC per month base + variable |
| **Yantra Harness Self-Managed** | CIO / CTO of large bank | annual subscription per environment | ₹2–6Cr per environment per year |
| **Always-On Cookbooks** | CRO / Chief Compliance Officer | per cookbook per month | ₹5–15L per cookbook per month |
| **Implementation Services** | Sponsor (CRO/COO) | fixed-fee wedge + retainer | ₹40–80L six-week wedge + ₹10–30L/month |
| **RBI/IRDAI/SEBI Corpus subscription** | Compliance Head | annual | ₹50L–1.5Cr per year |

**Year-2 target (10 NBFCs):**
- 6 cloud (~₹2Cr/yr each) = ₹12Cr
- 2 self-managed (~₹4Cr/yr each) = ₹8Cr
- 2 cookbooks per customer × 10 × ₹10L × 12 = ₹24Cr
- Author seats (50 across 10 customers) × ₹25K × 12 = ₹1.5Cr
- Services (10 wedges + ongoing retainers) = ₹15–20Cr
- Corpus subscriptions (10 × ₹75L) = ₹7.5Cr

**Total ARR Y2: ~₹65–75Cr.** Margin profile: ~55% gross at this scale (services drag, fixed corpus cost). Higher than the prior note's estimate because the harness/author SKUs have software margins.

---

## 11. Open Questions for You

Some of these I can guess; better to ask explicitly.

1. **Standards body realism.** Are you willing to give up unilateral governance of the ProcessSkill spec in year 1–2 to win RBI/IDRBT acknowledgement? This is the core trade. You retain commercial advantage through the workbench, the corpus, and the gateway — but the spec belongs to the industry.
2. **Microsoft / Camunda commercial relationships.** Do you have warm intros at Microsoft India BFSI, Camunda APAC, and Anthropic India (if it exists yet)? These determine whether year-1 is "we are a Microsoft partner shipping the BFSI opinionation Microsoft cannot ship" or "we are alone."
3. **Founding team — the 4-person specificity in your §18.** Is the team in place? Specifically: do you have (a) the senior credit/risk SME, (b) the regulator-side veteran, (c) the platform engineer at AI-lab caliber? These three are non-substitutable. If gaps, Phase 0 is a hiring problem, not a building problem.
4. **Anchor customer #1.** Phase 0's most important deliverable is one signed LOI from an NBFC willing to author one product line in ProcessSkill format with you, on a paid pilot, in the next 90 days. Without this, the rest is theory.
5. **CMMN drop.** Are you wedded to CMMN in v0, or comfortable deferring? (My §7.3 view: defer.)
6. **Distribution-vs-control on the Harness.** Open-source self-managed Harness *competes with* Yantra Harness Cloud. Comfortable with that cannibalisation, or do you want a more closed posture year 1?
7. **IBM-Yotta posture.** Do you want to position as alternative to them, complement to them (their workbench partner), or quietly ignore until you have customers? My recommendation: ignore publicly, monitor closely, ensure your top-3 target customers are *not* in IBM's top-15 conversation.
8. **Sovereign cloud commitment.** Are you okay being on Yotta or AWS Mumbai by default and viewing on-prem as a per-customer add-on, or do you want on-prem as the default?

---

## 12. Revised Phased Plan

### Phase 0 — Standards, team, anchor customer (months 0–4)

Deliverables, all of which must complete before Phase 1:
- [ ] **ProcessSkill spec v0.1** published on a public Git repo (Apache-2.0); RFC process documented
- [ ] **`yantrac` validator v0.1** as OSS CLI + GitHub Action (covers manifest, DMN, BPMN, contracts, tests, regulatory ref resolution)
- [ ] **Foundation home identified** (LF AI & Data is most pragmatic); pre-conversations with IBA, IDRBT, FACE, NPCI started
- [ ] **Founding team complete** to the 4-person spec from your §18
- [ ] **Pilot customer #0 signed** (LOI; first product line scoped)
- [ ] **Regulator advisory engagement** initiated with RBI Innovation Hub / IDRBT / FREE-AI working group — even one informal meeting is sufficient as a Phase-0 gate
- [ ] **One reference ProcessSkill end-to-end**: personal-loan top-up assessment (your §4 example), publishable, demonstrable

Headcount end-of-phase: 4 founders + 2 engineers + 1 author SME + 1 BD = 8.

### Phase 1 — Harness MVP + first customer go-live on the wedge (months 4–10)

- [ ] **YantraAuthor v0** — VS Code extension + web composer; validator embedded; AI co-authoring (Claude); regulatory grounding panel reading RBI corpus
- [ ] **Knowledge engine v0** — RBI master directions + IRDAI circulars + SEBI rules ingested with version semantics; weekly delta job
- [ ] **YantraGateway v0** — MCP gateway with 8–10 India-DPI MCPs (one each: AA TSP, GSP, bureau, CKYCR, DigiLocker, eSign, WhatsApp, NACH)
- [ ] **Camunda 8 embedded** with Yantra BPMN templates for the wedge product line
- [ ] **L7 controls v0** — Ed25519 per-skill identity, WORM audit to S3 in Mumbai region, Langfuse-derived observability, Microsoft Agent Governance Toolkit integrated
- [ ] **One product-line ProcessSkill pack** (MSME unsecured cash-flow underwriting *or* gold-loan branch ops — pick at Phase 0 close) live in pilot customer
- [ ] **15 ProcessSkills authored** by pilot's senior credit/policy team using YantraAuthor; outcomes loop tuned

End-of-phase metrics: pilot in shadow mode → A/B mode at 10–20% of loans for the wedge product line; ≥ 2× underwriter throughput at ≤ baseline NPA-proxy; zero RBI-FPC violations.

Headcount: 16.

### Phase 2 — Standardise + 2 more customers (months 10–18)

- [ ] **ProcessSkill spec v1.0** through foundation governance; ≥ 2 external organisations with adopted-by-design statements
- [ ] **Always-on cookbooks productised** — `reg-monitor`, `npa-early-warning`, `fraud-cluster-watcher`, `audit-trail-generator` — sold standalone to NBFCs not yet ready for full author transformation
- [ ] **Two additional customers live** on the wedge; one in MSME, one in gold-loan
- [ ] **YantraGateway v1** — 25+ MCPs including Tier-B and 3 customer Tier-C wrappers
- [ ] **Partner program launched** — Karza, Perfios, CredGenics, Hyperverge sign partner-built ProcessSkill agreements
- [ ] **First RBI inspection-pack-builder** cookbook used in an actual inspection (with customer permission, anonymised)

Headcount: 24.

### Phase 3 — Multi-product, federation, Microsoft/Anthropic co-sell (months 18–30)

- [ ] **Harness Self-Managed** GA for large banks
- [ ] **Microsoft / Anthropic co-sell** — formal partner status; Yantra as the BFSI opinionation layer in those go-to-markets
- [ ] **Builder Hub** — community ProcessSkills with the trust layer; mirror Anthropic's `legal-builder-hub`
- [ ] **10 paying customers**; ARR ₹65–75Cr; gross margin ≥ 55%
- [ ] **First insurance customer** (IRDAI corpus; small expansion bet)

Headcount: 35–45.

### Phase 4 — Become the standard (months 30–48)

- [ ] **ProcessSkill v2.0** — adds CMMN if demand exists, adds insurance and capital-markets verticals
- [ ] **Indian regulator references the spec** in any official communication (this is the moment Yantra "wins" — not revenue)
- [ ] **Geographic expansion** — Bangladesh, UAE (NBFC analogues with overlapping regulatory style)
- [ ] **Yantra IPO-readiness** evaluation — at this scale (₹250–400Cr ARR plausible), the company is a category-defining asset

---

## 13. The One-Slide Mental Model

> Yantra builds the **format** in which Indian financial services authors its operational logic, the **harness** that runs it defensibly, and the **services** that get the first 10 institutions across the gap. The format becomes the standard; the harness becomes the runtime; the services pay the bills until the standard compounds.
>
> Microsoft owns identity. Anthropic owns cognition. Camunda owns workflow. IBM-Yotta owns sovereign infrastructure. Yantra owns the **artefact** — the thing the author writes, the thing the regulator reads, the thing the runtime executes.
>
> If, in 2030, Indian banks routinely say "we author in processkill format," Yantra has won, regardless of who wins the model war or the cloud war.

---

## 14. Sources (incremental to prior note)

- [Microsoft Agent Governance Toolkit — Microsoft Open Source Blog](https://opensource.microsoft.com/blog/2026/04/02/introducing-the-agent-governance-toolkit-open-source-runtime-security-for-ai-agents/)
- [microsoft/agent-governance-toolkit — GitHub](https://github.com/microsoft/agent-governance-toolkit)
- [Microsoft Agent Governance Toolkit covering OWASP Agentic Top 10 — InfoWorld](https://www.infoworld.com/article/4155591/microsofts-new-agent-governance-toolkit-targets-top-owasp-risks-for-ai-agents.html)
- [Google ADK is an Agent Execution Framework — Futurum](https://futurumgroup.com/insights/google-adk-is-not-a-toolkit-it-is-an-agent-execution-framework/)
- [Microsoft and Google Tackle Agentic Governance — Shelly Palmer](https://shellypalmer.com/2026/05/microsoft-and-google-tackle-agentic-governance/)
- [Camunda Agentic Orchestration](https://camunda.com/solutions/agentic-orchestration/)
- [Camunda 8.9: Fastest Path to Agentic Orchestration](https://camunda.com/blog/2026/04/camunda-8-9-fastest-path-to-agentic-orchestration/)
- [Camunda 8 AI Agents docs](https://docs.camunda.io/docs/components/agentic-orchestration/ai-agents/)
- [IBM, Yotta to build sovereign agentic AI platform for Indian enterprises — Business Today](https://www.businesstoday.in/technology/story/ibm-yotta-to-build-sovereign-agentic-ai-platform-for-indian-enterprises-530268-2026-05-07)
- [Yotta + IBM agentic AI platform — CRN Asia](https://www.crnasia.com/india/news/2026/yotta-ibm-plan-agentic-ai-platform-for-indian-enterprises-as-demand-grows-for-sovereign-ai-deployments)
- [Analysing RBI's AI Framework — NIPFP](https://www.nipfp.org.in/publication-index-page/blog-index-page/analysing-rbis-ai-framework/)
- [Understanding RBI FREE-AI Framework — Solytics Partners](https://www.solytics-partners.com/resources/blogs/understanding-rbi-free-ai-framework-2025-building-responsible-ethical-and-accountable-ai-governance-in-indias-bfsi-sector)
- [I4C–RBIH AI pact for fraud detection — Business Today](https://www.businesstoday.in/technology/story/indian-govt-rbi-innovation-signs-ai-pact-to-tackle-financial-frauds-531090-2026-05-12)

(Plus all sources from `yantra-research-and-plan.md` §9.)
