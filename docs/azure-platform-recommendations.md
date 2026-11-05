# Agentic Delivery Platform on Azure: Recommendations

**Created by Archana Singh** · Draft for PM review · 3 Nov 2026 (updated from the 31 Oct hybrid version)

Companion to two diagrams:
- `diagram-versions/agentic-flow-v8.html`: what the agents do, step by step.
- `diagram-versions/agentic-architecture-v2.3.html`: where and how it all runs on Azure, Foundry-first.

`diagram-versions/agentic-architecture-v1.html` (the earlier hybrid design) is kept for comparison. It's now the fallback option (section 11).

---

## 1. The goal

The developers spend their time on enhancements and day-to-day operations tickets, which is DevOps-style work. The team can't grow, so an agent team should take over that easier work:

- pick up a ticket (a bug, an enhancement or a new component)
- understand what to do
- build, test and ship it end to end

The developers then get their time back for innovation.

- **At first:** people watch the agents closely and do a final smoke test.
- **Later:** the flow becomes fully agentic, with people only at the approval gates.
- **Scope now:** the Sitecore flow (v8). The same platform can serve other platforms later.

**Fixed requirements from the discussion**

| Requirement | How the design meets it |
|---|---|
| Host on Azure, with Microsoft Foundry at the centre | Foundry holds the models, runs the agents and governs them; the other Azure services handle events, live screens and data |
| The flow runs in real time | Event-driven start and resume, a live view for people, and a production-ready foundation (section 4) |
| A model per agent, sized to the task, for cost | Large / Mid / Small / No model, pinned per agent (section 6) |
| Runs without anyone's personal account or token budget | Service identities everywhere, and model spend on the platform's own subscription (section 7) |
| Not run on GitHub | Agents run in Foundry on Azure; GitHub stays only as the code host |
| An Intake Agent that watches the ticket pipeline | Step 0 watches ServiceNow and Azure DevOps |
| Teams chat with content authors | **Later.** Not in scope now |

---

## 2. Recommended setup

Put **Microsoft Foundry** at the centre. It does three jobs:
- **The model shop:** Large, Mid and Small model deployments (for example Claude Opus, Sonnet and Haiku), behind an AI gateway with token limits per agent.
- **The home where agents run:** every v8 agent runs as a **hosted agent in Foundry Agent Service**. Each session gets its own sealed sandbox, built from the team's agent image (the Claude Agent SDK, the team plugin, Node 24, pnpm, Playwright and the Sitecore CLI). The MCP connectors are registered once in Foundry and shared by the agents.
- **The control room:** Entra Agent ID per agent, guardrails, tracing and evaluations.

Around Foundry:
- **Events:** API Management, Event Grid, a scripted orchestrator (Azure Durable Functions) and a Service Bus task queue start and resume work within seconds.
- **Live view:** Cosmos DB, Azure Web PubSub and an **Agent Control Centre** web app show people what the agents are doing as it happens.
- **Foundation:** identity, secrets, a private network and infrastructure as code.

**Why this shape:**
1. **It follows the PM's direction.** Foundry is the one place to choose models and govern agents.
2. **It still reuses the design you already have.** Hosted agents run the team's own container image, so the v6/v7 skills, hooks and plugin can carry over. This must be proven in the spike (section 12, phase 0).
3. **Coding agents get a real workbench.** A sandbox per session can clone, build and test the monorepo.
4. **Orchestration stays predictable and cheap.** Durable Functions can wait days at a human gate at no cost, and they count loops exactly.
5. **Everything stays on Azure** (billing, identity, network, monitoring).

Hosted agents in Foundry Agent Service became generally available in June 2026, and Claude models are generally available in Foundry.

---

## 3. What a "sealed sandbox" is

A **sandbox** is a small, temporary computer that Foundry creates for one agent session, then removes. A **session** is one agent working on one task for one ticket, such as the FE Agent building the component for ticket 42.

