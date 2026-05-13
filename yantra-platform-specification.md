# Yantra — An Agentic ERP for Indian Financial Services

A grounds-up specification of the Yantra platform, its product constructs (ProcessSkill, ProcessRepo, YantraGateway, YantraAuthor, LiveAid, LLM Adapter, Knowledge Graph, Discover, Portability Layer), the implementation methodology, and the strategic frame.

> Working specification, May 2026. Sources cited inline.

---

## 1. The Frame — Why Yantra Is an Agentic ERP, Not an Agent Framework

On May 12, 2026, at Sapphire, **SAP unveiled the *Autonomous Enterprise*** — a fundamental reengineering of its ERP platform around agentic AI. The shape of the announcement is the most important reference point for understanding Yantra:

- **SAP Business AI Platform** — a single governed environment that unifies SAP BTP, Business Data Cloud, and Business AI.
- **SAP Knowledge Graph** at the core — a structured map of business entities, processes, and relationships that grounds every agent in business context.
- **SAP Autonomous Suite** — 50+ domain-specific *Joule Assistants* (finance, supply chain, procurement, HCM, CX) orchestrating 200+ specialized agents to run processes end-to-end.
- **Joule Studio** — the managed studio for building, deploying, and running agents and skills. No-code + pro-code; supports Claude Code, Cursor, VS Code; supports LangGraph, CrewAI, AutoGen, LlamaIndex.
- **Anthropic Claude as primary reasoning** across Joule.
- **MCP** for tool connectivity. **A2A (Agent-to-Agent)** for Bring-Your-Own-Agent runtimes. **n8n embedded** as orchestration glue (n8n's valuation doubled to $5.2B on the back of this).

SAP did not build an "agent framework alongside Microsoft's." SAP rebuilt ERP itself as an agentic system, with the agent framework being a *consequence* of the platform, not the product. That is the right mental model for Yantra.

**Yantra is the Agentic ERP for Indian Financial Services.** It is not a wrapper on Anthropic Skills, not a vertical pack on top of Microsoft Agent Framework, and not a workflow engine with LLM hooks. It is the system inside which Indian banks, NBFCs, AMCs, and insurers will configure and run their operations in the agentic era — analogous to what S/4HANA + Joule + Business AI Platform is becoming for global enterprises, but built agent-native from day one, opinionated for Indian financial regulation and rails, and architected so the underlying model is a replaceable component rather than the centre of the platform.

The shorthand: **Yantra is to Indian Financial Services what SAP Autonomous Enterprise is to global manufacturing and supply chain.** With three deliberate differences:
1. **Built agent-native**, not retrofitted onto a 30-year-old transactional core.
2. **Multi-LLM by design** via an adapter — Claude, GPT, Gemini, Sarvam, Krutrim, on-prem Llama — chosen per workload sensitivity, not per platform vendor relationship.
3. **Configured in ProcessSkill** — a deterministic, multi-notation format that is simultaneously human-readable, machine-executable, and regulator-auditable — not in ABAP or vendor-proprietary metadata.

---

## 2. The Platform — Yantra's Twelve Canonical Components

Yantra is a platform, not a product. Twelve components, each replaceable in isolation, composed into one governed environment:

```
                              ┌──────────────────────────────┐
                              │      Identity & Sovereign    │
                              │      Hosting (component 11)  │
                              └──────────────┬───────────────┘
                                             │
   ┌─────────────────────────────────────────┴───────────────────────────────────┐
   │                          Y A N T R A   P L A T F O R M                       │
   │                                                                              │
   │   ┌───────────────┐  ┌───────────────┐  ┌───────────────┐  ┌──────────────┐ │
   │   │ ProcessSkill  │  │  ProcessRepo  │  │   Yantra      │  │   LiveAid    │ │
   │   │  Spec (1)     │  │  Library (2)  │  │   Author (3)  │  │  Service (4) │ │
   │   └───────────────┘  └───────────────┘  └───────────────┘  └──────────────┘ │
   │                                                                              │
   │   ┌───────────────┐  ┌───────────────┐  ┌───────────────┐  ┌──────────────┐ │
   │   │   Knowledge   │  │     LLM       │  │ Deterministic │  │   Yantra     │ │
   │   │   Graph (5)   │  │  Adapter (6)  │  │  Engines (7)  │  │ Gateway (8)  │ │
   │   └───────────────┘  └───────────────┘  └───────────────┘  └──────────────┘ │
   │                                                                              │
   │   ┌───────────────┐  ┌───────────────┐  ┌───────────────┐                   │
   │   │    Agent      │  │   Portability │  │    Yantra     │                   │
   │   │  Runtime (9)  │  │  Layer (10)   │  │  Discover (12)│                   │
   │   └───────────────┘  └───────────────┘  └───────────────┘                   │
   └──────────────────────────────────────────────────────────────────────────────┘
```

Each component is detailed in §3–§14. The composition reads top-to-bottom, left-to-right: **identity gates everything**, **ProcessSkill is the unit of configuration**, **ProcessRepo is the curated library**, **Author + LiveAid are the configuration tools**, **Knowledge Graph + LLM Adapter + Deterministic Engines + Gateway are the runtime substrate**, **Agent Runtime composes them**, **Portability Layer publishes outward**, **Discover is how every customer engagement begins**.

---

## 3. ProcessSkill — The Configuration Unit

A ProcessSkill is to Yantra what a Claude Plugin is to Claude Code — a single installable package that bundles **deterministic control** (decisions, processes, contracts, hooks, helpers, monitors) with the **non-deterministic context** an LLM needs to operate on top of it (the markdown surface) — except sharpened for regulated financial services so that every deterministic artefact is enforceable, auditable, and authorable by domain experts rather than software engineers.

It is a *folder of files* that fully specifies how a specific operational process is to be performed inside the institution. The same folder is read by humans (in preview), executed by the runtime (from code), audited by regulators (from signatures), explained by training systems (from procedures), and tested by CI (from scenarios) — without drift between any of them.

### 3.1 Inspiration — the Claude Plugin construct

Claude Code's plugin system, stabilised through 2025–2026, settled on a directory layout that cleanly separates deterministic mechanics from non-deterministic intelligence:

| Claude Plugin component | Role | Determinism |
|---|---|---|
| `.claude-plugin/plugin.json` | Manifest — identity, version, deps | Deterministic declarative |
| `hooks/` | Scripts firing on lifecycle events (pre/post tool, session start/end) | **Deterministic control** — no model in the loop |
| `.mcp.json` | MCP server connections (DBs, APIs, services) | Deterministic interface; underlying service may not be |
| `.lsp.json` | LSP server configs for code intelligence | Deterministic |
| `commands/` | Slash-command shortcuts | Deterministic invocation, non-deterministic body |
| `bin/` | Executables added to Bash PATH | Deterministic |
| `monitors/` | Background watchers | Deterministic triggers |
| `settings.json` | Defaults | Deterministic |
| `skills/<name>/SKILL.md` | Markdown procedural knowledge | **Non-deterministic** — read by the LLM |
| `agents/` | Subagent definitions (system prompts + scopes) | Non-deterministic body, deterministic configuration |
| `CLAUDE.md` | Project context | Non-deterministic context |

The genius of the construct is that **every plugin is simultaneously a deterministic engine and an LLM-readable artefact** — and authors can dial up or down which side of the boundary any given capability sits on. Yantra adopts this exact pattern, but with BFSI-grade notations on the deterministic side and a richer subagent model on the lifecycle side.

### 3.2 The construct — a deterministic spine with a non-deterministic surface

A ProcessSkill is best understood as two halves, both authored together, both required:

| Half | What lives here | Yantra component | Role at runtime |
|---|---|---|---|
| **Deterministic spine** | `manifest.yaml`, `decisions/*.dmn`, `process/*.bpmn`, `state/lifecycle.yaml`, `contracts/`, `helpers/*.py`, `templates/*.j2`, `hooks/`, `monitors/`, `tests/*.feature` | Executed by Camunda + DMN engine + Python sandbox + YantraGateway hooks + scheduler | The artefact a regulator audits; the source of truth for "what does this skill do" |
| **Non-deterministic surface** | `README.md`, `prompts/SKILL.md`, `prompts/system.txt`, `regulatory/annotations.md`, `subagents/*/system.md` | Loaded into LLM context by the LLM Adapter | The artefact the LLM reads when judgement is required between deterministic steps |

The deterministic spine **is the source**. The non-deterministic surface is **derived** — `prompts/SKILL.md` is auto-generated from manifest + BPMN summary + DMN rule narration + contract descriptions + template excerpts. The LLM-facing surface and the executable surface cannot drift, because one is compiled from the other.

### 3.3 Folder layout

```
<process-skill>/
├── .processkill/
│   ├── plugin.json                    # manifest in Claude-Plugin-compatible form
│   └── signatures/                    # Ed25519 over folder hash
│
├── manifest.yaml                      # canonical metadata — name, semver, shape,
│                                        owner, reviewers, sensitivity tier,
│                                        regulatory refs, jurisdictions,
│                                        compliance gates, escalation chain
├── README.md                          # human-readable prose (non-deterministic)
│
├── decisions/                         # DMN 1.5 — eligibility, pricing, KYC tier,
│   └── *.dmn                          #   fraud rules, claim approval, AML rating
│
├── process/                           # BPMN 2.0 — origination, claims, recon,
│   └── *.bpmn                         #   reporting, complaint handling
│
├── state/
│   └── lifecycle.yaml                 # state machine — entity lifecycle
│
├── contracts/
│   ├── inputs.schema.json
│   ├── outputs.schema.json
│   └── tools.openapi.yaml             # tools the skill calls (via YantraGateway)
│
├── helpers/                           # deterministic compute
│   └── *.py                           #   EMI, drawing-power, ECL, recon
│
├── templates/                         # compliance-approved customer comms
│   ├── kfs.j2                         #   Key Fact Statement (RBI Digital Lending)
│   ├── sanction-letter-{en,hi,...}.j2
│   └── recovery-notice.j2
│
├── hooks/                             # deterministic gates (analog of Claude hooks)
│   ├── pre-execute/                   #   consent-gate, kyc-tier-gate, dpdp-gate
│   ├── post-execute/                  #   audit-emit, redact, fpc-language-check
│   └── on-state-change/
│
├── monitors/                          # always-on background watchers
│   └── *.yaml                         #   reg-change-watch, rate-band-drift, etc.
│
├── tests/
│   ├── *.feature                      # Gherkin — Given/When/Then per rule
│   └── fixtures/
│
├── eval/
│   ├── rubric.yaml                    # outcomes-style rubric
│   └── adversarial.feature            # red-team scenarios
│
├── regulatory/
│   ├── refs.yaml                      # RBI/IRDAI/SEBI cites w/ as-of dates
│   └── annotations.md                 # interpretive notes
│
├── prompts/                           # auto-generated, non-deterministic surface
│   ├── SKILL.md                       #   the LLM-facing surface
│   │                                  #   (compatible with Anthropic/Cursor/Joule)
│   └── system.txt                     #   agent persona
│
├── subagents/                         # lifecycle subagents (§3.7)
│   ├── audit/
│   ├── test/
│   ├── train/
│   └── support/
│
└── .yantra/                           # build artefacts
    ├── compiled/                      #   compiled DMN, BPMN, schemas
    ├── canvas/                        #   rendered Process Canvas (§3.5)
    └── views/                         #   pre-rendered Preview / Code / Blame
```

### 3.4 Mapping Claude Plugin components onto ProcessSkill

A direct correspondence — useful both for understanding and for the Portability Layer (§11) that exports ProcessSkills back as Claude Plugins:

| Claude Plugin | ProcessSkill equivalent | Notes |
|---|---|---|
| `.claude-plugin/plugin.json` | `.processkill/plugin.json` + `manifest.yaml` | Manifest split: short JSON for plugin systems, rich YAML for the operational metadata |
| `skills/<name>/SKILL.md` | `prompts/SKILL.md` | Auto-generated from spine, not hand-authored |
| `commands/` | BPMN service tasks + slash-command bindings in manifest | A ProcessSkill installs as one or more slash commands |
| `agents/` | `subagents/{audit,test,train,support}` | The four lifecycle subagents — always present, always co-generated |
| `hooks/` | `hooks/{pre-execute,post-execute,on-state-change}` | Same deterministic-control role; BFSI compliance hooks pre-built |
| `.mcp.json` | `contracts/tools.openapi.yaml` | Pointer to YantraGateway-resolved tools, not raw MCP endpoints |
| `.lsp.json` | (handled by YantraAuthor / Yantra runtime) | Yantra's language services are platform-wide, not per-skill |
| `bin/` | `helpers/*.py` | Sandboxed Python helpers; no shell execution by design |
| `monitors/` | `monitors/*.yaml` | Same role — background watchers; declarative trigger + invocation |
| `settings.json` | Inside `manifest.yaml` | Single manifest, simpler authoring |
| `CLAUDE.md` | (lives at the ProcessRepo / institution level) | "Practice profile" is per-institution, not per-skill |

A ProcessSkill compiled with `yantrac export --target=claude-plugin` produces a fully valid Claude Plugin folder that Anthropic-runtime users can `/plugin install`. The reverse is partial — a Claude Plugin can be imported as a ProcessSkill scaffold, but the deterministic spine has to be authored to make it executable in Yantra's regulated runtime.

### 3.5 The Process Canvas — visual flow

A process is not understood by reading files. It is understood by *seeing* the flow. The Process Canvas is the visual view of a ProcessSkill — generated from the deterministic spine, not authored separately, so it cannot drift from the executable artefacts.

#### What it shows

```
       ┌──────────────────────────────────────────────────────────┐
       │                  P R O C E S S   C A N V A S             │
       │                                                          │
       │  ⓘ Lane: Process flow (BPMN spine, left-to-right)        │
       │  ▶─[KYC]─▶─◇─[BSA]─▶─◇─[Decision]─▶─[Memo]─▶─[Sanction]  │
       │       │      ▲                ▲           ▲              │
       │       │   pre-hooks       DMN preview   templates        │
       │       │   ┌──┴──┐         ┌───┴───┐                      │
       │       │   │consent│         │ 8 rules│                      │
       │       │   │ gate │         │ 3 shown │                      │
       │       │   └──┬──┘         │ open ▶ │                      │
       │       │      │             └────────┘                      │
       │  ⓘ Lane: State lifecycle                                  │
       │  [new]─▶[kyc-ok]─▶[bsa-ok]─▶[underwritten]─▶[sanctioned]  │
       │                                                          │
       │  ⓘ Lane: Data flow (contracts)                            │
       │  inputs.schema ▶ AA pull ▶ bureau pull ▶ DMN ▶ output    │
       │                                                          │
       │  ⓘ Lane: Tool calls (via YantraGateway)                   │
       │  [AA-Finvu] [Bureau-Experian] [GST-Karza] [eSign-NSDL]   │
       │                                                          │
       │  ⓘ Lane: Subagent observation points                      │
       │  audit ◯       test ◯ ◯ ◯ ◯       support ⚠              │
       │                                                          │
       │  Toggle: [✓ process] [✓ state] [✓ data] [ tools] [ hooks] │
       │  Click any node → drill into source artefact              │
       └──────────────────────────────────────────────────────────┘
```

#### How it works

The canvas is **derived**, not authored:

1. **Primary spine**: the BPMN flow renders via `bpmn-js`-derived components. Tasks, gateways, events, message flows — all from `process/*.bpmn`.
2. **Decision overlays**: every BPMN exclusive/inclusive gateway whose condition expression references a DMN file shows a *miniature DMN table preview* inline (top 3 rules, "see all 8 ▶") via `dmn-js` components. Clicking opens the full decision-table editor.
3. **State lane** (below): the `state/lifecycle.yaml` rendered as a horizontal state-flow band, aligned to the process tasks that cause transitions.
4. **Data lane** (below): inputs / intermediate data / outputs shown as a Sankey-like band, with each connection traceable to a contract schema.
5. **Tool lane** (below): every BPMN service task decorated with its YantraGateway target (MCP server badge + sensitivity tier color).
6. **Hook annotations**: pre/post-execute hooks shown as fuse-icons attached to the relevant tasks (consent-gate, aadhaar-mask-gate, fpc-language-check).
7. **Subagent observation points**: small badges showing where each lifecycle subagent (§3.7) hooks in — audit at every state transition, test at every gateway, support at every escape hatch, train at every customer-facing step.
8. **Layers are toggleable.** A business reader sees the spine + state; a compliance officer turns on hooks; an integration engineer turns on tool calls; an auditor turns on subagent observation.
9. **Click any node** → drill to source artefact (DMN editor, BPMN modeller, Python helper, template, OpenAPI definition).

#### Why this is novel

Existing tools approximate parts of this:
- **Camunda Modeler** has BPMN + DMN, but in separate windows.
- **LangGraph Studio** visualises state graphs but not BPMN + DMN composition.
- **Temporal UI** shows workflow execution traces but not the design-time composition.
- **Backstage** offers multi-view component pages but not for process specs.
- **Mermaid / draw.io** can render any individual notation but not the composed view.

The Process Canvas is the composition of BPMN spine + inline DMN previews + state lane + data lane + tool lane + hook annotations + subagent indicators in one auto-derived navigable view. To our knowledge no existing tool composes all of these from a single specification. It is one of Yantra's defensible UX assets.

The implementation: a SvelteKit / Next.js renderer that takes the ProcessSkill folder, parses each artefact, and composes the layered diagram via `bpmn-js` + `dmn-js` + custom D3 layers. Same renderer serves YantraAuthor (interactive), GitHub PR previews (read-only embed), Knowledge Graph entity pages, and the runtime documentation.

### 3.6 Multi-view rendering — Preview, Code, Canvas, Blame

A markdown file in modern editors is viewed in three ways: source, rendered preview, and Git blame. A ProcessSkill — composed of many files in many notations — needs the same affordance, extended.

Yantra renders every ProcessSkill in five views, **all derived from the same folder, always in sync, always one click apart:**

| View | Audience | What it shows |
|---|---|---|
| **Preview** *(default for humans)* | Business authors, reviewers, executives | Single scrolling page composing all components in readable order: manifest summary → "when to use" → Process Canvas → decisions (DMN rendered as tables) → state lifecycle (state diagram) → contracts (collapsible) → templates (rendered samples) → test scenarios → regulatory refs → signatures + audit info. The way a human reads a policy. |
| **Code** | Engineers, agents, debuggers | The raw folder: file tree on the left, file content on the right. What an agent's LLM Adapter ultimately sends to the model. The "code view" of the ProcessSkill. |
| **Canvas** | Process designers, compliance, auditors | The Process Canvas (§3.5). Visual. Layered. Interactive. |
| **Blame** | Reviewers, auditors, regulators | Line-by-line / rule-by-rule attribution: who authored, who reviewed, who signed, which Git commit, which signature. Clickable to that commit's full diff. The provenance view. |
| **Spec** | Programmatic consumers (CI, registry, portability exporters) | Normalised JSON description of the whole ProcessSkill — manifest + flattened DMN + BPMN sequence + contract refs + signature digests. Stable, versioned API surface for tools. |

**The Preview is the page that matters most.** It is the artefact a senior policy author shows the credit head; the artefact a compliance officer reviews; the artefact a regulator opens during inspection. All the underlying files are stored in their own folders (so each notation has its own native editor and validation), but the Preview composes them into a single, scrollable, anchor-linked page — the way a Notion page composes blocks, or a Jupyter notebook composes cells. Cross-references resolve as in-page anchors; clicking a DMN rule in the Preview jumps to that rule in the table view.

**Authors prompt; agents read code; humans read preview; regulators read blame; tools read spec.** Five audiences, five views, one source folder.

#### Implementation note

The renderer is a single Yantra service called from YantraAuthor, the Yantra runtime UI, the GitHub plugin (for PR preview), and the Knowledge Graph entity pages. View selection is a query parameter. Same compiled artefacts everywhere; same signatures; same data; different presentation.

### 3.7 The four lifecycle subagents — Audit, Test, Train, Support

Every ProcessSkill ships with four subagents — auto-generated at authoring time by ProcessMaster (§3.8) and kept in sync with the main skill as it evolves. Each subagent enables the **full lifecycle** of one role around the process. The author does not write four extra ProcessSkills; the author writes one, and the four subagents are produced and updated alongside.

This is the single most important productivity multiplier in the ProcessSkill construct: **every process comes with its own auditor, tester, trainer, and supporter from day one.**

#### The four subagents

```
<process-skill>/subagents/
├── audit/        # The full audit lifecycle
├── test/         # The full test lifecycle
├── train/        # The full training lifecycle
└── support/      # The full operational support lifecycle
```

Each folder contains:
- `system.md` — subagent persona, scope, escalation rules (the non-deterministic surface)
- `procedures/` — step-by-step playbooks for the role (BPMN where the lifecycle is sequential)
- `templates/` — outputs the subagent produces (audit reports, test reports, training material, runbooks)
- `eval/rubric.yaml` — outcomes rubric for the subagent's outputs
- Role-specific configuration files (see below)

#### 3.7.1 Audit subagent

Enables the complete audit lifecycle for the process this ProcessSkill governs:

- **Plan**: Generates an audit plan — sample selection, risk-weighted scope, methodology — based on the ProcessSkill's manifest, regulatory refs, and the runtime statistics from the Knowledge Graph (volumes, exception rates, override rates).
- **Execute**: Pulls WORM audit logs for the sampled instances, replays each through the ProcessSkill version that was live at the time of decision, compares actual outcome to replay outcome, classifies discrepancies.
- **Report**: Produces a structured audit report — exceptions, root-cause categories, ProcessSkill rule references, recommended changes — using `templates/audit-report.j2`.
- **Inspection support**: When a regulator inspection (RBI / IRDAI / SEBI) requests evidence for a specific decision, the audit subagent assembles the inspection pack — inputs, ProcessSkill version, DMN rule fired, LLM call records, signatures, approver, customer communications. Powers the `always-on/inspection-pack-builder` cookbook.
- Role-specific config: `audit/plan-template.yaml`, `audit/sampling-strategy.yaml`.

#### 3.7.2 Test subagent

Enables the complete testing lifecycle:

- **Plan**: Generates a test plan covering every DMN rule, every BPMN path (happy + exception), every state transition, every contract field, every adversarial scenario. Coverage targets configurable per skill shape.
- **Execute**: Runs the Gherkin scenarios + adversarial scenarios + generated edge-case scenarios in sandbox; integration-tests against test instances of YantraGateway MCPs.
- **Coverage analysis**: Reports DMN rule coverage (which rules fired in tests, which didn't), BPMN path coverage, contract field coverage. Refuses CI promotion below configured thresholds.
- **Regression detection**: Diffs current version's behaviour against the previous signed version on the standard test corpus; surfaces behavioural changes for reviewer attention.
- Role-specific config: `test/coverage.yaml`, `test/regression-baseline.yaml`.

#### 3.7.3 Train subagent

Enables the complete training lifecycle:

- **Generate training material** from `prompts/SKILL.md` + `templates/` + selected Gherkin scenarios — explainer videos (via text-to-video), interactive walkthroughs, quick-reference cards.
- **Onboarding curriculum**: Sequenced lessons for new ops staff joining the process — concept introduction, hands-on simulation, certification quiz.
- **Certification assessments**: Generated from the test subagent's scenarios with answers hidden; auto-graded; ties into the institution's LMS.
- **Knowledge-gap detection**: Watches override rates and exception clusters from the Knowledge Graph; when a specific failure mode recurs, generates a targeted micro-training and pushes to affected ops staff.
- Role-specific config: `train/curriculum.yaml`, `train/lms-integration.yaml`.

#### 3.7.4 Support subagent

Enables the complete operational support lifecycle:

- **Runbook generation**: Generates incident runbooks from BPMN exception paths and `hooks/` failure modes — what to do when `consent-gate` denies, what to do when AA TSP returns 5xx, what to do when DMN can't decide.
- **Incident triage**: When the process fails in production, the support subagent classifies the failure, identifies the responsible component (DMN rule mis-fired? hook tripped? upstream MCP timed out?), and routes to the right responder.
- **Escalation routing**: Drives the manifest's escalation chain — first-line ops, then product owner, then compliance, with timing and channel rules.
- **Customer query response**: When a customer asks "why did my loan application fail?", the support subagent reads the audit-trail-grade evidence and produces a regulator-compliant, customer-readable explanation (in the customer's language) without exposing PII or business logic. The "right to explanation" workflow.
- Role-specific config: `support/runbook.yaml`, `support/escalation.yaml`.

#### Why four, why these four

These four cover the full ALM (Application Lifecycle Management) of a process: *did it run correctly* (audit), *will it run correctly* (test), *can my people operate it* (train), *what happens when it fails* (support). Other roles (compliance, product evolution, performance optimisation) are handled at the platform level rather than per-skill. The four lifecycle subagents are the *minimum* viable set for every ProcessSkill in production; institutions can add custom subagents if they want, but these four are non-negotiable for production admission.

#### Co-generation, co-evolution

ProcessMaster (§3.8) generates the four subagents at the same time as the main ProcessSkill is authored — from the same source artefacts. When the main skill changes (a DMN rule added, a BPMN path altered, a regulatory ref updated), ProcessMaster re-derives the affected subagent procedures and presents the diffs for reviewer sign-off. The four subagents cannot drift from the main skill, because they are projections of it.

### 3.8 ProcessMaster — the authoring co-agent

ProcessMaster is the agent that sits with the author while a ProcessSkill is being created. It is itself a ProcessSkill — meta — living in ProcessRepo at `processrepo/meta/processmaster/`. It is invoked automatically whenever YantraAuthor opens a ProcessSkill folder for editing.

Two roles, simultaneously:
1. **Guide** — translate business intent into ProcessSkill artefacts as the author types.
2. **Lint** — keep what is produced compliant with the ProcessSkill specification.

#### The authoring loop

The author sees three panels in YantraAuthor:

```
┌─────────────────────────┬─────────────────────────────┬────────────────────────┐
│  PROMPT BOX             │  PREVIEW (live-updated)     │  PROCESS CANVAS (live) │
│  (transient)            │  (the produced ProcessSkill)│  (visual flow)         │
│                         │                             │                        │
│  Author writes in       │  Reads exactly as a senior  │  Shows the process     │
│  business language:     │  policy author would write  │  spine with decision   │
│                         │  in prose, with embedded    │  points + state lane + │
│  "Top-up eligibility    │  decision tables, BPMN      │  data lane updating in │
│  for a Personal Loan    │  diagram, KFS template      │  real time as the      │
│  customer with a        │  rendered, regulatory       │  author refines intent │
│  vintage of 12 months   │  refs cited inline.         │                        │
│  and no DPD in last 6   │                             │                        │
│  months, who has paid   │  Updates after each prompt. │                        │
│  ≥ 50% of original      │                             │                        │
│  loan, can be offered   │  Lint issues highlighted    │                        │
│  up to 30% top-up at    │  inline with severity:      │                        │
│  prevailing rate + 25   │   ⚠ ambiguous threshold     │                        │
│  bps. KFS in language   │   ⨯ missing test for rule 4 │                        │
│  of choice."            │   ⓘ similar skill exists    │                        │
│                         │                             │                        │
│  ProcessMaster ⟳        │                             │                        │
│  > "Should I treat       │                             │                        │
│  vintage as months       │                             │                        │
│  since first disbursal   │                             │                        │
│  or months since         │                             │                        │
│  account opening?"       │                             │                        │
│                         │                             │                        │
│  Author: "first          │                             │                        │
│  disbursal."             │                             │                        │
│                         │                             │                        │
│  ProcessMaster ⟳         │                             │                        │
│  > "Updated manifest +   │                             │                        │
│  decision table rule 1.  │                             │                        │
│  Also generated a test   │                             │                        │
│  scenario for the        │                             │                        │
│  vintage-boundary at     │                             │                        │
│  exactly 12 months."     │                             │                        │
└─────────────────────────┴─────────────────────────────┴────────────────────────┘
```

The prompts are **transient** — they scroll away, like any chat history. The Preview and the Canvas are **durable** — they update as ProcessMaster commits changes to the underlying artefacts. The author is never asked to write DMN XML or BPMN XML or YAML; the author writes intent in their domain language, and the ProcessSkill emerges fully-formed and spec-compliant.

#### ProcessMaster's capabilities

What ProcessMaster does, simultaneously and continuously, as the author writes:

| Capability | What it does |
|---|---|
| **Intent parsing** | Reads the author's business-language prompt; identifies what kind of artefact change is being requested (new rule, new path, new template, regulatory amendment, etc.) |
| **Artefact generation** | Drafts/updates `manifest.yaml`, `decisions/*.dmn`, `process/*.bpmn`, `contracts/*.json`, `templates/*.j2`, `tests/*.feature`, `regulatory/refs.yaml` — whatever the change touches |
| **Clarification** | Asks targeted questions when intent is ambiguous (the vintage example above) — never silently picks a default that the author has not affirmed |
| **Linting** | Continuously runs the `yantrac validate` suite; surfaces issues inline with severity and one-click fixes |
| **Pattern matching** | Looks for similar ProcessSkills in ProcessRepo; surfaces them as references — *"this looks similar to `core-verticals/credit-core/policy-check` — want to import 3 of its hooks?"* |
| **Regulatory grounding** | Cites RBI / IRDAI / SEBI as the author writes; flags references to repealed circulars; tracks supersession |
| **Test generation** | Generates Gherkin scenarios that exercise every new/changed rule, including boundary conditions |
| **Subagent regeneration** | Updates the four lifecycle subagents (§3.7) to reflect the change |
| **Anti-drift checks** | Verifies that the Preview, the Code, the Canvas, and the Spec views all reconcile after each edit |
| **Style enforcement** | Keeps the human-readable parts (`README.md`, `prompts/SKILL.md`) in the institution's prose style, learned from `CLAUDE.md`-style practice profile |

#### Why ProcessMaster lives in ProcessRepo, not in YantraAuthor

YantraAuthor is the *editor*. ProcessMaster is the *agent*. The distinction matters:

- **Authors choose their editor.** VS Code, web composer, Logseq, Obsidian — all of them call YantraAuthor's renderer + validator + ProcessMaster service.
- **ProcessMaster evolves separately from any editor.** New patterns added to ProcessRepo, new lint rules, new regulatory updates — ProcessMaster picks them up automatically because it lives in ProcessRepo and reads the latest spec + standards on every invocation.
- **ProcessMaster is the same artefact class as everything else it touches.** It is a ProcessSkill (meta). It has a manifest, a deterministic spine (lint rules, generators, validators), a non-deterministic surface (its conversational system prompt), four lifecycle subagents of its own (yes — the audit-subagent of ProcessMaster audits ProcessMaster's own authoring decisions), and a Process Canvas (yes — visualised).

This meta-recursion is intentional. It is how Yantra demonstrates internally that the ProcessSkill construct is general enough to describe *every* operational artefact in the platform — including the platform's own authoring agent. If ProcessMaster cannot be expressed as a ProcessSkill, the construct is not yet complete.

#### Naming convention

The pattern of "ProcessMaster" generalises. Future co-authoring agents may include:
- **GraphMaster** — co-authors the Knowledge Graph schema with an institution's data architect
- **GatewayMaster** — co-authors Tier-C MCP wrappers with an institution's integration engineer
- **DiscoverMaster** — runs the Yantra Discover engagement as a conversational agent

All are meta-ProcessSkills in ProcessRepo. All speak business language and produce deterministic artefacts. All lint as they generate.

### 3.9 The taxonomy of skill shapes

Not all ProcessSkills are the same. The shape determines tooling, testing rigour, the human-in-the-loop posture, and the four lifecycle subagents' default configurations:

| Shape | What it does | Example | HITL posture | Audit cadence |
|---|---|---|---|---|
| **Decisional** | Inputs → one outcome from an enumerated set | Top-up eligibility, fee waiver, KYC tier | Auto unless rubric flags | Monthly sample |
| **Review** | Scrutinises work against a checklist; human is final | Credit memo review, reg filing review | Always HITL | Quarterly trend |
| **Reconciliation** | Exact arithmetic against book of record | T+1 recon, IFRS 9 ECL, GST recon | Auto with exception-only HITL | Per-cycle full |
| **Generative collaboration** | Produces drafts a human iterates on | Pitch decks, RM briefs, board notes | Always HITL | Spot-check |
| **Retrieval-synthesis** | Pulls from many sources, summarises | Customer 360 brief, reg circular digest | Auto, audit trail | Monthly sample |
| **Orchestration** | Composes child ProcessSkills into long workflows | Loan origination end-to-end, period close | Configurable per child | Per-instance |

The manifest's `shape:` field is mandatory. ProcessMaster verifies that the artefacts produced are appropriate to the shape (a "Decisional" shape with no DMN file is a lint error; a "Generative collaboration" shape with no `eval/rubric.yaml` is a lint error). Each shape also configures sensible defaults for the four lifecycle subagents — a Decisional skill's audit subagent samples more aggressively; a Generative collaboration skill's train subagent invests more in onboarding curriculum.

### 3.10 Signing and trust

Every ProcessSkill is signed at three points in its lifecycle: by the **author** (Ed25519 over the folder hash), by the **reviewers** (named in the manifest — typically two: a domain SME and a compliance officer), and by the **publisher** (the institution or partner publishing the skill). The Yantra runtime refuses to load an unsigned or invalidly signed skill in production. The signatures are part of the audit trail — a regulator can verify that the skill that produced a specific 2027-Q1 decision was the version signed by these specific people on this date. The Blame view (§3.6) makes this provenance navigable.

### 3.11 What a ProcessSkill *is not*

- It is **not a prompt template**. Prompts are *outputs* of the ProcessSkill, not the substance.
- It is **not a workflow alone**. BPMN is one notation; the deterministic decisions and helper code are equal citizens.
- It is **not a fine-tune**. ProcessSkills are model-independent. The same ProcessSkill runs on Claude, GPT, Gemini, or an on-prem open-weight model behind the LLM Adapter (§7).
- It is **not a Claude Plugin** — it is a strict superset. A Claude Plugin focuses on tool/skill bundling for a coding assistant; a ProcessSkill is a regulated-operations artefact with deterministic enforceability, four lifecycle subagents, signed provenance, and a Process Canvas. A ProcessSkill *exports as* a Claude Plugin (§11) but is not equivalent to one.
- It is **not authored in files**. It is authored in *intent*, in conversation with ProcessMaster (§3.8). The files are the produced output, not the author's primary surface.
- It is **not vendor-proprietary metadata**. It is markdown + open-standard notation files, in Git, with a published spec. Portability is a feature, not an afterthought.

---

## 4. ProcessRepo — The Curated Industry Library

ProcessRepo is the canonical library of ProcessSkills for Indian financial services — the equivalent of SAP's industry-specific Best Practices content, except open, community-governed, and tuned to RBI/IRDAI/SEBI rather than SAP's product backlog.

### 4.1 Repository structure

```
processrepo/
├── .marketplace.json                   # registry of available ProcessSkills
├── docs/
├── core-verticals/
│   ├── credit-core/                    # the foundational vertical — used by all lenders
│   │   ├── bank-statement-analysis/
│   │   ├── gst-cashflow-analytics/
│   │   ├── bureau-interpretation/
│   │   ├── policy-check/
│   │   ├── fraud-triangulation/
│   │   └── credit-memo-writer/
│   ├── compliance-core/                # KYC tiers, FPC, Digital Lending,
│   │   ├── ckyc-tier-assignment/         DPDP, AML — used by every customer-touching skill
│   │   ├── fpc-language-checker/
│   │   ├── digital-lending-kfs/
│   │   ├── dpdp-purpose-gate/
│   │   └── aml-risk-rating/
│   └── communications-core/            # KFS, sanction letters, recovery comms
│       ├── kfs-generator/
│       ├── sanction-letter-multi-lang/
│       └── recovery-notice-jurisdiction-aware/
├── product-lines/
│   ├── msme-unsecured/
│   ├── msme-secured/
│   ├── gold-loan/
│   ├── vehicle-loan-new/
│   ├── vehicle-loan-used/
│   ├── lap/
│   ├── personal-loan/
│   ├── co-lending/
│   ├── credit-card/
│   └── microfinance-jlg/
├── functions/                          # cross-product process functions
│   ├── kyc-vcip/
│   ├── underwriting/
│   ├── pricing-fees/
│   ├── disbursal/
│   ├── collections-early-bucket/
│   ├── collections-late-bucket/
│   ├── recovery-legal/
│   ├── npa-classification-iracp/
│   ├── ecl-ifrs9/
│   ├── fraud-realtime/
│   └── regulatory-filing/
├── always-on/                          # long-running scheduled ProcessSkills
│   ├── reg-monitor/                    # RBI/IRDAI/SEBI/state circulars
│   ├── npa-early-warning/
│   ├── collections-router/
│   ├── fraud-cluster-watcher/
│   ├── audit-trail-generator/
│   └── inspection-pack-builder/        # bundles WORM evidence for RBI inspection
├── partner-built/                      # vendor-authored, vendor-signed
│   ├── karza/                          # KYC, GST, MCA
│   ├── perfios/                        # bank-statement analysis
│   ├── credgenics/                     # collections orchestration
│   ├── hyperverge/                     # liveness, face match, fraud
│   ├── signzy/                         # KYC + V-CIP
│   └── ...
├── meta/                               # meta-ProcessSkills — the platform's own agents
│   ├── processmaster/                  # the authoring co-agent (§3.8)
│   ├── graphmaster/                    # Knowledge Graph co-author
│   ├── gatewaymaster/                  # Tier-C MCP wrapping co-author
│   └── discovermaster/                 # Yantra Discover conversational engagement
└── scripts/
    ├── validate.py                     # the same validator that powers LiveAid
    ├── compile-skill-md.py             # generates Anthropic-compatible surface
    ├── deploy-managed.sh               # publishes always-on cookbooks
    └── publish-marketplace.py
```

### 4.2 Governance — three classes of artefact

| Class | Owner | Licence | Review |
|---|---|---|---|
| **Core verticals** | Yantra Standards Body (foundation seats: IBA, FACE, IDRBT, NPCI, 2 banks, 2 NBFCs, Yantra) | Apache-2.0 | Skills Steering Committee with ≥ 1 RBI/IDRBT-credentialed reviewer |
| **Partner-built** | Vendor (Karza, Perfios, etc.) | Vendor's choice | Vendor-signed; passes Yantra validator; trust-layer scan on install |
| **Customer-private** | The institution | Closed | Never enters ProcessRepo unless explicitly contributed back, anonymised |

The Yantra runtime applies a **trust layer** to every install: security scan, license check, signature verify, freshness check (skills with regulatory refs older than 6 months without explicit acknowledgement are blocked from production). Same model as software supply-chain security, applied to operational logic.

### 4.3 Why ProcessRepo matters strategically

A new NBFC adopting Yantra does not start from a blank slate. It installs the core verticals (credit-core, compliance-core, communications-core) and the product-line packs for what it lends against; the install delivers ~80% of typical functionality on day one. The remaining 20% is institution-specific — and that's where ProcessSkills are *authored*, by the institution's senior policy people, using YantraAuthor in conversation with ProcessMaster (§3.8).

This 80/20 starting point is the difference between an "agentic ERP" project costing ₹2Cr and 4 months, versus ₹50Cr and 18 months. ProcessRepo is what makes adoption tractable.

---

## 5. YantraGateway — The Integration Layer

YantraGateway is what SAP's BTP + MCP server registry is to S/4HANA: the single governed door through which every ProcessSkill reaches the outside world. Every tool call, every system integration, every external API goes through it. Authentication, authorisation, rate-limiting, audit, redaction, transformation — applied uniformly across hundreds of integrations.

### 5.1 Architecture

YantraGateway is an MCP-native gateway derived from open-source components (Kong, agentgateway, or IBM ContextForge as candidates) with Yantra-specific plugins layered on top:

```
ProcessSkill
    │
    │  invokes tool via contracts/tools.openapi.yaml
    ▼
┌─────────────────────────────────────────────────────────┐
│                    YantraGateway                        │
│  ┌─────────┐ ┌─────────┐ ┌──────────┐ ┌──────────────┐ │
│  │ AuthN/Z │ │ Consent │ │ Redact   │ │ Rate-limit + │ │
│  │ Plugin  │ │ Gate    │ │ (PII,    │ │ Cost cap     │ │
│  │ (Entra/ │ │ (AA,    │ │ Aadhaar) │ │              │ │
│  │ Okta)   │ │ DPDP)   │ │          │ │              │ │
│  └─────────┘ └─────────┘ └──────────┘ └──────────────┘ │
│  ┌─────────┐ ┌─────────┐ ┌──────────┐ ┌──────────────┐ │
│  │ Audit   │ │ Idempo- │ │ Schema   │ │ MCP Server   │ │
│  │ → WORM  │ │ tency   │ │ Validate │ │ Discovery    │ │
│  └─────────┘ └─────────┘ └──────────┘ └──────────────┘ │
└─────────────────────┬───────────────────────────────────┘
                      │
   ┌──────────────────┼──────────────────┐
   ▼                  ▼                  ▼
DPI MCPs        Commercial MCPs     Customer-system MCPs
(Tier A)        (Tier B)            (Tier C)
```

### 5.2 The MCP fleet

Yantra ships MCPs for the gravity wells of Indian financial data. Customers do not build these; they consume them.

**Tier A — Digital Public Infrastructure (foundational):**

| Capability | Underlying | MCP responsibilities |
|---|---|---|
| Identity / eKYC | Aadhaar (UIDAI), DigiLocker, CKYCR | Tokenised pull, OTP flow, V-CIP orchestration, masking |
| Account Aggregator | Sahamati / Finvu / OneMoney / CAMSFinServ / NADL | FIU registration, consent flow, FIP routing, data pull, schema normalisation |
| Tax / GST | GSTN via GSPs (Karza / Perfios / ClearTax) | GSTR-1/3B/e-invoice, returns, taxpayer profile |
| Credit bureau | CIBIL TU, Experian India, Equifax India, CRIF Highmark | Bureau pull, error parsing, dispute handling |
| e-Sign | NSDL / CDSL e-sign | Aadhaar e-sign orchestration, audit |
| Repayment rails | NACH (NPCI), eMandate, UPI AutoPay, BBPS | Mandate creation, debit, presentation, response codes |
| Property records | State IGRS portals, CERSAI | Title search, encumbrance check, charge filing |
| Vehicle records | VAHAN, Parivahan | RC verification, hypothecation |
| Court / litigation | eCourts, NCLT, IBBI | Borrower litigation check |
| AML / fraud | FIU-IND, SACHET, sanctions lists | STR filing, PEP screening |
| Regulatory feeds | RBI, IRDAI, SEBI press releases + master directions | Subscription, diff, classification |

**Tier B — Commercial data and ops:**

Karza, Perfios, FinBox, Scoreme, Bureau (the company), Bridge Fintech, Hyperverge, Signzy, IDfy, Yubi (CredAvenue), CredGenics, Spocto, Creditas, IBJA gold rate feed, used-car valuation (Cars24/CarTrade/IMV), property valuation (PropEquity/Liases Foras), WhatsApp Business API (Gupshup/AiSensy), Exotel/Knowlarity (voice), MSG91/Karix (SMS).

**Tier C — Customer-specific systems:**

Built per customer as part of the engagement. Wraps the customer's existing LOS (Lentra/Pennant/Nucleus FinnOne/Newgen/Cloud-Lending), LMS, core banking (BaNCS/Finacle/Flexcube), BRE (FICO/Pega/Provenir/Lentra Decision Matrix), collections platform, CRM, communication stack. The Tier C wrappers are how Yantra integrates with the institution's existing investment — not by replacing those systems, but by exposing them as MCPs the ProcessSkill consumes.

### 5.3 Why YantraGateway is its own product

Every other component in the platform talks through YantraGateway to do anything externally. That makes it the **trust boundary** of the enterprise — the place a regulator audits, the place a security team monitors, the place the institution sets cost and rate-limit policy. Building it as a separate, governed component (rather than letting agents call APIs directly) is the difference between a defensible production deployment and a demo.

---

## 6. YantraAuthor — The Implementation Workbench

YantraAuthor is to Yantra what Joule Studio is to SAP Autonomous Enterprise: the workbench in which agents and ProcessSkills are configured, simulated, deployed, and governed. It is the editor surface; the intelligence behind authoring lives in ProcessMaster (§3.8), which YantraAuthor invokes on every keystroke.

### 6.1 YantraAuthor — surfaces

YantraAuthor meets authors where they work. Three surfaces, one renderer, one validator, one ProcessMaster:

| Surface | Audience | Primary use |
|---|---|---|
| **Web composer** *(primary)* | Senior policy authors, compliance officers, risk heads | Browser-based; three-panel layout (Prompt + Preview + Canvas, §3.8); no install; all five views (§3.6) |
| **VS Code extension** | Engineers, BAs, technical credit officers | Pro-code authoring, Git workflow, LSP-driven validation; same Preview / Canvas / Blame views as panels |
| **Logseq / Obsidian plugin** | Authors who already work in markdown-knowledge tools | Lower-friction starting point; promotes to web composer for visual editing |

All three surfaces call the same renderer (which produces Preview / Code / Canvas / Blame / Spec views), the same `yantrac` validator, and the same ProcessMaster agent in ProcessRepo. The author's choice of editor is a preference; the platform behaviour is identical across them.

### 6.2 What YantraAuthor provides

- **The three-panel authoring loop** (Prompt + Preview + Canvas) described in §3.8 — author works in business language; ProcessMaster produces spec-compliant artefacts; Preview and Canvas update live.
- **Direct artefact editors** for power users who want to edit DMN tables, BPMN diagrams, Gherkin scenarios, or templates without going through the prompt loop: a Camunda-Modeler-derived BPMN canvas, a `dmn-js`-derived decision-table editor, a state-machine editor, a Gherkin authoring pane, a Jinja template editor with safety checks.
- **Simulator**: replays a ProcessSkill against anonymised historical cases from the customer's portfolio (loaded via Yantra Discover, §13); shows decision distributions, exception classes, edge cases, what-if comparisons across versions.
- **Regulatory grounding panel**: cite-as-you-write — selecting any rule offers RBI / IRDAI / SEBI references from the Knowledge Graph corpus; flags repealed circulars; tracks supersession.
- **Diff & review**: PR-style reviews; named reviewers from manifest auto-notified; sign-off captured as Ed25519 signatures recorded in `.processkill/signatures/`.
- **Promotion pipeline**: dev → simulation → shadow → A/B → production, with named gates and quality bars per stage; the four lifecycle subagents (§3.7) gate promotion (test coverage minimum, audit replay clean, training material generated, support runbook complete).
- **Subagent panel**: dedicated view for the four lifecycle subagents — audit, test, train, support — showing their generated procedures and templates with the author able to override defaults.

### 6.3 The validator, runtime-uniform

The `yantrac` validator is the spec enforcement engine. It runs in four places, identical behaviour:

1. **In YantraAuthor (LSP)**: inline as the author writes; sub-second feedback on syntax, structure, manifest completeness, broken refs.
2. **Pre-commit**: full validator pass — DMN completeness (no missing rules under Unique hit policy), no overlaps, FEEL parses, test-per-rule coverage, contract-input ↔ DMN-input ↔ Gherkin-given consistency, regulatory-ref freshness, subagent presence.
3. **CI**: same validator in the customer's GitHub Action.
4. **Runtime admission control**: the Yantra runtime refuses to load a ProcessSkill that does not pass validation.

ProcessMaster (§3.8) calls the validator continuously and surfaces lint issues inline. The validator is the rule enforcer; ProcessMaster is the rule-aware co-author. Both read the same spec.

### 6.4 The scenario library

YantraAuthor surfaces a **scenario library** — high-quality, deeply annotated examples drawn from ProcessRepo and from anonymised industry practice — that authors browse, fork, and learn from. A library of 30 well-curated examples teaches a new author more than 500 mediocre ones. The library is the institution's onboarding asset for elevated senior authors and a key reason new authors reach productive output within weeks rather than months.

### 6.5 The author as first-class engineer

The single largest organisational change Yantra requires is the elevation of the senior policy author — the credit-policy expert, the compliance writer, the senior process designer — to first-class engineering authorship. In most Indian institutions today, this person is paid less than a senior software engineer and treated as upstream of those who build systems. In a Yantra deployment, the policy author *is* the engineer; the artefact they produce is the deployable asset.

YantraAuthor + ProcessMaster + the four lifecycle subagents make this elevation real. The author gets a conversation-driven authoring loop (so they author in their language), continuous lint (so the output is spec-compliant), live Preview and Canvas (so they see what they are producing), simulation against real portfolio data (so they validate before promoting), and auto-generated audit / test / train / support subagents (so they don't need a separate team to operationalise the work). The implication for the customer's HR posture is significant — the senior policy author becomes the highest-leverage role in operations. Yantra's services arm sells this transformation as much as it sells the platform.

---

## 7. The LLM Adapter — Multi-Model by Design

Yantra is not a Claude application or a Gemini application or an Azure OpenAI application. It is a platform that selects the right model for each piece of work, with the model being a *replaceable component* sitting behind an adapter.

### 7.1 Why an adapter is non-negotiable

Three structural reasons:

1. **Sensitivity tiering.** A board-confidential credit memo cannot be reasoned over by an offshore-hosted frontier model in the same way a customer-greeting can. The adapter routes by sensitivity tier declared in the ProcessSkill manifest.
2. **Sovereignty.** Some workloads must remain on-prem (board, M&A, deep portfolio analysis). The adapter routes to a local Llama / Mixtral / Sarvam instance for those, and to Bedrock-Mumbai or Foundry-India for others.
3. **Vendor independence.** Anthropic, OpenAI, Google, and the Indian model labs (Sarvam, Krutrim, BharatGen, AI4Bharat) are moving fast and unpredictably. The institution that bet exclusively on any one of them in 2024 has been wrong twice already. The adapter preserves optionality.

### 7.2 What the adapter does

The adapter (built on LiteLLM as a foundation, with Yantra-specific policy) provides:

- **Routing** by sensitivity tier, latency budget, cost budget, and capability requirement (vision, long-context, tool-use, Indic language)
- **Failover** when a model degrades or is unavailable
- **PII redaction** before egress to external models
- **Audit logging** of every call (prompt, response, model version, tokens, cost, latency) to WORM storage
- **Cost telemetry** per ProcessSkill, per agent, per process instance — answers the "what is this costing me" question instantly
- **Eval traffic mirroring** — periodically run the same prompt on a comparator model to detect drift

### 7.3 The model menu (May 2026 anchor)

A typical large NBFC deployment uses 4–6 models:

| Tier | Sensitivity | Default model | Why |
|---|---|---|---|
| 0 | Board-confidential, M&A | On-prem Llama 3.x 70B / Mixtral 8x22B | Never leaves the data centre |
| 1 | Customer PII, credit decisions | Claude Opus 4.7 on Bedrock-Mumbai or Foundry-India | Best reasoning + India residency |
| 2 | Internal analytics, ops | Claude Sonnet on Bedrock-Mumbai or GPT-4.x on Azure-India | Cost / latency balance |
| 3 | Document extraction, OCR | Claude Haiku / Gemini Flash | Cheap, fast |
| 4 | Indic customer-facing (Hindi + 6 vernaculars) | Sarvam-2 / Krutrim / fine-tuned Gemma | Language quality |
| 5 | Embeddings / classification | Open-source on-prem | Cost at scale |

The institution's sensitivity policy is declared in `manifest.compliance.tier`. The adapter enforces. The audit log captures. Switching a tier-1 default from Claude to GPT to Gemini, or vice versa, is a configuration change — not a re-platforming.

---

## 8. Deterministic Engines — Where the LLM Doesn't Belong

A surprising amount of what enterprise software does is **not LLM work**. EMI calculation, drawing-power computation, GST reconciliation, IFRS-9 ECL, decision-table evaluation, BPMN workflow execution — these are deterministic. They must produce the same answer twice, every time, for a regulator. The LLM is the *wrong* tool for them.

Yantra explicitly integrates and orchestrates deterministic engines:

| Engine | Role | What runs there |
|---|---|---|
| **Camunda 8** | Durable workflow execution (BPMN) | Long-running processes — loan origination, claims, period close — that span hours/days/weeks with retries, escalations, idempotency |
| **DMN engine** (Camunda DMN or Drools) | Decision evaluation | Eligibility, pricing, fee waiver, KYC tier, fraud rules. The DMN files in ProcessSkill's `decisions/` are executed deterministically |
| **Optional commercial BRMS** (FICO Blaze, Pega, Provenir) | Heavy decision/scoring workloads | Where the customer already runs one — Yantra calls it via MCP; does not replace |
| **Python helper runtime** | Pure arithmetic | EMI, ECL, drawing-power, recon — runs in a sandboxed Python interpreter; helpers are unit-tested in CI |
| **Vector / RAG store** | Retrieval grounding | Knowledge Graph (§9) consults this; LLM Adapter receives grounded context |

Camunda 8.9 (April 2026) made the "BPMN activity = LLM tool" pattern first-class — each BPMN activity in an ad-hoc sub-process can be an LLM call, with the activity name and documentation telling the model what tool it is. Yantra adopts this pattern: BPMN holds the *durable shape* of the process; LLMs handle the *non-enumerable judgement* steps within it; deterministic engines handle the rest. The ProcessSkill's `process/*.bpmn` is what Camunda runs.

The composition rule: **deterministic where enumerable, LLM where not, and never the wrong way around.** This is what makes Yantra defensible to a regulator — and what most "AI agent" projects fail at.

---

## 9. Yantra Knowledge Graph — The Business-Context Substrate

SAP's Business AI Platform put the **Knowledge Graph** at the core for a reason. An agent cannot reason about an institution's operations without a structured map of its entities (customers, products, accounts, branches, RMs), its processes (origination, servicing, collections), its policies (credit policy, FPC, AML), its data (records, documents, events), and its relationships (this customer holds these products, originated by this RM, under this branch's portfolio).

Yantra Knowledge Graph is this structured map for an Indian financial institution. It is built collaboratively during Yantra Discover (§13) and maintained continuously by the platform.

### 9.1 What lives in the Knowledge Graph

- **Entities**: customer, product, account, application, loan, branch, RM, channel, partner, employee, agent (the AI kind), policy, regulation
- **Processes**: ordered references to ProcessSkills that span entities; their typical execution paths, exceptions, KPIs
- **Policies**: which ProcessSkill governs which entity-class under which condition
- **Regulations**: the corpus of RBI master directions, IRDAI circulars, SEBI rules, with version semantics ("as of 2026-04-30"), supersession chains, and mapped impact (which ProcessSkills are affected)
- **Data**: schema map of source systems (LOS tables, LMS tables, CBS, BRE configs) with lineage to entities
- **Events**: typed business events (application created, KYC completed, sanction issued, disbursal done, EMI bounced, NPA classified) with publishers and subscribers
- **Relationships**: the typed edges that make queries possible — *which RM originated this NPA cluster, what was the policy version at that time, what ProcessSkill version evaluated it, what did the LLM see, what did the human override*

### 9.2 Why this is differentiated

Two reasons the Indian-FS-specific Knowledge Graph is hard to build and hard to replicate:

1. **The regulatory corpus is institution-shared but interpretation-specific.** Every NBFC reads the same RBI Master Direction on Digital Lending, but the *interpretation* — what counts as a Lending Service Provider, what cooling-off period applies, what language qualifies for "loan agreement in language understood by borrower" — varies by institution. The Knowledge Graph carries Yantra-curated baseline interpretations + institution-specific overrides, both versioned, both auditable.

2. **The data graph crosses systems that don't share schemas.** LOS, LMS, CBS, BRE, collections platform, CRM, communication stack — each has its own data model. The Knowledge Graph normalises them. This work is what Yantra Discover (§13) automates.

### 9.3 How the Knowledge Graph is used at runtime

Every ProcessSkill invocation:
- pulls relevant entities and their attributes from the graph (current customer, product, applicable policy version)
- grounds the LLM Adapter call with the relevant regulatory references
- writes events back to the graph so subsequent ProcessSkills (in the same or later processes) see fresh state
- enables cross-process queries — *"which loans approved this quarter were under policy version X.Y?"* — that the regulator will ask

---

## 10. Identity, Authentication, Authorisation, and Sovereign Hosting

Yantra does not reinvent identity. It uses the institution's existing identity provider (Microsoft Entra, Okta, Google Workspace, or on-prem AD) for human authentication, and the patterns established by Microsoft's Agent Governance Toolkit and Google's Agent Identity for *agent* authentication.

### 10.1 Human identity

Every Yantra user — author, reviewer, approver, ops user, administrator — logs in via the institution's IdP. SSO is the default; MFA is enforced by the IdP, not by Yantra. Authorisation (who can author what, who can approve what, who can deploy what) is a Yantra concern, expressed through roles tied to IdP groups.

The login experience: **"Sign in with Microsoft" / "Sign in with Google" / "Sign in with your corporate SSO"** — the institution's choice, transparent to Yantra.

### 10.2 Agent identity

Every agent and every ProcessSkill has a cryptographic identity (Ed25519). This is the model Microsoft Agent Governance Toolkit (Apr 2026) and Google ADK Agent Identity converged on, and Yantra adopts. The identity:
- signs every action the agent takes (audit-trail-grade non-repudiation)
- gates which tools (via YantraGateway) the agent can call
- gates which data (in the Knowledge Graph) the agent can read
- expires and rotates on a policy-driven schedule

The agent identity is also what makes **Portability (§11)** clean: an agent's identity is the durable handle by which it is recognised across runtimes.

### 10.3 Sovereign hosting

Indian financial institutions have data-residency obligations that no global hyperscaler region by itself fully satisfies for all workloads. Yantra is **deployment-agnostic but Indian-region-default**. Three deployment modes:

| Mode | Substrate | When |
|---|---|---|
| **Yantra Cloud (managed)** | Yotta Shakti Cloud / Airtel Cloud / Ashoka Cloud / NTT Cloud / AWS Mumbai / Foundry-India | NBFCs that want managed, regulated-but-not-on-prem |
| **Yantra Sovereign (managed on partner sovereign cloud)** | One named Indian-sovereign provider per customer | Banks and large NBFCs with explicit sovereign-cloud mandates |
| **Yantra Self-Managed** | Customer's data centre or VPC | Top-tier banks, conglomerate parents, the most sensitive workloads |

### 10.4 Sovereign cloud partners

Indian sovereign-cloud providers — **Yotta, Airtel Cloud, Ashoka Cloud, NTT-India** — are **partners**, not competitors. The relationship works in both directions:

- **For Yantra**: sovereign cloud capacity in Indian data centres with appropriate certifications (CERT-In, MeitY empanelment), without building data centres ourselves.
- **For the cloud providers**: Yantra is their *go-to-market into Indian financial services*. Selling raw compute and storage to a bank's CIO is hard; selling "the agentic ERP for financial services, running on our sovereign cloud" is materially easier. Yotta's existing IBM partnership (announced May 7, 2026) signals exactly this play; Yantra is the BFSI-specialised analogue.

The strategic implication: Yantra has multiple sovereign-cloud relationships, not one exclusive. This preserves negotiating leverage and lets each cloud partner sell into customers they have existing relationships with.

---

## 11. Portability — Yantra Agents on Other Runtimes

The institution's operational logic, encoded as ProcessSkills, is the institution's asset. The runtime is an implementation choice. Yantra is built so the same ProcessSkill can also run on Microsoft Agent Framework, Google ADK, Anthropic Managed Agents, SAP Joule Studio, or any A2A-compatible runtime — with Yantra Portability Layer producing the right artefact for each.

### 11.1 Why this matters

Three reasons:
1. **Customer choice.** A bank that has already standardised on Microsoft Agent Framework should still be able to author in ProcessSkill format and deploy where their controls already live.
2. **No-platform-lock-in promise.** Yantra's standing pitch is "your operational logic is your asset, not our hostage." Portability is what makes that pitch real.
3. **Distribution.** Each external runtime is a distribution channel. ProcessSkills published to Anthropic's plugin marketplace, MS Agent Framework's gallery, Google's ADK registry, and SAP's Joule Studio simultaneously increases reach without per-channel custom work.

### 11.2 Compatibility matrix

| ProcessSkill component | Anthropic (Cowork / Managed Agents) | Microsoft Agent Framework | Google ADK | SAP Joule Studio |
|---|---|---|---|---|
| `prompts/SKILL.md` | Loads as Skill | Loads as agent skill | Loads as ADK skill descriptor | Loads as Joule skill |
| `decisions/*.dmn` | Tool callout to Yantra DMN endpoint | Tool callout to Yantra DMN endpoint | Tool callout | Tool callout |
| `process/*.bpmn` | Tool callout to embedded Camunda | Logic Apps wrapper or Camunda callout | Workflows wrapper or Camunda callout | Joule orchestration wraps Camunda |
| `contracts/tools.openapi.yaml` | MCP server registration | Agent Framework tool registration | ADK tool registration | Joule Studio tool registration |
| `templates/*.j2` | Inline | Content Safety + custom action | Vertex Content Safety | Joule template |
| `eval/rubric.yaml` | Outcomes rubric (1:1) | App Insights eval | Vertex Eval | Joule Studio eval |
| Agent identity | Anthropic-native | Microsoft Entra Agent ID | Google Agent Identity | SAP Identity |

### 11.3 The export tool

`yantrac export --target=msaf|adk|anthropic|joule|a2a <process-skill>`

Produces the target-runtime-specific artefact bundle. Includes:
- The runtime's manifest format
- The right wrapper for tool calls back to YantraGateway (so external-runtime agents still get the India-DPI fleet)
- The right wrapper for DMN/BPMN execution (still backed by Yantra-hosted engines)
- The right identity binding

The institution can run the same ProcessSkill version 1.7 on Yantra Cloud for its retail portfolio and on Microsoft Agent Framework for its in-Outlook ops queries — both with one source of truth in Git.

---

## 12. Agent Runtime — How a ProcessSkill Actually Executes

When an agent built from a ProcessSkill runs (whether on Yantra's own runtime or via the Portability Layer on another), three orchestration layers compose:

| Layer | Time-scale | What runs there |
|---|---|---|
| **Agent reasoning** | seconds | The LLM Adapter selects a model; the model reasons over `prompts/SKILL.md` + Knowledge Graph context; chooses next tool |
| **Durable workflow** | hours – weeks | Camunda 8 holds the BPMN state — approval queues, retries, idempotency, escalations |
| **Tool invocation** | per-call | YantraGateway authN/Z's, audits, redacts, rate-limits, calls the MCP server |

The same ProcessSkill composes all three layers. Its `prompts/SKILL.md` tells the reasoning layer when to load it. Its embedded `process/*.bpmn` tells the workflow engine the durable shape. Its `decisions/*.dmn`, `helpers/*.py`, and `templates/*.j2` are tools the workflow invokes via the gateway. The coherence comes from the ProcessSkill being the *shared specification* across all three layers.

For a customer engagement, this looks like: an underwriter clicks "Run underwriting" on a loan application. The Knowledge Graph hydrates the customer, product, applicable policy version. The runtime loads `processrepo/product-lines/msme-secured/underwriting`. Camunda kicks off the BPMN. Sub-tasks fan out — bureau pull, AA-based BSA, GST cash-flow analysis, fraud triangulation, property valuation — each as a parallel agent with its own identity, each calling YantraGateway, each grounded in the Knowledge Graph. The lead reasoning agent composes findings, runs `decisions/eligibility.dmn` deterministically, drafts the credit memo via `templates/credit-memo.j2`, and presents to the underwriter. The Outcomes rubric scores the memo before presentation; if it fails, another loop. Every step is audited; every model call is logged; every signature is captured. Total elapsed time: minutes for the AA / bureau / GST work, longer for the property piece. Underwriter time: review and approve, not assemble.

---

## 13. Yantra Discover — How Every Engagement Begins

No institution starts a Yantra deployment with a blank slate. They have decades of operational logic — in policy documents, system configurations, training material, ticket logs, audit trails, employee heads. **Yantra Discover** is the automated process-audit-and-migration phase that converts that reality into a starting point.

This is the most underrated component of the platform. It is the difference between a Yantra engagement that takes 6 weeks to first production use, and one that takes 18 months.

### 13.1 What Yantra Discover does

Over a structured 3–6 week sprint, Yantra Discover:

1. **Ingests existing artefacts.** Policy documents (PDFs, Word, Confluence). Process manuals. Training material. SOPs. Vendor product configurations (LOS rules, BRE decision tables, BPMN diagrams in existing tools). Code (where credit policy is encoded directly in Java/Python — common in older shops). Ticket logs from JIRA / ServiceNow showing exception classes. Audit trails showing actual execution patterns.

2. **Observes live operations.** Screen recordings (consented) of senior ops staff running the actual process. Voice recordings (consented) of credit committee meetings showing how decisions are *actually* made. System telemetry showing which fields are read, which buttons are pressed, in what order.

3. **Interviews senior staff.** AI-assisted structured interviews capturing tacit knowledge — *what does an experienced underwriter look at first? what does "the way we've always done it" actually mean here? where do you override the system and why?*

4. **Performs process mining** on transactional logs from LOS/LMS/CBS to derive the **actual** as-is process — not the documented one. Path frequencies, cycle times, rework loops, exception clusters.

5. **Produces a structured as-is map**, in machine-readable form, of:
   - Every operational process the institution runs, with BPMN scaffolds
   - Every decision the institution makes, with DMN scaffolds (rules inferred + flagged for review)
   - Every system the institution touches, with OpenAPI inferred from observed traffic
   - Every regulation cited or implied, mapped to the corpus
   - Every entity, attribute, and relationship — populating the initial Knowledge Graph
   - Every data store, with schema, lineage, freshness, and PII classification

6. **Generates a gap analysis** versus ProcessRepo:
   - Which processes are well-covered by core verticals and product-line packs (the 80%)
   - Which need institution-specific ProcessSkill authoring (the 20%)
   - Which existing systems need MCP wrappers (the Tier C set)
   - Which regulatory areas are insufficiently documented at the institution
   - Which processes show drift between document, system, and practice — the *most valuable output* of Discover

7. **Produces a migration plan**:
   - Sequencing: which workflows to migrate first (typically: review-shaped, low risk, high volume)
   - Data migration: how the institution's historical loan / customer / transaction data maps into the Knowledge Graph
   - Coexistence: which existing systems Yantra wraps (most) vs replaces (few — typically scattered Excel-and-tribal-knowledge processes)
   - Resource plan: which senior policy authors need to be elevated and how much protected time they need

### 13.2 What Yantra Discover *is* (and is not)

It is **not a Big-4 consulting engagement.** Those take 6 months and produce slide decks. Yantra Discover takes 3–6 weeks and produces machine-readable artefacts that load into YantraAuthor on day one of the next phase. The deliverable is the starting point of the engagement, not a report about it.

It is **not process mining alone.** Process-mining tools (Celonis, UiPath Process Mining, ABBYY Timeline) tell you what happened in your transaction logs. Discover does that *plus* document understanding, *plus* interview synthesis, *plus* regulatory mapping, *plus* code/configuration extraction — and produces a target-architecture artefact (ProcessSkill scaffolds) rather than a dashboard.

It is **not document AI alone.** Document-AI tools (Hyperverge, Karza, Vidya AI) extract structured data from individual documents. Discover extracts *operational logic* from corpora of documents and aligns it with reality.

It is the **AI-native synthesis of all three**, with a clear target architecture (ProcessSkill format, Knowledge Graph schema) that everything is normalised into.

### 13.3 Discover architecture

Discover is itself built on Yantra — it is a *meta* application of the platform. Its components:

- **Ingestion connectors** for the institution's content sources (Confluence, SharePoint, NAS, Git, JIRA, ServiceNow, LOS/LMS exports, BRE exports)
- **Document understanding pipeline** (Claude Opus for structure + extraction, Gemini for layout, Sarvam for Indic vernacular ops content)
- **Process-mining engine** (open-source — PM4Py — wrapped with Yantra-specific event-log normalisers for LOS/LMS schemas)
- **Interview agent** — runs structured interviews via voice or chat, transcribes, normalises into operational facts
- **As-is mapper** — composes the above into the Knowledge Graph scaffold and the BPMN/DMN scaffolds
- **Gap analyser** — diff against ProcessRepo; output a sequenced migration plan
- **Discover console** — the web UI for the customer's senior team to review, edit, sign-off

### 13.4 Discover deliverables

After 4 weeks:
- **As-is Knowledge Graph** — entities, processes, policies, systems, data lineage
- **ProcessSkill scaffolds** for every discovered process (manifest + skeleton BPMN/DMN + inferred contracts)
- **MCP inventory** — which systems need Tier C wrappers, scoped and sized
- **Gap analysis** vs ProcessRepo with recommendations
- **Sequenced migration plan** with first-wave pilot scope (typically 1 product line × 1 review skill, 1 reconciliation skill, 1 decisional skill)
- **Author elevation plan** — which policy people, how much protected time, what training
- **Data migration plan** — what to bring into the Knowledge Graph, what to leave in source systems and call via MCP
- **Executive readout** — a single document the customer's executive committee acts on

### 13.5 Pricing

Yantra Discover is a fixed-fee, fixed-duration engagement. Indicative: **₹35–80L for a 4–6 week sprint** depending on institution size and scope. It is also the most reliable on-ramp to a Harness deployment — the conversion rate from Discover to Phase-1 implementation is the key business KPI.

---

## 14. The Implementation Methodology

Yantra is sold and delivered with a deliberate methodology — not a custom-engineering motion. Five named phases, each with explicit deliverables and gates.

| Phase | Duration | Deliverable | Sign-off |
|---|---|---|---|
| **0 — Discover** | 3–6 weeks | As-is map, gap analysis, sequenced migration plan, first-wave scope | Customer ExCo |
| **1 — Foundation** | 4–6 weeks | Yantra Cloud (or Sovereign / Self-Managed) live, identity wired, top-10 Tier-C MCPs built, Knowledge Graph initial population, LiveAid trained on customer corpus | Customer CIO + CRO |
| **2 — First-wave authoring** | 6–8 weeks | 15–25 ProcessSkills authored by elevated senior policy team — typically 1 product line + cross-product compliance | Customer Credit Head + Compliance Head |
| **3 — Shadow + A/B** | 6–12 weeks | First-wave ProcessSkills run in shadow against live operations; A/B promotion to 10–20% of live volume; outcomes rubric tuned | Customer Credit Head + RBI engagement |
| **4 — Scale + always-on cookbooks** | ongoing | Production rollout; always-on cookbooks (reg-monitor, NPA-EWS, fraud-cluster, audit-trail-generator) live; further product lines onboarded in waves of three | Quarterly CRO + Board |

The whole arc — Discover to production — is **6–9 months** for a mid-sized NBFC. Author elevation, regulatory engagement, and platform familiarity compound; the second wave of ProcessSkills takes a fraction of the first wave's time, and by the fourth wave the institution authors with Yantra as casually as it once configured its LOS.

---

## 15. Why Now — The Competitive and Regulatory Landscape

Three forces converge in 2026 to make Yantra both possible and necessary:

### 15.1 Agentic platforms have become real

In May 2026 alone:
- **SAP unveiled the Autonomous Enterprise** with Claude embedded across Joule Assistants and 200+ specialised agents.
- **Anthropic shipped its Financial Services plugins, Cowork updates, and a $1.5B JV with Blackstone, Hellman & Friedman, and Goldman Sachs** for forward-deployed AI services.
- **Microsoft shipped the Agent Governance Toolkit** (April) — open-source, covering OWASP Agentic Top-10 risks, hooks into both Microsoft Agent Framework and Google ADK.
- **Google ADK** repositioned as an agent execution framework with Agent Identity and Agent Registry.
- **Camunda 8.9** shipped first-class "BPMN-activity-as-LLM-tool" patterns.
- **IBM and Yotta** announced a sovereign agentic AI platform for Indian enterprises.
- **Anthropic released Memory, Dreaming, Outcomes, and Multiagent Orchestration** for Managed Agents at Code w/ Claude SF, plus Live Artifacts in Cowork.

The pattern across all of these is the same: the unit of construction is no longer the chat prompt; it is the **deterministically configured agent grounded in business context**. The components that make this work — model adapters, MCP, A2A, BPMN-as-agent-shell, identity-per-agent, audit trails, durable workflows — have all stabilised in the last 9 months. The platform pattern is now obvious enough to build against.

### 15.2 Indian regulation is catching up — and the window is open

RBI has published its **Framework for Responsible Emerging AI in the Financial Sector (FREE-AI)** with seven Sutras: safety, transparency, accountability, fairness, inclusivity, sustainability, explainability. IRDAI and SEBI are converging on similar expectations. The current state of operational documentation in most Indian financial institutions does not meet what these will require. The ProcessSkill format — which is human-readable, machine-executable, regulator-auditable simultaneously — does.

Standards have not yet ossified. The institutions and platforms that operate transparently with the regulators in 2026–2027 help shape the operating standard; those that wait are told what shape it must take.

### 15.3 The global stack does not solve for India

Anthropic's FS plugins ship with US data partners (Moody's, LSEG, S&P, PitchBook). SAP's Joule Assistants ship with global business-process templates. Microsoft's Agent Framework is identity-centric but India-FS-regulation-agnostic. None of them ship with first-class connectors to AA, GSTN, CKYCR, DigiLocker, NACH, VAHAN, or IGRS; none of them are grounded in RBI Master Directions; none of them speak Hindi at the quality a customer expects.

The Indian opening is not "build a cheaper SAP." It is "build the SAP-shaped platform for the Indian-FS-specific operating context that the global vendors will not build."

---

## 16. The Business Model

### 16.1 Three revenue legs

1. **Platform subscriptions** — Yantra Cloud / Sovereign / Self-Managed, billed per institution, per environment, with consumption components (LLM tokens, gateway calls, storage)
2. **Yantra Author seats** — per senior policy author, monthly. This is the buyer where the CHRO/COO/CRO sit; software-margin revenue.
3. **Implementation services** — Discover engagements (fixed-fee), Foundation + first-wave (fixed-fee), ongoing managed services (retainer)

Plus two recurring add-ons:
- **Always-on cookbooks** — per cookbook per month (reg-monitor, NPA-EWS, fraud-cluster, audit-trail, inspection-pack-builder)
- **RBI/IRDAI/SEBI corpus subscription** — daily-delta, version-aware regulatory feed; annual

### 16.2 Open-source posture

| Asset | Licence | Rationale |
|---|---|---|
| ProcessSkill specification | Apache-2.0, foundation-governed | A standard private is a standard dead |
| `yantrac` validator + LSP + CLI | Apache-2.0 | Author tooling must be free or it doesn't get adopted |
| ProcessRepo core verticals | Apache-2.0 | Standard-defining; must be inspectable |
| Reference Agent Runtime (single-tenant) | BSL → Apache-2.0 after 4 years (Camunda model) | Defensible for 4 years, then community |
| Yantra Author Cloud | Commercial SaaS | Per-seat — where Yantra makes money on authors |
| Yantra Gateway managed | Commercial SaaS | Per-call after free tier; uptime SLA is the value |
| RBI/IRDAI/SEBI corpus (live updated) | Commercial subscription | Daily delta + briefings |
| Implementation services | Fixed-fee + retainer | Where the wedge happens |

### 16.3 Pricing (indicative)

| SKU | Price metric | Range |
|---|---|---|
| Yantra Author | per seat / month | ₹15K–40K |
| Yantra Cloud | per institution / month base + variable | ₹15–40L / month base |
| Yantra Sovereign / Self-Managed | annual / environment | ₹2–6Cr / year |
| Always-on cookbooks | per cookbook / month | ₹5–15L / month |
| RBI/IRDAI/SEBI corpus | annual | ₹50L–1.5Cr |
| Discover engagement | fixed | ₹35–80L |
| Foundation + first-wave | fixed | ₹1.2–3Cr |
| Ongoing retainer | monthly | ₹10–30L |

A 10-customer Y2 target plausibly generates **₹65–80Cr ARR** at ~55% gross margin, with services drag declining year over year as the platform matures and the Author seat count grows.

---

## 17. The Strategic Bets

Five bets are built into Yantra's design. They should be named explicitly so the team and investors can debate them:

1. **The unit of value migrates from the system to the artefact.** What the institution owns and re-deploys is the ProcessSkill, not the runtime. If this bet is wrong, Yantra is a worse SAP.
2. **Multi-LLM via adapter beats single-LLM commitment.** Model performance, pricing, and regulatory standing will continue to shift. Institutions that route by sensitivity tier outperform institutions that bet exclusively on one vendor. If wrong, Yantra has carried adapter overhead for no benefit.
3. **The senior policy author is elevated to first-class engineer.** The Yantra Author seat business depends on this. If wrong, the same work continues to be done by software engineers translating policy intents into code, and YantraAuthor is a niche tool.
4. **Open-standard ProcessSkill spec, foundation-governed, beats proprietary format.** SAP keeps Joule skills proprietary; IBM watsonx keeps its formats internal. Yantra bets the opposite — that openness wins distribution, that the foundation governance wins regulator confidence, and that commercial value sits in the workbench, gateway, corpus, and services. If wrong, Yantra has given up proprietary lock-in for no commensurate gain.
5. **Indian regulation is a wedge, not an obstacle.** A platform grounded in RBI/IRDAI/SEBI from day one wins against global platforms that get to India in year 3. If wrong, the institutions adopt the global platforms first and retrofit Indian compliance.

The five bets are mutually reinforcing — they win or lose together more than independently. That is the right shape for a platform play.

---

## 18. Open Questions

Before committing to the plan in §19, these need answers:

1. **Founding team.** The platform requires four named senior people: a founder with senior Indian lending operating experience, an AI / platform engineer with experience at one of the AI labs or a platform company, a regulator-side veteran (RBI / IRDAI / NPCI / IDRBT alumnus), and a head of ProcessSkill content from a senior credit / risk / compliance background at a large bank. Is this team in place, partially in place, or to be hired? Each gap is a Phase-0 risk.

2. **Anchor customer #1.** Phase 0 is most useful when a named NBFC is signed for a paid Discover engagement within 90 days. Who is that customer?

3. **Sovereign-cloud partner sequence.** Which Indian sovereign cloud is the first formal partnership — Yotta (likely first contact, given the IBM precedent), Airtel Cloud (largest data-centre footprint), Ashoka Cloud, or NTT? The first partnership shapes the GTM.

4. **Foundation home for ProcessSkill spec.** Linux Foundation AI & Data, OASIS, BIAN, or a fresh body? Each carries different governance and adoption dynamics.

5. **Regulator engagement.** RBI Innovation Hub, IDRBT, FREE-AI working group — which conversation is opened first? Even one informal meeting before Phase 1 is invaluable.

6. **Existing investor / capital posture.** Bootstrap-to-break-even (achievable in 18 months with one signed pilot + one cloud partner) or seed round (faster, compresses optionality)? The decision shapes hiring pace and the open-source aggression.

7. **Naming.** "Yantra," "ProcessSkill," "ProcessRepo," "YantraGateway," "YantraAuthor," "LiveAid," "Yantra Discover" — are these the names that go to market, or are they internal placeholders? Naming influences positioning more than most things; worth deciding before launch.

8. **First product-line wedge.** MSME unsecured cash-flow underwriting (highest novelty) or gold-loan branch ops (highest revenue-per-customer-day, lowest complexity)? The decision shapes which ProcessRepo verticals are built first.

---

## 19. The Phased Plan

### Phase 0 — Standards, team, anchor (months 0–4)

- ProcessSkill specification v0.1 published openly; foundation home identified; pre-conversations with IBA, IDRBT, FACE, NPCI initiated
- `yantrac` validator + LSP + CLI v0.1 open-sourced
- Founding team complete to the 4-person spec
- Anchor customer #1 signed for paid Discover; one sovereign-cloud partnership LOI
- First regulator engagement (RBI Innovation Hub or IDRBT)
- One reference ProcessSkill end-to-end published (canonical: personal-loan top-up assessment — 8-rule DMN + BPMN + 10 Gherkin scenarios + KFS template)
- Headcount: 8

### Phase 1 — Platform MVP + first Discover (months 4–10)

- Yantra Cloud on sovereign-cloud partner #1; identity wired; WORM audit; observability live
- Knowledge Graph schema v1; RBI/IRDAI/SEBI corpus ingestion live
- YantraGateway v0 with 10 Tier-A MCPs (AA, GSTN, CKYCR, DigiLocker, bureau×3, eSign, NACH, VAHAN, FIU-IND)
- YantraAuthor v0 (VS Code + web composer) with LiveAid integrated; LLM Adapter routing across 3 models
- Camunda 8 embedded; DMN engine wired; Python helper runtime sandboxed
- ProcessRepo core verticals + one product-line pack (chosen at Phase 0 close)
- Anchor customer #1: Discover sprint executed, Foundation deployed, first-wave authoring underway
- Headcount: 18

### Phase 2 — Standardise + 2 more customers (months 10–18)

- ProcessSkill spec v1.0 under foundation governance; ≥ 2 third-party-authored ProcessSkills in ProcessRepo
- Anchor customer #1 in shadow mode → A/B; first measured outcomes
- Two further customers signed for Discover → Foundation; one MSME, one secured-retail
- Portability Layer v1: export to Anthropic + Microsoft Agent Framework runtimes
- Always-on cookbooks: reg-monitor, NPA-early-warning, fraud-cluster-watcher, audit-trail-generator productised
- Partner program: Karza, Perfios, CredGenics, Hyperverge sign partner-built ProcessSkill agreements
- Headcount: 28

### Phase 3 — Multi-product, sovereign-cloud co-sell (months 18–30)

- Yantra Sovereign and Self-Managed GA
- Sovereign-cloud partners 2 and 3 added; formal co-sell motions
- Microsoft / Anthropic co-sell partnerships formal; Yantra positioned as the India-FS opinionation layer
- Builder Hub launched (community ProcessSkills with trust layer)
- 10 paying customers; ARR ₹65–80Cr; gross margin ≥ 55%
- First RBI inspection-pack-builder used in an actual inspection (with anonymised permission)
- Headcount: 40

### Phase 4 — The standard takes hold (months 30–48)

- ProcessSkill v2.0; CMMN added if customer demand materialises; insurance and capital-markets verticals
- Indian regulator publicly references ProcessSkill format in a circular, guidance, or framework — Yantra "wins" not by revenue but by becoming the standard
- Geographic expansion: Bangladesh, UAE (regulatory adjacency)
- Yantra at ₹250–400Cr ARR; category-defining

---

## 20. The One-Page Mental Model

> Yantra is the Agentic ERP for Indian Financial Services. It is to Indian banks, NBFCs, AMCs, and insurers what SAP's Autonomous Enterprise is becoming to global manufacturing — except built agent-native, multi-LLM by design, opinionated for Indian regulation and rails, and configured in an open, deterministic format (ProcessSkill) that the institution owns.
>
> The institution configures its operations in ProcessSkills — folders of DMN, BPMN, OpenAPI, Gherkin, Python, and markdown that are simultaneously human-readable, machine-executable, and regulator-auditable. It installs the curated ProcessRepo as a starting point. It authors institution-specific ProcessSkills in YantraAuthor, with LiveAid making the author productive. The Yantra Knowledge Graph gives every agent business context. YantraGateway is the trust boundary through which agents reach AA, GSTN, CKYCR, bureau, internal LOS/LMS, and everything else. The LLM Adapter routes each model call to the right model for the workload's sensitivity. Camunda runs the durable workflows. The agents have cryptographic identities and run on Microsoft Entra or Google Workspace authentication. The whole thing is hosted on sovereign Indian cloud — Yotta, Airtel, Ashoka, NTT — chosen by the customer.
>
> Every engagement begins with Yantra Discover — an automated 3–6 week sprint that maps the institution's current operations into the target ProcessSkill format and Knowledge Graph schema. From there, the institution is in production in 6–9 months.
>
> Microsoft owns identity. Anthropic, OpenAI, Google, Sarvam own cognition. Camunda owns durable workflow. Yotta and Airtel own sovereign infrastructure. Yantra owns the **artefact** — the ProcessSkill — and the **opinionation** — how an Indian financial institution should configure itself in the agentic era. If, by 2030, Indian banks routinely say *"we author our credit policy in processkill format and run it on Yantra,"* Yantra has won.

---

## 21. Sources

**SAP Sapphire 2026 — the anchor**
- [SAP and Anthropic: Claude on SAP Business AI Platform — SAP News Center](https://news.sap.com/2026/05/sap-anthropic-to-bring-claude-sap-business-ai-platform/)
- [SAP Unveils the Autonomous Enterprise — SAP News Center](https://news.sap.com/2026/05/sap-sapphire-sap-unveils-autonomous-enterprise/)
- [SAP Sapphire 2026: The Autonomous Enterprise Is Credible, But It Comes With Concentration Risk — Forrester](https://www.forrester.com/blogs/sap-sapphire-2026-the-autonomous-enterprise-is-credible-but-it-comes-with-concentration-risk/)
- [SAP launches managed Joule Studio with Cursor and Claude Code support — The New Stack](https://thenewstack.io/sap-joule-studio-managed-agents/)
- [SAP recasts Joule as the front door to autonomous enterprise AI — SiliconANGLE](https://siliconangle.com/2026/05/12/sap-recasts-joule-front-door-autonomous-enterprise-ai/)
- [SAP unveils Autonomous Enterprise — Help Net Security](https://www.helpnetsecurity.com/2026/05/12/sap-autonomous-enterprise-business-workflows/)
- [Joule Studio | AI Agent and Skill Builder — SAP](https://www.sap.com/products/artificial-intelligence/joule-studio.html)
- [Agentic AI & AI Agents — SAP Architecture Center](https://architecture.learning.sap.com/docs/ref-arch/ca1d2a3e)
- [n8n embedded in SAP Joule Studio — TheNextWeb](https://thenextweb.com/news/n8n-sap-joule-studio-workflow-automation)

**Anthropic — agentic platform features**
- [Agents for financial services — Anthropic](https://www.anthropic.com/news/finance-agents)
- [anthropics/financial-services — GitHub](https://github.com/anthropics/financial-services)
- [anthropics/claude-for-legal — GitHub](https://github.com/anthropics/claude-for-legal)
- [New in Claude Managed Agents: dreaming, outcomes, and multiagent orchestration — Claude](https://claude.com/blog/new-in-claude-managed-agents)
- [Code w/ Claude SF 2026 — Claude](https://claude.com/blog/code-w-claude-sf-2026-sf)
- [Building a new enterprise AI services company — Anthropic + Blackstone JV announcement (Blackstone)](https://www.blackstone.com/news/press/anthropic-partners-with-blackstone-hellman-friedman-and-goldman-sachs-to-launch-enterprise-ai-services-firm/)

**Microsoft, Google, Camunda — competitive landscape**
- [Microsoft Agent Governance Toolkit — Microsoft Open Source Blog](https://opensource.microsoft.com/blog/2026/04/02/introducing-the-agent-governance-toolkit-open-source-runtime-security-for-ai-agents/)
- [microsoft/agent-governance-toolkit — GitHub](https://github.com/microsoft/agent-governance-toolkit)
- [Google ADK Is an Agent Execution Framework — Futurum](https://futurumgroup.com/insights/google-adk-is-not-a-toolkit-it-is-an-agent-execution-framework/)
- [Camunda 8.9: Fastest Path to Agentic Orchestration](https://camunda.com/blog/2026/04/camunda-8-9-fastest-path-to-agentic-orchestration/)
- [Camunda Agentic Orchestration](https://camunda.com/solutions/agentic-orchestration/)

**India — sovereign cloud and regulation**
- [IBM, Yotta to build sovereign agentic AI platform for Indian enterprises — Business Today](https://www.businesstoday.in/technology/story/ibm-yotta-to-build-sovereign-agentic-ai-platform-for-indian-enterprises-530268-2026-05-07)
- [Understanding RBI FREE-AI Framework — Solytics Partners](https://www.solytics-partners.com/resources/blogs/understanding-rbi-free-ai-framework-2025-building-responsible-ethical-and-accountable-ai-governance-in-indias-bfsi-sector)
- [Analysing RBI's AI Framework — NIPFP](https://www.nipfp.org.in/publication-index-page/blog-index-page/analysing-rbis-ai-framework/)
- [I4C–RBIH AI pact for fraud detection — Business Today](https://www.businesstoday.in/technology/story/indian-govt-rbi-innovation-signs-ai-pact-to-tackle-financial-frauds-531090-2026-05-12)
