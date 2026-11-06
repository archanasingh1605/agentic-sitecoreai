# Diagram versions

Each revision is saved as a new pair of files (`agentic-flow-vN.html` + `agentic-flow-vN.png`). Earlier versions are never overwritten.

- **HTML:** open in any browser. Add `?theme=light` or `?theme=dark` to the URL to force a theme.
- **PNG:** a light-theme export for slides, email or Azure DevOps wiki pages.

> **v4.2 restored (2026-10-25):** v4.2's files were overwritten by mistake and rebuilt from v4.3 (v4.2 = v4.3 without the Sitecore MCP note in "Later", plus the 4.2 badge). The PNG was re-exported. Content is as originally released.

> **Azure diagrams renamed (2026-11-03):** the `azure-platform-vN` files are now `agentic-architecture-vN` (v1, v2, v2.2), and their changelog entries are headed "Agentic architecture". The content is unchanged apart from the "Compare with v1" link, which now points to the new v1 file name. Older diagrams that mention "the Azure platform architecture diagram" in their text were left as released.

> **Client names removed (2026-10-25):** every version's title is now just "Agentic Delivery Flow", with the client and project names taken out. All PNGs were re-exported. Nothing else in the diagrams changed.

## Agentic architecture v2.13 · 2026-11-06

`agentic-architecture-v2.13.html` (+ `.png`, 2× export). v2.12 is kept unchanged.

- **Removed the "Status in Azure DevOps and ServiceNow" section.** The ADO states live in the Agentic Delivery Flow v8. The diagram still shows where status appears: the Azure DevOps and ServiceNow boxes on the far right, and the "Status, no extra app" note.

## Agentic architecture v2.12 · 2026-11-06

`agentic-architecture-v2.12.html` (+ `.png`, 2× export). A shorter page. v2.11 is kept unchanged.

- **Removed the footer line.** The "Created by Archana Singh" badge at the top stays.
- **The "Status in Azure DevOps and ServiceNow" table is now one paragraph.** The ADO states are defined in the Agentic Delivery Flow v8, so the paragraph points there instead of repeating them. It keeps the ServiceNow mapping, which isn't in the flow:
  - Open when the ADO story is created.
  - In progress while the flow works or waits on the team's own approvals.
  - On hold while it waits on someone outside the team.
  - Resolved when the story is Done.

## Agentic architecture v2.11 · 2026-11-06

`agentic-architecture-v2.11.html` (+ `.png`, 2× export). A tidier page. v2.10 is kept unchanged.

- **Removed the "Show the 'if volume grows' option" button above the diagram.** The dashed "If volume grows" box under the Function App still shows and hides Service Bus when clicked.
- **Removed the timings from "One ticket in real time"** (0:00, +2 s, +5 s and the others), and the "(timings are illustrative)" note.
- **Removed the v1 (hybrid) comparison:** the "What changed from v1" table, the "Compare with v1" badge and the footer link. The spike's fallback now reads "a runner on Azure Container Apps" instead of "v1's Container Apps runner".

## Agentic architecture v2.10 · 2026-11-06

`agentic-architecture-v2.10.html` (+ `.png`, 2× export). Makes the safety net's dotted lines easier to read. v2.9 is kept unchanged.

- **Labels on the dotted lines:**
  - "safety net asks: anything missed?" sits under the ticket sources.
  - "missed events" sits beside the arrow into Event Grid.
- The Microsoft Teams box and the note below it moved down slightly to make room. Nothing else changed.

## Agentic architecture v2.9 · 2026-11-05

`agentic-architecture-v2.9.html` (+ `.png`, 2× export). Uses the flow v8's Azure DevOps states and adds ServiceNow states. v2.8 is kept unchanged.

- **Far right:** the Azure DevOps box reads "The v8 states, one per stage, plus an agent comment" (hover to see the list). The ServiceNow box reads "Open · In progress · On hold · Resolved".
- **New section, "Status in Azure DevOps and ServiceNow":** a table of every v8 ADO state, when it's set, and the ServiceNow state it maps to.
  - New → Open, plus a comment with the ADO link.
  - Working stages and the team's own gates (design pick, PR approval, publish approval) → In progress.
  - 👤 Awaiting Story Details and 👤 Needs Human → On hold.
  - Done → Resolved.
  - More ServiceNow states can be added during implementation.
- **"Status, no extra app" note and journey step 7** now name the states.
- **Open questions:** "Which states match each step?" is answered and removed. New: does On hold pause the SLA in the client's ServiceNow? (Check with its admin.)
- The people section and the key moved down slightly to make room.