- **Its own:** each session gets a fresh, private machine (Microsoft calls it a *micro-VM*, a very lightweight virtual machine).
- **Sealed:** it can't see other agents' machines or files, or the platform underneath. It reaches the outside only through the routes we allow (GitHub, the Sitecore dev environment, the AI gateway, and so on).
- **Built from the agent image:** it starts from the image in Container Registry, already set up with the toolchain and the team plugin. It's like a new laptop that arrives with everything installed.

**Why it matters here:**
1. **Coding agents run real commands.** `pnpm install`, builds, unit tests and Playwright tests need a real machine, not just a chat with a model.
2. **15 tickets at once don't collide.** Each agent has its own copy of the repo and its own branch.
3. **Safety.** If an agent goes wrong, or is tricked by text hidden in a ticket (*prompt injection*), the damage stays inside its sandbox.
4. **Same start every time.** Every run begins from the same tested image, with no leftovers from the last job.

**To check in the spike:** how long a session can run, how much CPU and memory it gets (a monorepo build is heavy), and when it's cleaned up.

---

## 4. "Real time", three ways

| Meaning | How the design delivers it |
|---|---|
| **⚡ Event-driven** | Webhooks from ServiceNow, Azure DevOps and GitHub reach API Management. Event Grid routes them, and the orchestrator starts a run or resumes from a gate within seconds. A timer poll every few minutes is only the safety net for a missed webhook. |
| **👀 Live view** | Each agent step writes its status, tokens and cost to Cosmos DB. Its change feed pushes the update through Azure Web PubSub, so the Agent Control Centre updates with no refresh. People see which agent is on which ticket right now, and approve gates from the same screen. |
| **🏭 Production-ready** | Real Azure services, service identities instead of personal accounts, secrets in Key Vault, a private network, guardrails, evaluations, and dev, test and prod built the same way from code. |

**One ticket, step by step** (matches ① to ⑨ in the diagram; timings are illustrative):

1. **0:00** A ticket is created; the webhook reaches API Management.
2. **+2 s** Event Grid routes it; the orchestrator starts one run for the ticket, beginning with the Intake Agent.
3. **+5 s** The next step goes on the task queue; Foundry Agent Service starts that agent in a fresh sandbox.
4. **Seconds to minutes:** the agent loops with its pinned model through the AI gateway: ask, reply, act.
5. Through the tools (MCP), it commits to GitHub, opens the PR, gets a Vercel preview and creates Sitecore items.
6. Story memory goes to Blob Storage; status, tokens and cost go to Cosmos DB.
7. **Under 1 s later:** the Control Centre updates live.
8. **👤 Gates:** the run sleeps at no cost until a person approves in the Control Centre, ADO or GitHub. The approval is an event, so the run carries on within seconds.
9. **All the time:** traces, evaluations and cost flow to the control plane and Application Insights. Alerts fire if a ticket is stuck.

---

## 5. The building blocks

