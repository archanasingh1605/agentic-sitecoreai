# Handoff: Agentic Delivery Flow and its Azure platform

**Purpose:** carry this work into a new Claude Code session with nothing lost.
**Written:** 2026-10-25 (session 1), rewritten 2026-10-31 (session 2), and rewritten again on 2026-11-10 at the end of session 3.
**Created by:** Archana Singh. Credit her on every diagram and document; never use the signed-in account's name.

**Where things stand**
- **Design work is complete.**
  - The delivery flow is at **v8**: `diagram-versions/agentic-flow-v8.html` (+ `.png`).
  - The Azure platform architecture is at **v2.13**: `diagram-versions/agentic-architecture-v2.13.html` (+ `.png`).
  - Both are merged into `develop` and `main`.
- **Next phase:** the user wants to **learn Microsoft Foundry and the Azure setup around it**, so that real implementation can start. See section 12.

> **For the new session (Claude):** read this whole file before replying. Then:
> 1. look at `diagram-versions/agentic-architecture-v2.13.png` and `diagram-versions/agentic-flow-v8.png`;
> 2. read `docs/architecture-guide.md` (the plain-language guide to the platform);
> 3. skim `docs/azure-platform-recommendations.md` and `diagram-versions/CHANGELOG.md`.
>
> Follow the working preferences in `CLAUDE.md` (private, auto-loaded in this folder) strictly. Treat everything here as decided unless it's listed under open items (section 11).
>
> **Foundry changes fast.** Check facts against current Microsoft Learn pages before teaching them as true, and say when something is unverified.

---

## 1. How to work with this user

The user's working preferences and all **client-specific project details** are in **`CLAUDE.md`**. It's private and git-ignored, so it isn't on GitHub, and Claude Code loads it automatically in every session in this folder. This file stays generic so it can be committed and shared.

**Short version of the preferences** (full list in `CLAUDE.md`):
- **Who they are:** a senior Sitecore AI developer (XM Cloud, headless Next.js), **new to agentic AI and to Azure Foundry**. Explain in simple, everyday words, with analogies from their world (Sitecore serialization, Next.js middleware, IIS rules) and concrete examples.
- **Your roles:**
  - **context sponge:** never invent facts;
  - **brainstorm partner;**
  - **teacher:** say plainly when something isn't industry standard or good practice, explain why, and recommend the better approach.
- **Questions:** simple and brief, with options and an example for each. Mark one **Recommended**, with the reason. For fact-only questions, say there's no recommendation.
- **Don't jump ahead.** Wait until a chunk of context is complete. Never touch `side-notes.md` unless asked.
- **Git:** before anything with side effects, show the steps and get a go-ahead. Commit, push or raise PRs only when asked. **Never merge: the user merges PRs manually.**
- **Diagrams:** every revision is a new version, saved as HTML + PNG with a CHANGELOG entry. Minor changes take a minor number (v2.12 → v2.13).
- **No client or project names in anything committed.**

**What worked well in session 3**
- **One question per box:** the user goes through the diagram box by box and asks "what is this, and why?". Answer with a plain one-line summary first, then a table or example.
- **Push back:** they push back when something feels wrong ("shouldn't an agent do this?"). Give an honest teacher answer and options; don't simply agree.
- **Spoken input:** they often dictate, so words can be garbled ("YSEP pipeline" meant Bicep pipeline; "peer" meant footer). Read the intent and confirm only if it's unclear.
- **Self-explanatory diagrams:** they want diagrams that can be read without the docs, and they remove anything that duplicates another document.

---

## 2. The project being agentified (context)

*Generic summary. The full client-specific details are in the private `CLAUDE.md`.*

**Repo:** the client's monorepo, on the user's work machine, default branch `develop`.
- The user owns and works across the whole repo.
- This Mac **has no copy of the repo**. Repo knowledge comes from screenshots of its `CLAUDE.md` in `inputs/` (git-ignored).

**Platform**
- Sitecore XM Cloud (Sitecore AI) + headless Next.js.
- **Only the `corp` app is in scope; ignore the other app.**