## Agentic architecture v2.8 · 2026-11-05

`agentic-architecture-v2.8.html` (+ `.png`, 2× export). Status is shown where people already work, and the network pieces are marked. v2.7 is kept unchanged.

- **The live-view stack is gone:** Cosmos DB, Web PubSub, the Agent Control Centre web app and Front Door are removed. With no Control Centre, nothing would read Cosmos DB, and the orchestrator keeps the flow's state itself.
- **New on the far right, "Where people see status":**
  - **Azure DevOps:** the story's state at each step, plus an agent comment.
  - **ServiceNow:** "In progress", comments and "Resolved", only when the ticket started there.
  - Both are fed from the tools line (step ⑦). The people section now reads "See status, approve", and approvals happen in ADO or GitHub.
- **The right column is now "Monitoring & cost":** Application Insights (traces, cost per ticket), Monitor + Cost (alerts, budgets, a PM dashboard as an Azure workbook), and a "Status, no extra app" note listing what isn't built.
- **Network badges:**
  - 🌐 public door: API Management
  - 🔒 private endpoint: Event Grid, Foundry, Key Vault, Container Registry, Blob Storage
  - 🧩 inside the VNet: the Function App, Foundry Agent Service
  - A **NAT gateway** on the outgoing line gives one fixed outgoing IP.
  - The Private network box doubles as the legend.
- **Journey steps renumbered on the diagram:** ⑥ is now on the story-memory arrow, ⑦ on the status updates, ⑧ beside the people section.
- **Text updates:**
  - Journey steps 6–8, the "Real time" card, the v1 comparison table and the intro were rewritten.
  - The Control Centre open question is replaced by "Which ADO and ServiceNow states match each step?"
  - "Prove in a spike" adds two checks: Event Grid delivery to a private Function App, and hosted agents inside the VNet.

## Agentic architecture v2.7 · 2026-11-05

`agentic-architecture-v2.7.html` (+ `.png`, 2× export, default view). Shows what is an Azure service and what is our code, the resource group, and the safety net's links. v2.6 is kept unchanged.

- **Resource group:** a dashed "Resource group · new, one per environment" boundary inside the Azure subscription holds everything the platform creates.
- **Microsoft Entra ID moved outside the resource group and the subscription**, because it belongs to the whole organisation (tenant). Its identity arrow now runs from the new Entra box on the left.
- **One Function App block** holds the orchestrator, the safety net and scripted steps 1, 3A and 20, tagged **📝 Our code**.
- **API Management and Event Grid** are tagged **⚙️ Azure service**, so they read as configured services, not code.
- **The task queue (Service Bus) was dropped from the default design.** The orchestrator now starts agents itself, caps how many run at once, and retries before 👤 Needs Human.
- **"If volume grows" option:** a button above the diagram (or the dashed box under the Function App) shows Service Bus between the orchestrator and Foundry. The page also opens that view with `?sb=1`.
- **Safety-net lines:** dotted lines show the safety net checking ServiceNow, Azure DevOps and GitHub, and sending missed events into Event Grid (not through API Management).
- **The key moved to the far right**, with new entries for the safety-net line, the two kind tags and the resource group boundary.
- **New section, "Services we configure, and code we write":** an example for each of API Management, Event Grid, the Function App and the optional Service Bus.
- **Text updates:** journey steps 1 and 3 updated, and "Prove in a spike" adds "decide how many agents may run at once". The bottom band now has five boxes (Entra moved out).

## Agentic architecture v2.6 · 2026-11-05

`agentic-architecture-v2.6.html` (+ `.png`, 2× export). Shows what each control-plane part is in practice. v2.5 is kept unchanged.

- **The control plane is labelled "Settings and connections in Foundry, not apps or functions we build."**
- **Each cell has a kind tag and one line on who does what:**
  - 🪪 Entra Agent ID · **⚙️ Setting**: created for us; we grant its rights
  - 🛡️ Guardrails · **⚙️ Setting**: on by default; we tune it per model
  - 🔍 Tracing · **🔌 Connection**: to Application Insights; our code reports its steps
  - ✅ Evaluations · **📝 Feature**: we write the test sets and scripts
- **Application Insights** gains the label "↑ traces from Foundry stored here".
- **"Prove in a spike"** adds: confirm Foundry guardrails apply to the Claude deployments; if not, apply Azure AI Content Safety in the AI gateway.
- The bottom band moved down to make room. Nothing else changed.