| Zone | Azure service | What it does | Why it's needed |
|---|---|---|---|
| ⚡ Events & orchestration | **API Management** | One secure entry point for webhooks from ServiceNow, Azure DevOps and GitHub (tickets, state changes, approvals) | Checks each sender; one place to secure |
| | **Event Grid** | Routes each event to the right place in seconds | Event-driven start and resume |
| | **Durable Functions** (Durable Task Scheduler) | One orchestration per story: order, parallel steps, loop counts, gates, ADO states, ServiceNow sync. Runs the scripted steps 1, 3A and 20. | The scripted orchestrator from v2–v7. Foundry workflows are an option to test. |
| | **Service Bus** | Queue of agent tasks; one session per story; dead-letter queue → 👤 Needs Human | Lets 15 tickets wait safely; nothing is lost |
| | **Functions timer** | Polls ServiceNow and ADO every few minutes | The safety net if a webhook is missed |
| Microsoft Foundry | **Model catalogue and deployments** | Large, Mid and Small models (for example Claude Opus, Sonnet, Haiku) | Right-sized models |
| | **AI gateway** (API Management, linked in Foundry) | Token-per-minute limit and monthly quota per agent; spreads load; retries; logs tokens per agent | Cost control and fair use of quota |
| | **Tools (MCP)** | ADO, GitHub, Sitecore Marketer + CLI, Figma, Vercel, Playwright, ServiceNow, Sitecore Docs, registered once | Every agent uses the same, governed connectors |
| | **Foundry Agent Service (hosted agents)** | Runs every v8 agent, each session in its own sealed sandbox | The workbench where code gets written and tested |
| | **Control plane** | Entra Agent ID per agent, guardrails (content safety, prompt shields), tracing, evaluations | Governance and quality in one place |
| 👀 Live view & data | **Cosmos DB** | Live status and run ledger: ticket, step, agent, model, tokens, cost, result | Feeds the live view and FinOps |
| | **Azure Web PubSub** | Pushes each status change to open screens instantly | Live updates without refreshing |
| | **Agent Control Centre** (App Service, behind Front Door + Entra sign-in) | Live board, approve buttons for the 👤 gates, cost and quality per ticket | One screen for the people in the loop |
| | **Application Insights + Log Analytics** | Live traces of every step, tool call and model call (Foundry tracing) | Find out what an agent did and why |
| | **Azure Monitor + Cost Management** | Alerts and budgets | Catch stuck stories and overspend early |
| 🏭 Production foundation | **Entra ID**, **Key Vault** | Managed identities, Agent IDs, a GitHub App; all outside credentials stored and rotated | Runs without people's accounts |
| | **Container Registry** | Holds the agent image (Agent SDK + plugin + toolchain) | Every sandbox starts from the same tested image |
| | **Blob Storage** | Story memory (one folder per story) | Agents hand work over through it |
| | **VNet, private endpoints, NAT Gateway** | Private services; one fixed outgoing IP | Security; partners can allow-list the platform |
| | **Bicep + a pipeline** | Builds identical dev, test and prod copies, with one Foundry project each | Repeatable, reviewable infrastructure |

**Databases, in short:** there are two. Blob Storage holds files (story memory) and Cosmos DB holds records (live status and the run ledger). The orchestrator keeps its own state. App memory (AGENTS.md, the blueprint, lessons learned) stays in the GitHub repo. A search index (Azure AI Search) can come later.

**Web apps, in short:** one, the Agent Control Centre. It's what makes the live view possible. If the team decides ADO and dashboards are enough at first, it can move to a later phase (section 13).

---

## 6. A model per agent, and cost control

| Size | Used for | Agents (v8) | Example model |
|---|---|---|---|
| **Large** | Judgement on small inputs | 3 PRD Review · 4A Blueprint · 5 Task Planner · 6 Contract | Claude Opus |
| **Mid** | Writing code, tests, docs, content | 2 PRD · 2A Clarifier · 3B Prototype · 4 Spec · 7 FE + BE · 8 Code Quality + Security · 9 Test & Storybook · 10 Authoring Guide · 12 Test Page · 13 Preview QA · 17 QA Expert · 18 Content Authoring · 19 Content Review | Claude Sonnet |
| **Small** | Routine, well-defined work | 0 Intake · 11 PR · 13 Performance · 14 Bug Raising · 16 Merge & Deploy · Ops Report | Claude Haiku |
| **No model** | Scripts | 1 Story arrives · 3A Figma check · 20 Final Page Report · the orchestrator | none |

**Cost levers, in order of impact:**
1. **Right-size each agent** (the table above). It's the biggest lever, because most tokens are spent in the Mid-size coding agents.
2. **Scripts for mechanics.** The orchestrator, state updates and the page report cost nothing.
3. **Prompt caching** for repeated context (AGENTS.md, skills, the spec).
4. **Step-up on retry.** Start at the normal size; only the third round of a loop uses the next size up.
5. **Brakes:** per-agent token-per-minute limits and monthly quotas in the AI gateway, and Azure budgets with an alert at 80%.
6. **Sandbox time.** Hosted-agent sessions cost compute while they run, so agents should finish and close their session rather than sit idle. Measure this in the spike.