**Monorepo `headapps/`** (Turborepo + pnpm 11.5; npm and yarn are blocked)
- **Apps:** `apps/corp`, plus a second app (out of scope).
- **Packages:** `@client/ui` (atoms, molecules) · `@client/sitecore-components` (CMS components) · `@client/themes` (tokens, Tailwind preset) · `@client/utils` (analytics, store, i18n, cookies, SEO, GTM).

**Backend `authoring/`**
- .NET solution with Sitecore serialized YML items.
- **Rule:** foundational items are serialized and version-controlled, so every environment has the same items.

**Stack:** Node ≥24.16, Next.js 16 App Router, React 19, TypeScript 5.8 strict, Tailwind 4, Content SDK 2.1.0, Storybook 8.6.

**Component pattern**
- Named export `Component.tsx`; `index.ts` default-exports `withDatasourceCheck()(Component)`.
- `FieldVisibilityGuard` wraps fields; editing mode comes from `useSitecore().page?.mode?.isEditing`.
- Server components by default; `data-qa` / `data-testid` attributes.
- Co-located `.test.tsx` + `.stories.tsx`.

**Testing**
- Jest + RTL for unit tests; Playwright for end-to-end tests.
- Every component needs 4 test cases: valid data, missing fields, editing mode, error state.

**SonarQube (blocks merges):** complexity ≤15, functions ≤75 lines, files ≤300 lines; no `any`, no `!`, no `console.log`, no silent `catch`.

**Git and CI/CD (client repo)**
- `feature/<TICKET-ID>-…` branches.
- GitHub Actions run the PR checks; CodeQL runs weekly.
- Deploys: XM Cloud (CM) and Vercel (Next.js).

**Facts the user confirmed**
- **The tracker is Azure DevOps (ADO).**
- Ops tickets also come from **ServiceNow**.
- **One shared Sitecore dev environment.**
- The **BE Agent creates items on dev**, then serializes them (`ser pull`) to YML.
- **Code is on GitHub**; the FE deploys to Vercel (a preview per PR).
- **Coverage target in the flow: 70% for now.**
- Stories may include a Figma URL. Brand info is in the Sitecore Brand Kit.
- Official Sitecore MCP servers: **Marketer MCP** and **Docs MCP**.

---

## 3. Origin of the work

- The user's **product manager** wants the project agentified as much as possible, and asked for diagrams showing where and how.
- **Session 1–2:** the delivery flow, built from a handwritten sketch (in `inputs/`, git-ignored).
- **Session 3:** the Azure platform it runs on. The PM suggested **Microsoft Foundry** for hosting the models.
- **Reference only:** a YouTube video, summarized in `reference-agentic-team-flow-video.md`. **Don't copy that flow.**

---

## 4. The delivery flow (v8): what the agents do

**Phases:** Intake (step 0, always on) · Plan (1–6) · Build (7–10) · Verify & ship (11–17) · Content (18–21, optional).

**Human gates (👤, 4):** 2B story details · 3C prototype pick · 15 PR approval · 21 publish approval.