## Agentic architecture v2.5 · 2026-11-04

`agentic-architecture-v2.5.html` (+ `.png`, 2× export). Shows which tools are remote, which run inside the sandbox, and which aren't MCP at all. v2.4 is kept unchanged.

- **Tools (MCP) box = remote MCP only, "registered once":**
  - Azure DevOps, GitHub, Sitecore Marketer, Figma, Vercel, ServiceNow, Sitecore Docs
  - a note: "Each agent gets only the tools on its own list"
  - The arrow label now reads "remote tool calls".
  - "Playwright browser" and the "+ CLI" part of the Sitecore chip moved out, because they run inside the sandbox.
- **New strip in Foundry Agent Service, "Inside every sandbox · from the team's agent image":**
  - **Local MCP:** Playwright, Next.js DevTools, Chrome DevTools, Storybook
  - **Command line:** git, pnpm, Sitecore CLI, marked "not MCP: plain tools next to the code"
- **Container Registry** now reads "Plugin, skills, hooks, local MCP servers, git, pnpm, Sitecore CLI".
- **Journey step ⑤** explains the per-agent tool list, and the split between remote tools and sandbox tools.
- The control plane and bottom band moved down to make room. Nothing else changed.

## Agentic architecture v2.4 · 2026-11-03

`agentic-architecture-v2.4.html` (+ `.png`, 2× export). Shows that each ticket goes through the event path twice: once for intake, once for delivery. v2.3 is kept unchanged.

- **Foundry Agent Service is split into two runs:**
  - **Intake run** (top row): the 0 Intake chip, "once per new ticket · writes the story to ADO".
  - **Delivery run · steps 1–21**, which "starts when 👤 the story is marked ready". It holds the Plan, Build, Verify & ship and Content columns. Intake is no longer listed under Plan.
- **The orchestrator box** says "Two runs per ticket": a new ticket starts the Intake run, and 👤 marking the story ready starts the delivery run (v8 order, loops, gates).
- **A note under the ticket sources** says "The event path runs twice per ticket: new ticket, then story marked ready."
- **Journey step ②** now explains both runs and the backlog owner's "ready" step between them. Before, it said one run started with the Intake Agent.
- Intake stays inside Foundry on purpose. It's an agent with a model and tools, so it uses the same runtime, identity, limits and tracing as every other agent.
- The Foundry box, control plane and bottom band moved down to make room. Nothing else changed.

## Agentic architecture v2.3 · 2026-11-03

`agentic-architecture-v2.3.html` (+ `.png`, 2× export). Follows the Agentic Delivery Flow v8.

- **New chip in Foundry Agent Service:** 2A Clarifier (Mid). Every agent column now uses slightly tighter spacing so the extra chip fits.
- **Gates:** "People decide at 2B, 3C, 15 and 21" (was 2A).
- References to the delivery flow now point to v8.
- Otherwise identical to v2.2.

## v8 · 2026-11-03

Adds a new agent to the flow (2A Clarifier), so this is a new major version.

- **New step 2A · Clarifier Agent:**
  - When the PRD Agent (or the PRD Review Agent, through it) has open questions, the Clarifier searches the ServiceNow ticket, linked ADO items, Figma and similar past stories.
  - It posts each question with a **suggested answer and its source**. With no source, it writes "no source found" instead of guessing.
  - Its tag reads "Suggests, never decides". It uses the Mid model, the new skill `clarify-story`, and the ServiceNow, Azure DevOps and Figma connectors.
- **The human gate moves to 2B · Story owner confirms the answers:**
  - The owner ticks or corrects each suggestion in ADO, then moves the story back to Drafting Requirements.
  - Only confirmed answers go into the story.
  - No round cap; reminder after 2 working days (unchanged).
- **Edges:** 2 → 2A "open questions" → 2B "questions + suggested answers" → back to 2 "answers confirmed".
- **Also updated:**
  - the Plan phase trigger text
  - the footer rule about missing story details
  - the skills list: `clarify-story` added, 24 skills in total
  - the connectors: ServiceNow is also read by the Clarifier; ADO is also used to post questions with suggested answers
- Every row from step 3 down moves down one place; step numbers are unchanged. The Intake Agent (step 0) is no longer highlighted as new; the Clarifier is.
- **Export:** the PNG is exported at 1.6× scale, because the extra row makes the page too tall for 1.7×.

## Agentic architecture v2.2 · 2026-11-03