**Teacher note: pin, don't route.** Foundry's *model router* picks a model per request automatically. That's useful for general chat traffic, but a delivery pipeline needs **predictable, testable** behaviour. Pin a size per agent, and change it only after evals pass.

**Model versions:** pin exact versions at implementation. Unpinned aliases can jump to a newer version that isn't deployed in your Foundry account.

---

## 7. Running without anyone's personal account

| System | Identity used | Notes |
|---|---|---|
| Azure services and agents | Managed identity, Entra Agent ID | No passwords at all |
| Models (Foundry) | Agent identity through the AI gateway | Spend goes to the platform subscription, not to anyone's seat |
| GitHub | **GitHub App** | Bot author on PRs; access limited to this repo; no personal access token |
| Azure DevOps | Service principal or managed identity | The official ADO MCP server is remote and uses Entra sign-in |
| ServiceNow | Integration user with least rights | ServiceNow offers its own MCP server console (check the version and licence) |
| Sitecore | Automation client credentials per environment | Already noted for the Marketer MCP / Agent API |
| Vercel | Team-level token | **Check:** Vercel's MCP uses a browser sign-in today |
| Figma | Service account token | |
| People using the Control Centre | Their own Entra sign-in | People sign in to approve; agents never use a person's sign-in |

All secrets go in **Key Vault** and are rotated. When a person leaves or changes role, nothing breaks.

---

## 8. Scaling: do we need load balancers?

**Short answer: not for the agents. Yes, a small one, in front of the Control Centre.**

- **What a load balancer does:** it spreads incoming web requests across servers that are already running. That suits websites and APIs.
- **Why agent work is different:** each ticket is a long job of minutes to hours, with many steps. The industry-standard pattern here is a **queue plus auto-scaling workers**, also called *queue-based load leveling*.
  1. 15 tickets arrive, and 15 orchestrations start.
  2. Each agent step becomes a message in the Service Bus queue.
  3. Foundry Agent Service starts one sandbox session per agent task, up to the limit we set.
  4. When there's no work, no sessions run.
- **The Control Centre is a normal web app**, so **Azure Front Door** (with a firewall) sits in front of it.

**The real limits on parallel work**, and how the design handles each one:

| Limit | What happens if ignored | Handling |
|---|---|---|
| Model token quota (tokens per minute) | Throttling (429 errors), slow agents | The AI gateway spreads calls across deployments, retries, and sets per-agent limits |
| Hosted-agent session limits | Tasks wait for a free sandbox | The WIP limit; check the quota for the subscription in the spike |
| The one shared Sitecore dev environment | Agents clash on items or deploys | Items are unique per story (already decided); the WIP limit caps parallel BE work |
| Human review capacity | 15 PRs waiting for approval | The WIP limit; this is the real bottleneck early on |
| Vercel and partner API rate limits | Failures at preview or deploy steps | Retries with back-off; the WIP limit |

**WIP limit** (work in progress) means the maximum number of stories in Build at once. Start small (for example 3) and raise it as trust and quotas grow.

---

## 9. Moving off GitHub as the agent runner

- **Changes:** agents no longer run in GitHub Actions or Claude Code in CI. They run in Foundry Agent Service on Azure.
- **Stays the same:** GitHub is the code host, and PRs, SonarQube and CodeQL stay as they are. The PR Agent opens PRs as the GitHub App.
- **GitHub events still matter:** a PR approval (gate 15) sends a webhook to API Management, which wakes the orchestrator.

---

## 10. People, governance and monitoring

### Do we need a PM or Scrum Master?

**Recommendation: no Scrum Master for the agents, but yes to real people in two roles, and yes to proper project governance for building the platform.**