| # | Step / Agent | What it does | Model | Skill |
|---|---|---|---|---|
| 0 | Intake Agent | Watches ServiceNow and ADO. Sorts each ticket (bug, enhancement, new component, not for agents), spots duplicates, links the ServiceNow ticket, creates or updates the ADO story with a suggested priority | S | `triage-tickets` |
| 1 | User story arrives | The backlog owner marks the story ready; story memory opens (script) | none | — |
| 2 | PRD Agent | Checks the story against a readiness checklist (→ 2A if details are missing), then writes the PRD from the story + Figma | M | `write-prd` |
| 2A | Clarifier Agent | Drafts a **suggested answer with its source** for each open question (ServiceNow, ADO, Figma, past stories); "no source found" instead of guessing. Never decides | M | `clarify-story` |
| 2B | 👤 Story owner confirms | Ticks or corrects the answers. No round cap; reminder after 2 working days | — | — |
| 3 | PRD Review Agent | Challenges the PRD and checks it against Figma; gaps go back to 2 (max 3) | L | `review-prd` |
| 3A | Figma URL? (script) | Yes → 4; no → 3B | none | — |
| 3B | Prototype Agent | 3 clickable prototype variants from the reviewed PRD | M | `build-prototypes` |
| 3C | 👤 Pick a prototype | Becomes the design reference | — | — |
| 4 | Spec Agent | PRD → spec, recording the design reference | M | `write-spec` |
| 4A | Blueprint Agent | Picks the base reference component; runs once, in parallel | L | `pick-blueprint` |
| 5 | Task Planner Agent 🤖 | Splits the spec into tasks; dependencies decide the order | L | `plan-tasks` |
| 6 | Contract Agent 🤖 | Agrees the base model so FE and BE work in parallel; can send the plan back to 5 (max 3) | L | `define-contract` |
| 7 | FE Agent ‖ BE Agent | FE: the Next.js component. BE: Sitecore items on dev + YML (doesn't commit) | M | `build-component`, `create-sitecore-items` |
| 8 | Code Quality ‖ Security | Local Sonar rules + vulnerability scan; one fix list → 7 (max 3) | M | `check-code-quality`, `scan-security` |
| 9 | Test & Storybook Agent | 4 required test cases + stories; 70% coverage; failures → 7 (max 3) | M | `write-tests-and-stories` |
| 10 | Authoring Guide Agent | A Markdown guide for authors, attached to the ADO story by the orchestrator | M | `write-authoring-guide` |
| 11 | PR Agent | Commits code + YML + guide, raises the PR, checks the Vercel preview | S | `raise-pr` |
| 12 | Test Page Agent | Builds a test page on dev by following only the guide | M | `build-test-page` |
| 13 | Preview QA ‖ Performance | QA checks the acceptance criteria and design and adds Playwright E2E tests; Performance checks speed | M / S | `test-preview`, `check-performance` |
| 14 | Bug Raising Agent | Bugs as ADO child items, with Vercel logs → 7 (max 3) | S | `raise-bugs` |
| 15 | 👤 PR approval | In GitHub; SonarQube + CodeQL remain the final gate | — | — |
| 16 | Merge & Deploy Agent | Merges; deploys to Vercel + Sitecore dev | S | `merge-and-deploy` |
| 17 | QA Expert | Full test on dev against the spec | M | `run-full-qa` |
| 18 | Content Authoring Agent | Uses the Brand Kit and the guide; dev first | M | `author-content` |
| 19 | Content Review Agent | Brand and standards check → 18 (max 3) | M | `review-content` |
| 20 | Final Page Report (script) | Preview URL, item path and GUID per page | none | — |
| 21 | 👤 Approve, then publish | | — | — |

**Rules across the flow**
- **Loops:** every agent loop is capped at 3 rounds, then goes to 👤 **Needs Human**. The only uncapped loop is 2A → 2B. The third round may use the next model size up.
- **Build triad per task:** develop (7) → review (8) → test (9). An agent never reviews or tests its own work.
- **Spec-driven development:** steps 2–6 shape the spec, 7 builds from it, and 13 and 17 check against it.
- **Story memory:** agents hand work over through it. No agent merges or publishes without a person's approval.

**ADO states (custom; an ADO admin must add them):**
- New → Drafting Requirements → 👤 Awaiting Story Details* → Reviewing Requirements → Design* → 👤 Awaiting Design Pick* → Finalizing Requirements → In Development → Quality Checks → Testing → Preparing Authoring Guide → Preview Testing → 👤 Awaiting PR Approval → Deployment in Progress → Under QA Review → Content Authoring* → 👤 Awaiting Publish Approval* → Done.
- Plus 👤 Needs Human at any time. *Only when that step is used.
- The orchestrator sets the state (no AI cost); agents add comments.

**ServiceNow states** (only when the work started there):
- **Open** when the ADO story is created.
- **In progress** while the flow works or waits on the team's own approvals.
- **On hold** for 👤 Awaiting Story Details and 👤 Needs Human.
- **Resolved** when Done. More states can be added during implementation.

**Agentic concepts (decided):**
- **Subagents:** each agent has its own context and tools.
- **Orchestration:** scripted, with AI judgement inside steps (🤖).
- **Memory:** app memory (`AGENTS.md`, the blueprint, lessons) and story memory.
- **Harness:** the Claude Agent SDK; no custom harness.
- **Packaging:** one team **plugin**, with **one skill per agent** (24 skills) and **hooks** (auto-format + lint, dev-only lock, run tests on change, pre-commit checks).
- **Connectors:** MCP servers: ADO, GitHub, Figma, Sitecore CLI/dev, Sitecore Marketer, Sitecore Docs, Vercel, Next.js DevTools, Playwright, Storybook (needs Storybook 10), Chrome DevTools, ServiceNow.

---

## 5. The Azure platform (architecture v2.13): where the flow runs

**Shape:** Foundry-first. Microsoft Foundry hosts the models, runs every agent and governs them. A few Azure services around it handle events, monitoring and data.

**Layout (left to right):**
- **Tickets and events in:**
  - ServiceNow (business rule webhook);
  - Azure DevOps (service hooks);
  - GitHub (GitHub App webhook);
  - Teams (later).
- **Inside the Azure subscription, one resource group per environment** (new, created with Bicep). Microsoft Entra ID sits **outside** it, because it belongs to the whole tenant.
- **Events & orchestration:**
  - **API Management** (⚙️ Azure service, 🌐 the one public door): checks each sender, limits, logs, and converts messages to one event shape.
  - **Event Grid** (⚙️, 🔒): routes events, with retries for up to 24 hours.
  - **A Function App** (📝 our code, 🧩 in the VNet, Flex Consumption) holding three things:
    - the **Orchestrator** (Durable Functions): two runs per ticket, the v8 order, loop counts, ADO states, sleeping free at gates, a cap on agents at once, retries, then Needs Human;
    - the **Safety net** (timer): asks ServiceNow, ADO and GitHub for missed events and sends them to Event Grid;
    - the **scripted steps** 1, 3A and 20.
  - **Service Bus** is an **optional** "if volume grows" queue. It's hidden by default; click the dashed box to show it.
- **Microsoft Foundry:**
  - **Model catalogue → deployments:** Large = Claude Opus, Mid = Sonnet, Small = Haiku, with pinned versions.
  - **AI gateway** (API Management): checks the agent ID, applies token limits, forwards, and logs cost per ticket.
  - **Tools:** remote MCP servers registered once (ADO, GitHub, Sitecore Marketer, Figma, Vercel, ServiceNow, Sitecore Docs), with a tool list per agent.
  - **Foundry Agent Service, hosted agents** (🧩): a sealed sandbox per session from the team's agent image. The Intake run, then the delivery run. Local MCP (Playwright, Next.js DevTools, Chrome DevTools, Storybook) and command-line tools (git, pnpm, Sitecore CLI) run inside the sandbox.
  - **Control plane:**
    - 🪪 Entra Agent ID (⚙️ setting);
    - 🛡️ Guardrails (⚙️ setting);
    - 🔍 Tracing (🔌 connection to App Insights);
    - ✅ Evaluations (📝 a feature we script).
- **Monitoring & cost:**
  - Application Insights (traces, cost per ticket);
  - Monitor + Cost (alerts, budgets, a PM dashboard as an Azure workbook);
  - a "Status, no extra app" note.
- **Far right:**
  - **Where the work lands:** GitHub, Vercel, Sitecore AI (XM Cloud dev), Figma and Sitecore Docs. Everything goes out through a **NAT gateway**, one fixed outgoing IP.
  - **Where people see status:** Azure DevOps and ServiceNow.
  - **The people:** backlog owner, developers, PM and platform owner, content approver.
- **Production foundation** (bottom band):
  - Key Vault (🔒): one login per outside system;
  - Container Registry (🔒): the agent image;
  - Blob Storage (🔒): story memory;
  - private network: private endpoints, VNet, NAT, and the network legend;
  - infra as code: Bicep.
- **Identity:**
  - one Entra ID per agent type (about 22);
  - the ticket number is a label, not an ID;
  - a fresh sandbox per task;
  - tokens about once an hour;
  - outside systems use non-personal logins kept in Key Vault.

**Journey ① to ⑨:** ticket arrives → two runs (Intake, then delivery when marked ready) → agent starts → agent thinks (via the gateway) → agent acts (tools) → progress saved (Blob + ADO state) → status in ADO/ServiceNow → 👤 gates → everything measured.

**Concurrency:** many tickets run **at the same time**, each with its own orchestrator run. Within a ticket the steps follow the v8 order. A waiting ticket uses nothing. Only the "agents at once" cap limits throughput.

---

## 6. Platform decisions made in session 3 (with reasons)

| Decision | Why |
|---|---|
| **Foundry-first** (v2) over the v1 hybrid (Container Apps runner) | Models, agents and governance in one place. v1 is kept in the repo as a fallback, but **the user no longer uses it**; v1 references were removed from v2.11 on |
| **Orchestrator = code** (Durable Functions), AI judgement inside steps | The flow's rules must hold every time; code is predictable, cheap, auditable, and can wait for days for free. An all-AI orchestrator was discussed and not recommended |
| **Two runs per ticket** (Intake run; delivery run when 👤 marked ready) | Delivery only starts after a person says the story is ready |
| **2A Clarifier Agent drafts, 👤 2B confirms** (flow v8) | The agent suggests answers with sources and never decides requirements |
| **Remote MCP registered in Foundry Tools; local MCP in the agent image's `.mcp.json`** | Central control and logging for outside systems; local tools run next to the code |
| **Identity:** one Entra Agent ID per agent type; the ticket number as a label | Reused IDs; the gateway logs cost per ticket |
| **Service Bus dropped** from the default design; an "if volume grows" option | The orchestrator's own limits and retries are enough at pilot size; fewer parts and lower cost |
| **No live Control Centre** (Cosmos DB, Web PubSub, web app, Front Door removed in v2.8) | Status shows in ADO and ServiceNow, where people already work; no idle App Service cost |
| **Safety net** sends missed events to Event Grid, not through API Management | It's already inside Azure; API Management is only for outside callers |
| **New resource group per environment**; Entra ID outside it | Clean boundaries |
| **Subscriptions** (recommended, not confirmed): start in the existing non-prod subscription, move to dedicated ones before prod | Model quota is per subscription and region; check Claude eligibility for the subscription type |
| **Tiers:** pay-as-you-go is fine; Premium mainly where private networking needs it | Service Bus private endpoints need Premium; check the API Management tier |
| **Infra as code with Bicep:** manual or portal setup is fine in the spike; a small GitHub Actions workflow before test and prod | Dev, test and prod must match; no build artifact, it applies a desired state. The Function App code and the agent image have their own small artifact pipelines |
| **ADO auth:** preferably the agents' Entra identity added as an ADO user (needs a Basic licence); fallback, a PAT in Key Vault | No personal tokens; nothing to leak or expire |

**Discussed, not decided:** a **Delivery Coordinator Agent** (phase 2). It would choose next moves when a run fails or is stuck, while the orchestrator's rulebook checks every move. I recommended "rulebook first, then the Coordinator after the pilot"; the user didn't confirm.

---

## 7. Teaching already given in session 3 (don't repeat unless asked)

- **Sealed sandbox:**
  - a fresh, isolated micro-VM per agent session, built from the image and thrown away afterwards;
  - the model catalogue vs deployments (pinned versions under your own names).
- **API Management as a reception desk:**
  - one public door; checks the pass; visitor list, sign-in log, crowd control; converts the form; sends to the right floor;
  - compared with Next.js `middleware.ts`;
  - it's a configured service with XML policies, not code;
  - the same product is used twice: the webhook front door and the AI gateway.
- **Service Bus:**
  - a managed queue (the ticket-number line at a counter);
  - parallel processing is a rolling cap, not batches of 5;
  - the cap is on agents running at once, not on tickets.
- **The safety net (reconciliation):** why webhooks get lost (outages, ADO switching off failing hooks, edited hooks, crashes before saving).
- **Event Grid** as a mail sorting room.
- **The control plane:** which parts are settings, which are connections, and which are features we script.
- **Key Vault, Container Registry, Blob, private network, NAT, VNet, private endpoints:**
  - no VPN is needed, because every outside system is SaaS;
  - "Bicep builds the house; the other pipelines move the furniture in";
  - IaC compared with Sitecore serialization;
  - the portal-clicking drift example.
- **`mcp.json` mapped to the platform:** remote → Foundry Tools; local → image `.mcp.json`; keys → Key Vault or Entra; model sign-in → Foundry deployments via the gateway.
- **Workflows vs agents:** use code for known rules and agents for judgement; "the AI decides, the code enforces"; an all-agent orchestrator still needs plumbing code.
- **The FE Agent spike:** what "clone, build and test the monorepo" proves (SDK support, network, sandbox size, tools, cost) and its fallbacks.
- **Concurrency:** tickets run simultaneously; steps are sequential within a ticket.

---

## 8. Version history (details in `diagram-versions/CHANGELOG.md`)

**Agentic Delivery Flow**
- v1–v6: base flow, concepts, design step, ADO states, MCPs, build triad.
- v7: step 0 Intake, model sizes, service identities, runs on Azure.
- **v8:** 2A Clarifier drafts answers, 2B owner confirms.

**Architecture vision:** v1, v2, v2.1. These were early, high-level drafts.

**Agentic architecture (Azure)**

| Version | Change |
|---|---|
| v1 | Hybrid: our own Container Apps runner (the fallback; no longer used) |
| v2 | Foundry-first redesign |
| v2.1 | Files lost; marked removed in the CHANGELOG |
| v2.2 | Identity layer and the "Agent identity, in short" panel |
| v2.3 | 2A Clarifier; references move to flow v8 |
| v2.4 | Intake run vs delivery run (two passes) |
| v2.5 | Remote MCP vs in-sandbox tools (local MCP, command line) |
| v2.6 | Control plane tagged as setting, connection or feature |
| v2.7 | One Function App block; ⚙️ Azure service vs 📝 our code tags; resource group boundary; Entra outside it; safety-net dotted lines; Service Bus as a click-to-show option; the key moved to the far right |
| v2.8 | Status in ADO and ServiceNow; Cosmos DB, Web PubSub, the Control Centre and Front Door removed; monitoring column; network badges and the NAT gateway |
| v2.9 | v8 ADO states with the ServiceNow mapping table |
| v2.10 | Labels on the safety-net lines |
| v2.11 | Removed the Service Bus button above the diagram, the journey timings and the v1 comparison |
| v2.12 | Removed the footer; status table shortened to a paragraph |
| **v2.13** | Status section removed (the ADO states live in flow v8) |

---

## 9. Documents

- **`docs/architecture-guide.md`:** the plain-language guide to the v2.13 platform, for peers:
  - how to read the diagram;
  - every area explained;
  - one ticket ① to ⑨ (with a Mermaid chart);
  - failures, settings and secrets, security;
  - decisions, spike items, open questions;
  - a glossary.
- **`docs/azure-platform-recommendations.md`:** the Foundry-first recommendations (13 sections). Its companion links still point to architecture **v2.3**; it predates v2.4–v2.13 (Service Bus and Cosmos DB were later dropped). **Update it or mark it superseded** when the user wants.
- **`docs/reference-guide.md`:** the reference guide to flow **v6** (older). Its editable Claude doc link is in `CLAUDE.md`.

---

## 10. Git and GitHub workflow

- **Repo:** `archanasingh1605/agentic-sitecoreai` (**private**).
- **Branching:** `feature/*` → PR into `develop` → release PR `develop` → `main`. **The user merges every PR.**
- **Commit messages** end with the Claude co-author line. PR bodies end with the Claude Code line and say "Created by Archana Singh".
- **Before every commit:**
  - check no `.env`, `CLAUDE.md`, `inputs/` or `.DS_Store` is staged;
  - grep for client terms and the account name.
- **Pushing:** no `gh` CLI on this Mac. Push with a one-off credential helper that reads `GITHUB_PAT` from `.env` at run time. Create PRs through the GitHub REST API with `curl`. The exact commands are in `CLAUDE.md`.
- **The old flow files v1–v5** (24 files) are **in the repo but deleted locally** to save space. They're marked `skip-worktree` so git ignores the local deletion, and a "commit everything" won't remove them from the repo.
  - To bring them back: `git ls-files -v | grep '^S' | cut -c3- | xargs git update-index --no-skip-worktree`, then restore them.
  - If someone changes one of them upstream, a pull may stop until that command is run.

**PR history**

| PR | Content | State |
|---|---|---|
| #1–#7 | Flow v1–v6, reference guide, releases | Merged |
| #8 | Architecture v1–v2.7, flow v7–v8, recommendations, handoff | Merged |
| #9 | Architecture v2.8–v2.13 | Merged |
| #10 | `docs/architecture-guide.md` | Merged |
| #11 | Release develop → main | Merged |

**State at this handoff:** everything is merged into `develop` and `main`. The local checkout is on `feature/architecture-guide`, which is merged. **For new work, branch from `origin/develop`.**

---

## 11. Open items

**Prove in a spike** (from the diagram)
1. Run one coding agent (7 · FE Agent, Mid) as a Foundry hosted agent with the team's image, and check it can clone, build and test the monorepo.
2. Confirm the **Claude Agent SDK runs inside hosted agents**. This is the biggest unknown: a news report said so, but no Microsoft or Anthropic guide was found. Fallbacks: Microsoft Agent Framework with Claude models, or a runner on Azure Container Apps.
3. Check Claude model quota and region availability for the subscription.
4. Confirm Foundry guardrails apply to the Claude deployments. If not, use Azure AI Content Safety in the AI gateway.
5. Compare Durable Functions and Foundry workflows for a gate that waits several days.
6. **Event Grid can't push to a private-only Function App.** Either allow only Event Grid in (service tag), or deliver through a Storage queue.
7. Confirm Foundry hosted agents can run inside the VNet.
8. Confirm the Claude Agent SDK can use Foundry-registered tools. If not, list the remote MCP servers in the image's `.mcp.json`, with secrets from Key Vault.
9. Decide how many agents may run at once.
10. Measure the cost of one real ticket, end to end.
11. Check Durable Functions on Flex Consumption with VNet integration.

**Open questions**
- Does On hold pause the SLA in the client's ServiceNow? (Ask its admin.)
- Can the client's ServiceNow send webhooks, or does it need the timer only?
- The current address of the Sitecore Docs MCP server.
- Which Azure region (it decides model availability and data residency).
- Which subscription to start in, and is Claude available for that subscription type?

**Carried over from the flow work** (parked or on hold):
- an ADO admin to add the custom states;
- the coverage target (70% for now);
- the Storybook 8.6 → 10 upgrade for the Storybook MCP;
- Sitecore and Vercel MCP service sign-in;
- writing `AGENTS.md`;
- where the existing Playwright tests run;
- the per-story base-component question;
- the pilot scope (proposed: one simple component story, steps 1–10, not confirmed);
- the Delivery Coordinator Agent (phase 2, not decided).

**`side-notes.md`:** the user's file. Don't edit it unless asked. Its "Parked for later" section holds **Audience intelligence** (left out of the workflow and architecture for now).

**Housekeeping (optional):**
- delete the merged feature branches on GitHub;
- remove the unneeded `admin:org` scope from the GitHub token;
- update or retire `docs/azure-platform-recommendations.md`.

---

## 12. Next phase: learning Foundry and the setup (proposal for the new session)

The user's goal: **understand Microsoft Foundry and the Azure setup well enough to start real work.**

Below is a **suggested** learning path, ordered by the spike's risks. Agree the order and depth with the user first. Teach hands-on where possible, in small steps, with the user doing the clicks and Claude explaining. Check each fact against current Microsoft Learn pages.

1. **Foundry basics.** Hub/resource vs project, the portal, roles and permissions. Create a Foundry resource and project in a sandbox resource group.
2. **Models.** Find Claude in the model catalogue, deploy Sonnet under a chosen name, call it from the playground and from a small script, and see the quota and region limits.
3. **Agents in Foundry.**
   - Prompt agents vs **hosted agents**.
   - Build the simplest hosted agent.
   - How the sandbox, image and identity work.
   - Check whether the **Claude Agent SDK** can run there (spike items 2 and 8).
4. **Tools.** Register one remote MCP server (e.g. Azure DevOps) as a Foundry tool, attach it to an agent, and see how sign-in works (Entra identity vs Key Vault secret).
5. **Identity and secrets.** Entra Agent ID, managed identities, Key Vault access through roles.
6. **Observability and safety.** Connect Application Insights tracing, look at a trace, try guardrails and a first evaluation.
7. **Orchestration.** A tiny Durable Functions orchestrator that starts an agent, waits for an external event (a fake approval) and resumes. Compare it with Foundry workflows (spike item 5).
8. **Events.** API Management → Event Grid → Function App with one ADO service hook. Add the safety-net timer.
9. **Networking.** VNet, private endpoints, NAT gateway, and the Event Grid–private Function App issue (spike items 6, 7, 11).
10. **Infra as code.** Turn what was clicked into Bicep, run it with one `az deployment` command, then add a small GitHub Actions workflow.
11. **The FE Agent spike end to end** (spike item 1), with cost measured (item 10).

**Before starting, ask the user** (with options, one marked Recommended):
- Which Azure subscription and region can they use, and do they have rights to create resource groups and Foundry resources?
- Which order and depth they want: concepts first, or hands-on first?
- Should learning notes go in this repo (e.g. `docs/learning/`) or in a new folder or repo?

The `claude-api` skill and Microsoft Learn are the sources for model IDs, SDK details and Foundry features. Don't answer from memory.

---

## 13. Files in this folder

| Path | What it is |
|---|---|
| `HANDOFF.md` | This file (generic, committed) |
| `CLAUDE.md` | **Private, git-ignored.** Working preferences, client details, private session notes |
| `docs/architecture-guide.md` | Plain-language guide to the platform (v2.13) |
| `docs/azure-platform-recommendations.md` | Foundry-first recommendations (older than v2.4; see section 9) |
| `docs/reference-guide.md` + `docs/images/` | Reference guide to flow v6 |
| `diagram-versions/agentic-architecture-v2.13.html/.png` | **Current platform diagram** |
| `diagram-versions/agentic-flow-v8.html/.png` | **Current flow diagram** |
| `diagram-versions/agentic-architecture-v1…v2.12`, `agentic-flow-v4.2/v6/v7`, `architecture-vision-*` | Earlier versions (flow v1–v5 are in the repo but not on disk; see section 10) |
| `diagram-versions/CHANGELOG.md` | What changed in every version |
| `side-notes.md` | The user's parked notes (don't edit unless asked) |
| `reference-agentic-team-flow-video.md` | Reference video summary (reference only) |
| `.env` | **Private, git-ignored.** `GITHUB_PAT` |
| `inputs/` | **Private, git-ignored.** Screenshots of the client repo's `CLAUDE.md` and the original sketch |