`agentic-architecture-v2.2.html` (+ `.png`, 2× export). Removes made-up example values.

- The identity tag on the agent → AI gateway call now reads "🪪 agent ID + ticket no." instead of the example "prd-agent · ticket=42".
- The "Agent identity, in short" panel describes the IDs in words ("The PRD Agent, FE Agent, Intake… each have one") and says "Every call carries the ticket number" instead of quoting example values.
- Otherwise identical to v2.1.

## Agentic architecture v2.1 · 2026-11-03

> **Files removed:** the v2.1 files are no longer kept; v2.2 replaces them. This entry is kept as a record.

`agentic-architecture-v2.1.html` (+ `.png`, 2× export). Shows on the diagram how agents prove who they are, so it reads without the write-up. v2 is kept unchanged.

- **🪪 Identity markers** in a new green colour:
  - a pill on the agent → AI gateway call: "prd-agent · ticket=42" (the agent's ID plus the ticket label)
  - a dashed arrow from Entra ID to the control plane: "issues Agent IDs + hourly tokens"
  - a key entry: "Identity: who is calling"
- **"Agent identity, in short" panel** (bottom left):
  - **Who:** one ID per agent type (about 22), reused for every ticket
  - **Which ticket:** a label on each call, not an ID
  - **Where:** a fresh sandbox per task
  - **When:** a token about once an hour, checked by the gateway on every call
  - **Outside systems:** one login each, in Key Vault
- **Clearer wording:**
  - The AI gateway reads "Checks 🪪 ID → limits → forwards → logs".
  - The control-plane cell reads "Entra Agent ID · one per agent type".
  - Entra ID: "one ID per agent type; managed IDs for Azure; no personal accounts".
  - Key Vault: "one login per outside system".
  - Journey step ④ explains the ID check.
- The "Production foundation" label moved to the right so the new arrow doesn't cross it.

## Agentic architecture v2 · 2026-11-03

`agentic-architecture-v2.html` (+ `.png`, 2× export). A Foundry-first redesign, so this is a new major version. v1 (the hybrid design) is kept unchanged for comparison.

- **Microsoft Foundry is now the centre of the picture.** One large box holds:
  - the model catalogue and deployments (Large, Mid, Small; e.g. Claude Opus, Sonnet, Haiku)
  - the AI gateway (API Management): token limit per agent, quota, logs
  - Tools (MCP): connectors registered once and shared by the agents
  - **Foundry Agent Service (hosted agents)** running every v7 agent, grouped by phase, with model sizes. Each session runs in its own sealed sandbox built from the agent image.
  - the control plane: Entra Agent ID, guardrails, tracing, evaluations
- **"Real time" shown three ways**, with a marker for each:
  - ⚡ Event-driven: API Management → Event Grid → orchestrator (Durable Functions) → Service Bus, plus a timer as the safety net. Approvals are events that wake the orchestrator.
  - 👀 Live view: Cosmos DB change feed → Azure Web PubSub → Agent Control Centre, so people see agents working as it happens. Application Insights, Monitor and Cost Management are in the same column.
  - 🏭 Production foundation band: Entra ID, Key Vault, Container Registry, Blob Storage, private network, infrastructure as code.
- **Simpler to read than v1:** 9 numbered steps follow one ticket, explained under the diagram with illustrative timings. A plain-words "What is Foundry?" strip sits at the top.
- **New sections:** "What changed from v1", "Prove in a spike" (including confirming the Claude Agent SDK inside hosted agents) and "Open questions".
- Foundry feature status was checked on 2026-11-03 (hosted agents generally available).

## Architecture vision v2.1 · 2026-10-31

`architecture-vision-v2.1.html` (+ `.png`).

- **Fix:** in v2, "⤢ Open the full flow" opened the viewer, but the v7 diagram stayed blank. The viewer loaded `agentic-flow-v7.html` as a separate local file, and browsers block that between files opened from disk (and file previews can't reach the neighbouring file at all). v2.1 carries a copy of Agentic Delivery Flow v7 inside the page, so the viewer works wherever the page is opened. "Open in new tab" still opens the separate `agentic-flow-v7.html`.
- No visual changes apart from the version badge.

## Architecture vision v2 · 2026-10-31

`architecture-vision-v2.html` (+ `.png`, 2× export). v1 is kept unchanged.

- **Agent pool redesigned:**
  - It now shows **Ticket 1 opened up**, with the steps from the Agentic Delivery Flow in four columns (Plan, Build, Verify & ship, Content). Each agent chip shows its step number and the model size it calls (L, M, S), and the 👤 approval gates are marked.
  - **Ticket 2** is shown collapsed, with a phase progress bar, and **Tickets 3–15** as one dashed box. The task queue now explains how many agents run in parallel, so the pool no longer needs five ticket groups.
- **Link to the full flow:** an "⤢ Open the full flow" button in Ticket 1, and a badge at the top, open Agentic Delivery Flow v7 in a full-screen viewer, with an "Open in new tab" option. Without JavaScript the link opens v7 in a new tab. The PNG shows the button only.
- **Two-way agent ⇄ Foundry:** "① prompt" and "② reply" arrows between the pool and Foundry, plus an **agent loop** strip in the pool: ① the agent asks → ② the model replies → ③ the agent acts → repeat → ④ task done, and the ADO state moves on.
- **Container Registry clarified:**
  - It moved to the first slot of the bottom band, renamed "Shared services".
  - A new "image pulled at start" arrow runs from the registry to the pool. The box reads "Stored here · runs in the pool".
  - Fix: in v1 the "story memory · run records" arrow pointed into the registry. Now "run records" goes to Cosmos DB and "story memory" goes to Blob Storage.
- **Foundry:** model cards show the L, M and S letters, and a note says calls pass through the AI gateway in API Management.
- **Notes:** four notes (registry vs pool, the two-way agent loop, 15 tickets at once, the web app and the full flow). `?theme=light|dark` now works on this page.

## Architecture vision v1 · 2026-10-31

A new picture-style diagram, `architecture-vision-v1.html` (+ `.png`), for dashboards and slides. It has its own version numbers. It shows in literal terms what gets built, with an icon per component and little text.

- **People and tickets in** (left): backlog owner, developers, PM and platform owner, content approver; ServiceNow and Azure DevOps; Microsoft Teams shown as later.
- **Inside Azure, web app and entry points:**
  - Azure Front Door + WAF (the load balancer, in front of the web app only)
  - the **Agent Control Centre web app** (Azure App Service: ticket queue, live agents, approvals, cost and quality dashboards)
  - API Management (webhook entry and AI gateway)
  - Microsoft Entra ID
- **Inside Azure, orchestration and AI agents:**
  - Intake Agent (always on)
  - the orchestrator (Durable Functions in a Function App)
  - the task queue (Service Bus)
  - the **agent pool** (Azure Container Apps): 15 tickets in parallel, shown as 5 ticket groups with the agents working on each, plus "+10 more"
  - Microsoft Foundry with the Large, Mid and Small models
- **Inside Azure, database, storage and monitoring:**
  - Azure Cosmos DB (the database)
  - Blob Storage (story memory)
  - Container Registry (the agent image, where the team plugin lives)
  - Key Vault
  - Monitoring
- **Where the work lands** (right): GitHub, Vercel, Sitecore AI, Figma / Docs MCP. Plus a key to the icons.
- **Three notes:**
  - what the web app is
  - where the plugin runs (in the agent image, not the web app)
  - how 15 tickets run at once
- **Change of position:** this diagram includes the Control Centre web app, which the Agentic architecture v1 listed as "later".

## Agentic architecture v1 · 2026-10-31

A new companion diagram, `agentic-architecture-v1.html` (+ `.png`). It shows how the Agentic Delivery Flow (v7) runs on Azure, with Microsoft Foundry as the AI control plane. It has its own version numbers, separate from the flow diagram.

- **Outside Azure:** the ticket sources (ServiceNow, Azure DevOps Boards) and the delivery systems (GitHub, Vercel, Sitecore AI, Figma, Sitecore Docs MCP).
- **8 zones inside Azure:**
  - A · Intake & triggers (API Management, Functions, the Intake Agent)
  - B · Orchestration (Durable Functions, Service Bus, WIP limit)
  - C · Agent runtime (Container Apps jobs running the Claude Agent SDK + team plugin; Foundry hosted agents as an option)
  - D · Microsoft Foundry (Large/Mid/Small model deployments, the AI gateway, the agent registry, evaluations)
  - E · Identity & secrets
  - F · Data & memory (Blob story memory, Cosmos DB run ledger)
  - G · Observability & FinOps
  - H · Network & platform delivery
- **9 numbered arrows**, explained under the diagram, show how one ticket travels through the platform.
- **People at the gates:** backlog owner, reviewers, content approver, platform owner. Teams chat is shown as later.
- **Key questions answered:**
  - load balancers versus a queue with auto-scaling
  - right-sized models and cost control
  - running without personal accounts
  - what we measure
  - whether a PM or Scrum Master is needed
  - what is deliberately left out
- **Options compared:** hybrid (recommended), Foundry-native, Claude Managed Agents, GitHub-based (ruled out).
- The PNG is exported at the usual 2× scale.
- The full write-up is in `docs/azure-platform-recommendations.md`.

## v7 · 2026-10-31

Adds a new step (0) and a new layer (model per agent), so this is a new major version.

- **New step 0 · Intake Agent:**
  - It watches ServiceNow and Azure DevOps all the time. It sorts each ticket (bug, enhancement, new component, not for agents), spots duplicates, links the ServiceNow ticket, and creates or updates the ADO story with a suggested priority.
  - It uses the Small model, the new skill `triage-tickets` and a new ServiceNow connector. It's marked 🤖 because it decides how work is routed.
  - The backlog owner then marks the story ready for agents (step 1).
  - A new "Intake" phase sits at the top of the phase rail. Every other step moves down one row; step numbers are unchanged.
  - Teams chat with content authors is listed under "Later".
- **🧮 Model per agent:**
  - Every agent shows its model size: Large (judgement on small inputs: 3, 4A, 5, 6), Mid (code, tests, documents, content: most agents), Small (routine: 0, 11, 13 Performance, 14, 16), or No model (scripts: 1, 3A, 20).
  - A new panel explains the sizes (examples: Claude Opus, Sonnet and Haiku in Microsoft Foundry), pinning each size in configuration, and step-up on retry. The concept key has the new tag.
- **🪪 Runs as a service identity, not a person:** a new panel. It covers Entra identities for agents, a GitHub App for PRs, and service accounts for ADO, ServiceNow, Sitecore, Vercel and Figma. Secrets are in Key Vault, and model costs go on the platform's own Azure subscription.
- **☁️ Where it runs:** a new panel. The flow runs on Azure, not in GitHub Actions; GitHub stays the code host. It points to the new Azure platform architecture diagram.
- **Other changes:**
  - Story memory is now kept in Azure storage.
  - The plugin note says the same package runs in the Azure runtime and on developers' machines.
  - The PR Agent commits as the team's GitHub App.
- **Footer:**
  - three new rules (service identities, pinned models checked with evals, ServiceNow kept in step by the orchestrator)
  - ServiceNow added to the connectors (12 in total)
  - `triage-tickets` added to the skills (23)
  - "Later" updated: model choice and worktrees are now covered; Teams intake and a short route for small bugs were added
- **Export:** the PNG is exported at 1.7× scale instead of 2×, because the page is now too tall for Chrome to paint at 2× in one screenshot.

## v6 · 2026-10-25

Reorders the Build phase (review before test), so this is a new major version.

- **Created by Archana Singh** is now shown in the header.
- **Build triad: develop → review → test, run per task.**
  - Steps 8 and 9 swapped. **8 is now Code Quality ‖ Security (review)**, and **9 is now Test & Storybook (test)**. Every MCP, skill, hook and memory tag moved with its step.
  - The **70% coverage check moved** from Code Quality to the Test step, because tests only exist after review.
  - **New loop:** failing tests go back to 7 (max 3). Review fixes still go back to 7 (max 3).
  - **New ADO state "Testing"** for step 9, giving 19 states in total.
  - The orchestrator runs the triad per task, in the Task Planner's order. This is shown in the Orchestration panel and the Build phase rail.
  - It isn't labelled TDD on purpose: TDD means the tests are written before the code.
- **Spec-driven development (SDD) marked.** A new "Development approach" panel explains SDD and the triad. 📐 SDD markers are on steps 2–7, 13 and 17, and 🔁 Triad markers are on 7, 8 and 9.
- **Done per component:** code + business logic + unit tests + Storybook story, kept together. This is a new rule in the footer.
- **Skill names, one per agent:** each 📘 tag now shows the skill's name for implementation, in kebab-case (for example `write-prd`, `plan-tasks`, `raise-bugs`). Agents that had no skill now have one, and a new footer box lists agent → skill.
  - Shared rules (Sonar, Sitecore templates, brand standards and so on) now live in AGENTS.md, and skills point to them. The memory panel and concept key explain this.

## v5 · 2026-10-25

Adds a new step to the flow (2A), so this is a new major version.

- **New step 2A · 👤 Story owner adds the missing details:**
  - The PRD Agent first checks the story against a 📘 Story readiness checklist.
  - If details are missing, it asks the story owner in an ADO comment (@mention). The story moves to the new state **👤 Awaiting Story Details** (18 states in total).
  - The owner updates the story and moves it back to Drafting Requirements, and the PRD Agent drafts again.
  - This loop has no round cap, because a person is already answering. A reminder goes out after 2 working days.
  - PRD Review gaps still go back to the PRD Agent, which uses 2A if it needs a person.
  - Arrows: "missing details" (2 → 2A), "details added" (2A → 2), and "story complete" (2 → 3, drawn on the right). The review loop now runs on the left.
- **New MCP connectors:**
  - **Next.js DevTools MCP** on the FE Agent (7): live build, runtime and type errors from the local dev server.
  - **Playwright MCP** on the Test & Storybook Agent (8, checking stories in a browser), Preview QA (13) and QA Expert (17). It replaces the generic "Browser" connector.
  - **Storybook MCP\*** on the FE Agent (7: find existing components and their docs to reuse, instead of building near-duplicates) and on 8 (write and test stories). *Needs a Storybook upgrade from 8.6 to 10, which is listed under "Later".
  - **Chrome DevTools MCP** on the Performance Agent (13).
  - **Vercel MCP** on 11, 13 (both agents), 14 (logs) and 16. It replaces the plain "Vercel" connector.
- **The Preview QA Agent now adds Playwright end-to-end tests to the PR.**
- The footer has a new rule, the updated connector list and state list, and new "Later" items (Vercel sign-in, Storybook upgrade).
- Every step below 2 moved down one row; step numbers are unchanged.

## v4.4 · 2026-10-25

- **Requirements split into two ADO states** (now 17 states): **Drafting Requirements** (step 2 · PRD Agent) and **Reviewing Requirements** (step 3 · PRD Review Agent, and the 3A decision). When the review sends gaps back, the story returns to Drafting Requirements.
- **Authoring guide attached to the story:** step 10 writes the guide as a Markdown (.md) file, and the orchestrator attaches it to the ADO story. It's replaced whenever the guide is rewritten after a fix round.
- **The guide is read from the story later:** Test Page Agent (12), QA Expert (17) and Content Authoring Agent (18).
- A new rule in the footer explains the guide attachment. The footer state list is updated.

## v4.3 · 2026-10-25

- The **"Later"** box now notes that the Sitecore MCP details are settled at implementation: sign-in for automated runs (via the Sitecore authentication API), pointing the Marketer MCP at dev, and confirming the Docs MCP address.
- For now the diagram only shows that both MCPs are available and where they'll be used. Otherwise identical to v4.2.

## v4.2 · 2026-10-25

Adds Sitecore's two official MCP servers as connectors:

- **🔌 Sitecore Docs MCP** (Sitecore developer documentation) on 4A Blueprint, 6 Contract, 7 FE and BE agents, and 10 Authoring Guide.
- **🔌 Marketer MCP** (SitecoreAI pages, content, components, assets, Brand Kit) on 12 Test Page Agent (replaces "Sitecore dev"), 17 QA Expert (read), 18 Content Authoring and 19 Content Review (read). It replaces the separate "Brand Kit" tags.
- The BE Agent and Merge & Deploy keep "Sitecore dev", because the Marketer MCP can't create templates or renderings, or deploy.
- The footer's connector list now explains the split between the three Sitecore connectors.

## v4.1 · 2026-10-25

ADO state updates (now 16 states):

- **Planning → Finalizing Requirements** (steps 4–6: Spec, Task Planner, Contract).
- **New: Preparing Authoring Guide** (step 10). Previously this step was part of In Development.
- **Dev Verification split into two:** **Deployment in Progress** (step 16 · Merge & Deploy) and **Under QA Review** (step 17 · QA Expert → Done if there's no content phase).
- The footer state list is updated to match.

## v4 · 2026-10-25

Adds the Azure DevOps state layer, so this is a new major version.

- **Every step shows the ADO state** the user story is set to when that step starts. The 14 states are one per stage: New, Requirements, Design, 👤 Awaiting Design Pick, Planning, In Development, Quality Checks, Preview Testing, 👤 Awaiting PR Approval, Dev Verification, Content Authoring, 👤 Awaiting Publish Approval, Done, and 👤 Needs Human (any loop that hits 3 rounds).
- **The scripted orchestrator sets the state** (no AI cost). Agent-level detail is added as a comment on the story.
- The ADO state is for **people**. Agents coordinate through the orchestrator and story memory, not the state, which avoids two parallel agents overwriting it.
- The Blueprint Agent shows no story state, because it runs once and isn't tied to a story.
- The footer lists all states in order (for the DevOps admin). A **"Show ADO states"** toggle hides them; `?states=off` opens the page with them hidden.

## v3.1 · 2026-10-25

- **Step 2 · PRD Agent** now uses the Figma design (🔌 Figma, if the story has a URL) when writing the PRD.
- **Step 3 · PRD Review Agent** also checks that the PRD matches the Figma design, and sends mismatches back as gaps.
- **Step 3B · Prototype Agent** builds the 3 variants from the *final, reviewed* PRD, so a change in review doesn't force new prototypes.
- Otherwise identical to v3.

## v3 · 2026-10-25

Adds a design step to the flow, so this is a new major version.

- **Design reference branch after the PRD is approved:**
  - **3A · Figma URL in the story?** If yes, the Figma file is the design reference (🔌 Figma connector).
  - **3B · Prototype Agent** (only if there's no Figma URL) builds 3 clickable prototype variants from the final PRD.
  - **3C · 👤 You pick one prototype**, which becomes the design reference.
  - The design reference is the single source of truth for how the result should look. The Spec Agent records it, the FE Agent builds to it, and the Preview QA Agent checks against it.
- **Memory files:** app memory is now **AGENTS.md** (shared, in the repo). Developer memory is **CLAUDE.md** (personal, git-ignored).
- The Plan phase notes it pauses at 3C only when prototypes are needed. The footer adds Figma to the connectors and the design-reference rule.
- Steps after 3 keep their numbers. The new steps use 3A–3C, like 4A.

## v2.1 · 2026-10-25

- Concept key now includes **🧩 Subagent**: every agent box is a subagent with its own context and tools, and it hands back only its result. There are no per-step tags, because it applies to every agent.
- Otherwise identical to v2.

## v2 · 2026-10-25

Adds the agentic-concepts layer on top of v1.2 (same flow, steps and swimlanes).

- **Orchestration panel:** scripted by default (fixed order, parallel steps, max-3 loop counts, pauses at gates), running phase by phase from events. AI orchestration only where judgment is needed (🤖 on the Task Planner and Contract Agent).
- **Phase triggers** in the Phase column: when each phase starts, pauses and resumes.
- **Memory panel:** app memory, story memory and developer memory, with tags showing what each step reads or writes.
- **Coloured tags on every step:** 🔌 connectors (Azure DevOps, GitHub, Sitecore, Vercel, browser), 📘 skills, 🪝 hooks, 🧠 memory, 🤖 AI decides.
- **"Show agentic concepts" toggle** (HTML only) hides the layer to get back the focused v1.2 view. Add `?concepts=off` to the URL to open it hidden.
- The footer now lists the connectors used, plus "Later" items: worktrees, model choice per agent, observability, evals.
- The plugin is still a side indicator.

## v1.2 · 2026-10-25

- The category headers now stay visible: when you scroll down in the HTML version, a bar at the top keeps showing all 7 categories, lined up with their lanes. It also follows horizontal scrolling on narrower screens.
- The diagram content is unchanged from v1.1. The PNG looks the same apart from the version badge, because floating headers only work in the browser.

## v1.1 · 2026-10-25

Small revision of v1. Major versions (v2, v3, …) are kept for structural changes, such as adding the agentic-concepts layer.

- The PRD Review → PRD Agent loop and the Contract Agent → Task Planner loop are now also capped at 3 rounds, then escalate to a human.
- Every loop in the flow now has the same rule, so the legend and footer state it once for all loops.

## v1 · 2026-10-25

First version: the full flow from user story to published content.

- 7 function-based swimlanes: Product & Requirements, Architecture & Planning, Development, Code Quality & Security, Testing & Validation, Release & Deployment, Content Operations.
- 21 numbered steps across 4 phases: Plan, Build, Verify & ship, and Content (optional).
- Human gates: PR approval (step 15) and publish approval (step 21).
- Fix loops capped at 3 rounds, then a human: quality/security fixes, bug fixes and content review.
- Loops with no cap decided yet: PRD review → PRD Agent, and Contract Agent → Task Planner.
- Plugin shown as a side indicator only.
- Not yet shown: orchestration, memory, skills, hooks and MCP connectors per stage (planned for v2).