**1. The agents don't need Scrum.**
- Scrum ceremonies (sprints, stand-ups, retros) exist to coordinate *people*.
- Agents work in **flow**: they pull the next ready story from a prioritised queue, one after another. This is **Kanban**, and the WIP limit is the brake.
- Sprint planning for agents adds overhead without benefit.

**2. Two human roles can't be handed to an agent.**
- **Backlog owner** (a product owner role): decides priority and marks stories "ready for agents" (step 1).
  - Priority is a business decision with accountability, so an agent can only *suggest* it. The Intake Agent does exactly that.
- **Platform owner** (likely you at the start): owns budgets, evals, model choices, the plugin's releases and incidents.
  - In the industry this is often called **AgentOps**.

**3. Building the platform is a real project.**
- Treat it like any product build:
  - a backlog
  - sprints
  - a PM or Scrum lead
  - stakeholders (security, cloud/infra, the ADO admin, ServiceNow owners)
  - a budget
  - demos
- It can't be built quietly in the basement.

**4. Where agents can help.** Each needs prerequisites that people set:
- **Backlog hygiene:** flag stale or duplicate tickets, suggest priority from agreed rules (part of the Intake Agent).
- **Story clarification (decided, 3 Nov 2026):** when the PRD Agent or PRD Review Agent has open questions, the **2A Clarifier Agent** (Mid model, skill `clarify-story`) searches the ServiceNow ticket, linked ADO items, Figma and similar past stories, and posts each question with a **suggested answer and its source**. The story owner confirms or corrects each one at **👤 2B**. Rules:
  - every suggestion names its source; with no source it writes "no source found, please answer" instead of guessing
  - only answers the person confirmed go into the story
  - the agent never moves the story on; the owner moves it back to Drafting Requirements, which wakes the orchestrator
  - **Why not let an agent answer on its own:** missing requirements are business decisions. An agent that fills them in alone would be guessing, and the PRD would rest on made-up requirements.
- **Ops Report Agent** (Small model): a weekly summary of cost, throughput and anomalies from the dashboards.
- **Prerequisites first:** the KPIs, budgets and thresholds must be defined by people before an agent reports on them.
- **Rule:** these agents **report and suggest; people decide.**

### FinOps (cost)
- **Per-agent token metrics** from the AI gateway, plus a **cost per run** in the run ledger. Together these give **cost per ticket** and **cost per agent**, shown live in the Control Centre.
- **Azure budgets** per environment, with alerts at 50%, 80% and 100%.
- **Gateway quotas** stop runaway spend: when a quota is used up, the agent stops and the story goes to 👤 Needs Human.
- **Tags** on every resource (environment, agent), so Cost Management can group spend.
- **A monthly review** by the platform owner: model mix, the most expensive agents, caching savings, sandbox time.

### Agent performance (quality and flow)
- **Flow:** lead time from ready to PR and from ready to done; how long tickets wait at each gate.
- **Quality:**
  - first-pass success per agent
  - loop rounds per step
  - 👤 Needs Human rate
  - defects found at preview versus after merge
  - PRs approved without changes
- **Health:** failed runs, queue length, stuck stories, throttling.
- **Evals:** a fixed set of past stories re-run in Foundry before each plugin, model or skill change, with the pass rate tracked over time.

These line up with the measures already proposed for the pilot in the reference guide.

---

## 11. Options compared

