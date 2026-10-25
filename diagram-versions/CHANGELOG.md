# Diagram versions

Each revision is saved as a new pair of files (`agentic-flow-vN.html` + `agentic-flow-vN.png`). Earlier versions are never overwritten.

- **HTML:** open in any browser. Add `?theme=light` or `?theme=dark` to the URL to force a theme.
- **PNG:** a light-theme export for slides, email or Azure DevOps wiki pages.

> **Client names removed (2026-10-25):** every version's title is now just "Agentic Delivery Flow", with the client and project names taken out. All PNGs were re-exported. Nothing else in the diagrams changed.

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