**Architecture HTML internals (v2.13)**
- One self-contained file with an inline SVG (`viewBox 0 0 1600 1236`). All content sits in `<g transform="translate(0,40)">`.
- **CSS classes:**
  - colour tokens `--c-evt/ai/agent/web/db/svc/ok`;
  - card classes `x-ai/x-evt/x-live/x-prod/x-code`;
  - SVG classes `.fapp` (Function App block), `.ibox` (inner cards), `.kind.k-evt` (⚙️/📝 tags), `.e.sn` (safety-net dotted line), `.nb/.nbc/.nbt` (network badges), `.npill` (NAT), `.spanel` (status note), `.rg/.rgtag` (resource group), `.sb-only/.sb-off` (Service Bus toggle, `?sb=1` opens it).
- **Editing approach:** copy the latest version to a new file with a Python script that does exact string replacements (each asserted to match), re-export, and check crops visually.
- **PNG export:**

  ```bash
  perl -e 'alarm 90; exec @ARGV' "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --disable-gpu --hide-scrollbars --force-device-scale-factor=2 --window-size=1632,<HEIGHT> --screenshot="$PWD/<file>.png" "file://$PWD/<file>.html?theme=light"
  ```

  - v2.13 uses height 2830.
  - Always wrap Chrome in `perl … alarm`, because headless Chrome sometimes hangs.
  - Check with `sips -c <h> <w> --cropOffset <y> <x>` crops; there's no PIL on this Mac.

---

## 14. How to start the new session

1. **Open a new Claude Code session in this folder** (`agentic-sitecoreai`), so `CLAUDE.md` loads automatically.
   - If you use a different folder, copy section 1 of `CLAUDE.md` into that folder's `CLAUDE.md` first.
2. Paste this prompt:

> Read `HANDOFF.md` in this folder in full, then look at `diagram-versions/agentic-architecture-v2.13.png` and `diagram-versions/agentic-flow-v8.png`, and read `docs/architecture-guide.md`. The design work is complete. I now want to learn Microsoft Foundry and the Azure setup around it, so real implementation can start. Follow the working preferences in `CLAUDE.md`. Confirm in a few lines that you have the context, then ask me the questions in section 12 of `HANDOFF.md` before proposing a learning plan.