| Option | What runs where | Strengths | Watch out for |
|---|---|---|---|
| **A · Foundry-first ✅ Recommended** (diagram v2) | Foundry: models, gateway, tools, hosted agents running the team's own image (Claude Agent SDK + plugin), control plane. Durable Functions orchestrate. | One place for models, agents and governance; follows the PM's direction; keeps the v7 skills, hooks and plugin; sandbox per session | **Prove that the Claude Agent SDK runs inside hosted agents.** One news report says it does, but no Microsoft or Anthropic guide was found yet. Also prove the session limits for monorepo builds. |
| **A2 · Foundry-native with Microsoft Agent Framework** | Same as A, but the agents are rebuilt on Microsoft Agent Framework with Claude models | Microsoft's own, documented framework for hosted agents | Skills, hooks and the plugin must be rebuilt. **Fallback 1** if the Agent SDK isn't supported in hosted agents. |
| **B · Hybrid** (diagram v1) | Foundry for models, gateway, identity, tracing and evals; agents in our own Container Apps jobs | Proven building blocks; full control of the workbench | More Azure pieces to own; governance assembled by hand. **Fallback 2** if hosted agents can't run the toolchain well. |
| **C · Claude Managed Agents** | Anthropic hosts and runs the agents | The least infrastructure to build | Runs outside Azure, not on Azure billing; conflicts with "host it on Azure" |
| **D · GitHub-based** | GitHub Actions / Claude Code in CI | Closest to the current setup | **Ruled out**: agents shouldn't run on GitHub |

**Honest view:** A, A2 and B share most of the platform: events, the orchestrator, the live view, data, identity and the network. They differ only in where the agent code runs. So the spike decides one box, not the whole architecture, and switching later is cheap.

**Teacher note: Durable Functions versus Foundry workflows.** Foundry workflows can also chain agents. For v8, Durable Functions stay recommended because the flow waits days at human gates, counts loops exactly and keeps ADO and ServiceNow in step. Test Foundry workflows in the spike and switch only if they handle long waits and exact loop limits as well.

---

## 12. Suggested rollout

| Phase | What | People's role | Exit criteria |
|---|---|---|---|
| **0 · Spike** (2–3 weeks) | One FE step (7 · FE Agent, Mid model) end to end as a Foundry hosted agent from the team's image; the Claude Agent SDK inside it; service sign-in to each system; a gate that waits and resumes from an approval event; cost of one story | Developers build and compare | A decision between A, A2 and B; session limits known; a cost estimate per story |
| **1 · Foundation** | The platform built as code (dev/test/prod), identities, gateway, ledger, the Control Centre with the live board, dashboards | Platform owner, cloud team | Security review passed; budgets live |
| **2 · Pilot** | One simple component story, steps 1–10 (the pilot already proposed), then a few ServiceNow bugs | A developer watches every step live | The pilot measures look healthy |
| **3 · Supervised** | Steps 0–17; WIP limit of 2–3; people smoke-test every result | Reviewers at the gates | First-pass success and escalation rate at target |
| **4 · Autonomous** | Raise the WIP limit; people stay at the gates only; content phase on | Gates only | Stable cost per ticket; defects after merge at or below the human baseline |

---

## 13. Open questions

1. **Bugs: full flow or a short route?** A small ServiceNow bug probably shouldn't go through PRD, prototypes and blueprint.
   - **Recommendation:** the Intake Agent tags the work type, and the orchestrator skips the design steps for bugs and small enhancements.
   - **To confirm.**
2. **Who is the backlog owner** who marks stories "ready for agents"?
3. **Agent Control Centre:** wanted in the first release, or are ADO and dashboards enough at first?
4. **ServiceNow:**
   - Can the instance send webhooks, or will it rely on the timer only?
   - Which version and licence do you have? It affects the MCP server option.
   - Who owns the integration user?
5. **Vercel:** is a non-personal option available for its MCP, or should the agents use Vercel's API with a team token?
6. **Azure landing zone:**
   - Is there an existing company subscription, network and policies to fit into?
   - Which region? It decides Claude model availability, hosted-agent availability and data residency.
7. **Data rules:** can ticket content and code be sent to the Claude models in Foundry? Check with security and legal; Foundry keeps the traffic inside Azure.
8. **Platform repo and pipeline:** where will the platform's own code and infrastructure code live, and which pipeline deploys it?
9. **Budget:** a monthly cap for the pilot.
10. Earlier open items still apply (see `HANDOFF.md`): the coverage target, the Storybook upgrade, ADO custom states, the Docs MCP address.

### Reminders for later

- **"Save before exit" rule.** Each sandbox is removed when its session ends, so nothing important may live only inside it. Before it finishes, every agent writes its work to a lasting place:
  - code to a git branch or PR
  - story documents to Blob Storage (story memory)
  - its run record (step, tokens, cost, result, plugin version) to Cosmos DB
  - a progress comment to the ADO story

  **Known gap:** the Agent SDK saves each agent's conversation transcript to local disk by default. A "save on finish" hook should upload it to Blob Storage for debugging and audits. To design at implementation.

---

## Sources

- [Generally available: hosted agents in Foundry Agent Service (Microsoft message center)](https://mc.merill.net/message/AZ563596)
- [Microsoft launches hosted agents in Foundry with secure sandboxes (Neowin)](https://www.neowin.net/amp/microsoft-launches-hosted-agents-in-foundry-with-secure-sandboxes/)
- [What's new in Microsoft Foundry](https://learn.microsoft.com/en-ie/Azure/foundry/whats-new-foundry)
- [Foundry SDK overview (Anthropic SDK with Claude in Foundry)](https://learn.microsoft.com/en-us/Azure/foundry/how-to/develop/sdk-overview)
- [Using Claude in Microsoft Foundry (Claude Help Center)](https://support.claude.com/en/articles/12864745-using-claude-in-microsoft-foundry)
- [Foundry Agent Service at Ignite 2025](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/foundry-agent-service-at-ignite-2025-simple-to-build-powerful-to-deploy-trusted-/4469788)
- [What's new in hosted agents (Build 2026)](https://devblogs.microsoft.com/foundry/hosted-agents-build26/)
- [Multi-agent workflows in Foundry Agent Service](https://devblogs.microsoft.com/foundry/introducing-multi-agent-workflows-in-foundry-agent-service/)
- [Foundry routines](https://learn.microsoft.com/azure/foundry/agents/concepts/routines) · [Routines generally available (Sep 2026)](https://devblogs.microsoft.com/foundry/from-chatbots-to-automated-assistants-routines-in-microsoft-foundry-are-now-generally-available/)
- [Claude models in Microsoft Foundry](https://learn.microsoft.com/en-au/AZURE/foundry/foundry-models/concepts/claude-models) · [Anthropic: Claude in Microsoft Foundry](https://www.anthropic.com/news/claude-in-microsoft-foundry)
- [Claude Code with Microsoft Foundry](https://code.claude.com/docs/en/microsoft-foundry) · [Claude Agent SDK overview](https://code.claude.com/docs/en/agent-sdk/overview)
- [Model router](https://learn.microsoft.com/en-au/AZURE/foundry/openai/concepts/model-router) · [Enforce token limits through the AI gateway](https://learn.microsoft.com/en-us/azure/foundry/control-plane/how-to-enforce-limits-models)
- [Agent identity in Foundry](https://learn.microsoft.com/azure/foundry/agents/concepts/agent-identity) · [Entra Agent ID governance](https://learn.microsoft.com/en-us/entra/id-governance/agent-id-governance-overview)
- [Jobs in Azure Container Apps](https://docs.azure.cn/en-us/container-apps/jobs) (option B)
- [Durable Functions: human approval with external events](https://learn.microsoft.com/en-us/training/modules/orchestrate-durable-functions/4-wait-human-approval-external-events)
- [Azure DevOps remote MCP server GA](https://devblogs.microsoft.com/devops/azure-devops-remote-mcp-server-ga/)
- [ServiceNow MCP Server Console](https://www.servicenow.com/docs/r/zurich/release-notes/mcp-server-console-rn.html)
- [Claude Managed Agents overview](https://tessl.io/blog/with-claude-managed-agents-anthropic-packs-the-infrastructure-to-run-agents-in-production/)

*Product status (preview or generally available) changes often; check each service's current status at implementation.*
