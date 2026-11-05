# Handoff: Agentic Delivery Flow diagram

**Purpose:** carry this work into a new Claude Code session with nothing lost.
**Written:** 2026-10-25 (session 1), fully rewritten 2026-10-31 at the end of session 2.
**Current diagram version:** **v6** → `diagram-versions/agentic-flow-v6.html` (+ `.png`)
**Created by:** Archana Singh (credit her on every diagram and document; never the signed-in account's name).

> **For the new session (Claude):** read this whole file before replying. Then read `diagram-versions/CHANGELOG.md`, look at `diagram-versions/agentic-flow-v6.png`, and skim `docs/reference-guide.md`. Follow the working preferences in `CLAUDE.md` (private, auto-loaded) strictly. Treat everything here as decided unless it's listed under "Open items" (section 10).

---

## 1. How to work with this user

The user's working preferences, and all **client-specific project details**, are in **`CLAUDE.md`**. It is private and git-ignored, so it isn't on GitHub, and Claude Code loads it automatically in every session in this folder. This file stays generic so it can be committed and shared.

Short version of the preferences (full list in `CLAUDE.md`):

- Senior Sitecore AI developer, new to agentic AI. Explain in simple words. Roles: context sponge (never invent facts), brainstorm partner, teacher (say plainly when something isn't industry standard).
- Questions: simple, brief, options with an example each, one marked **Recommended** with the reason.
- Don't jump ahead; wait for a chunk to be complete; never touch `side-notes.md` unless asked.
- **Before anything with side effects (git, GitHub), show the steps and get a go-ahead.** Commit / push / raise PRs only when asked. **Never merge: the user merges PRs manually.**
- Every diagram revision is a new version (minor → v6.1, v6.2…; structural → v7), saved as HTML + PNG with a CHANGELOG entry.
- **No client or project names in anything committed.**

---

## 2. The project being agentified (context)

*Generic summary. The full client-specific details are in the private `CLAUDE.md`.*

**Repo:** the client's monorepo, on the user's work machine, default branch `develop`.
- The user owns and works across the whole repo.
- This Mac **has no copy of the repo**. All repo knowledge comes from 7 screenshots of its `CLAUDE.md`, kept in `inputs/image-references/claude-md-*.jpg` (git-ignored).

**Platform**
- Sitecore XM Cloud (Sitecore AI) + headless Next.js.
- Sites: a multisite, multi-language corporate site (the `corp` app), plus a second app that is out of scope.
- **Only the `corp` app is in scope; ignore the other app.**

**Monorepo `headapps/`** (Turborepo + pnpm 11.5; npm and yarn are blocked)
- **Apps:** `apps/corp`, plus a second app (out of scope).
- **Packages:**
  - `@client/ui`: atoms and molecules.
  - `@client/sitecore-components`: the CMS components.
  - `@client/themes`: Style Dictionary tokens, Tailwind preset.
  - `@client/utils`: analytics, Zustand store, i18n, cookies, SEO, GTM.

**Backend `authoring/`**
- .NET solution with Sitecore serialized YML items.
- **Rule:** foundational items must be serialized and version-controlled so all environments have the same YMLs.

**Stack:** Node ≥24.16, Next.js 16 App Router, React 19, TypeScript 5.8 strict, Tailwind 4, Content SDK 2.1.0 (`@sitecore-content-sdk/nextjs`), Storybook 8.6.

**Corp routing:** `[site]/[locale]/[[...path]]/page.tsx` (ISR 300s), API routes, NextAuth.

**Component pattern**
- Named export `Component.tsx`; `index.ts` default-exports `withDatasourceCheck()(Component)`.
- `FieldVisibilityGuard` wraps fields; editing mode via `useSitecore().page?.mode?.isEditing`; links are disabled in editing mode.
- Server components by default.
- `data-qa` / `data-testid` attributes.
- Co-located `.test.tsx` + `.stories.tsx`.

**Conventions**
- Imports in 5 groups: React → external → Sitecore SDK → `@client/*` → relative (types last).
- Tailwind semantic tokens only; themes via `data-theme`; RTL via `rtl:`.
- Zustand store; next-intl.

**Testing:** Jest + RTL for unit tests; **Playwright for QA / end-to-end automation tests**. The repo `CLAUDE.md` says coverage thresholds of 80% lines / 70% branches. Every component needs 4 test cases: valid data, missing fields, editing mode, error state.

**Team practice (confirmed by the user):** a front-end developer writes, for each component, the JSX, the business logic that exposes the backend fields, the unit tests and a Storybook story. In the agentic flow the tests and stories come from a separate agent (step 9), but the deliverable per component is the same.

**SonarQube (blocks merges)**
- Complexity ≤15; functions ≤75 lines; files ≤300 lines.
- No `any`, no `!`, no `console.log`, no silent `catch`.
- `UPPER_SNAKE_CASE` constants; TODOs must reference a ticket.

**Git and CI/CD (client repo)**
- Branches: `feature/<TICKET-ID>-…`, `bugfix/…`, `chore/…`, `release/s-XX-main`.
- Main branches: `develop` and `main`, plus regional variants of both.
- Pre-commit: secretlint + lint-staged.
- GitHub Actions run the PR checks. CodeQL runs weekly.
- Deploys: XM Cloud (CM) and Vercel (Next.js).

**Facts the user confirmed**
- **Tracker is Azure DevOps (ADO)**, not Jira. The repo `CLAUDE.md` still says Jira; updating it is parked.
- **One shared Sitecore dev environment** for all developers. No per-developer environments. The user accepts the risks: items are visible to all, which is fine because they're unique per story.
- The **BE Agent creates items directly on dev**, then serializes them (`ser pull`) to YML ready to commit. The Vercel preview reads from dev.
- **Code is hosted on GitHub.** The FE deploys to Vercel (preview URL per PR); the BE deploys to the connected Sitecore AI environment (dev).
- **SonarQube** in GitHub; the user believes it expects 95% coverage. **Agreed target in the flow: 70% for now.**
- **User stories may include a Figma URL**, but not always.
- **Brand info lives in the Sitecore Brand Kit.**
- **Sitecore has two official MCP servers the user knows of: Marketer MCP and Docs MCP.** A Sitecore authentication API exists for automated sign-in (details at implementation).
- `.github/skills/` and `.github/instructions/` exist in the repo but are **excluded from this brainstorm** for now.

---

## 3. Origin of the work

- The user's **product manager** wants the project agentified as much as possible, and asked the user for an **architecture diagram** showing where and how.
- **Source:** a handwritten 4-page flow sketch (`inputs/image-references/flow-page*.jpg`, git-ignored), walked through chunk by chunk and then refined.
- **Reference only, not a template:** the YouTube video "The Agentic Team (and flow)" by Christian Hahn, summarized in `reference-agentic-team-flow-video.md`. **Don't copy that flow.** The summary does **not** mention TDD; its closest idea is the per-task developer–reviewer–tester triad, which became our build triad.

---

## 4. Current flow: the full, decided design (v6 base; v7 adds step 0 Intake, v8 adds the 2A Clarifier)

**Swimlanes (7 categories, by function):** 1 Product & Requirements · 2 Architecture & Planning · 3 Development · 4 Code Quality & Security · 5 Testing & Validation · 6 Release & Deployment · 7 Content Operations.

**Phases:** Plan (steps 1–6) · Build (7–10) · Verify & ship (11–17) · Content (18–21, optional).

**Human gates (👤, 4):** 2B story details (confirm the Clarifier's suggested answers) · 3C prototype pick · 15 PR approval · 21 publish approval.

| # | Step / Agent | Lane | What it does | ADO state | Connectors · skill |
|---|---|---|---|---|---|
| 1 | User story arrives | 1 | Story marked ready in ADO; may include a Figma URL; opens story memory | New | Azure DevOps |
| 2 | PRD Agent | 1 | Checks the story against a **Story readiness checklist**; if details are missing → 2A. Then writes the PRD from the story **+ Figma design if a URL exists** | Drafting Requirements | Azure DevOps, Figma · `write-prd` |
| 2A | Clarifier Agent (v8) | 1 | Takes the open questions, searches the ServiceNow ticket, linked ADO items, Figma and similar past stories, and posts each question with a **suggested answer and its source** ("no source found" instead of guessing). Suggests, never decides. Mid model | Drafting Requirements | ServiceNow, Azure DevOps, Figma · `clarify-story` |
| 2B | 👤 Story owner confirms the answers | 1 | Ticks or corrects each suggested answer (ADO comment, @mention), then **moves the story back to Drafting Requirements** to resume. Only confirmed answers go into the story. **No round cap**; reminder after 2 working days | 👤 Awaiting Story Details | — |
| 3 | PRD Review Agent | 1 | Challenges the PRD, **checks it matches Figma**, sends gaps back to 2 (**max 3 rounds**); if 2 needs a person to fill a gap, it uses 2A → 2B (one door to humans) | Reviewing Requirements | Figma · `review-prd` |
| 3A | Decision: Figma URL in the story? | 1 | Yes → straight to 4 (Figma is the design reference). No → 3B | Reviewing Requirements | Figma |
| 3B | Prototype Agent | 1 | Only if no Figma URL: builds **3 clickable prototype variants from the final, reviewed PRD** | Design | `build-prototypes` |
| 3C | 👤 A person picks one prototype | 1 | The chosen prototype becomes the design reference | 👤 Awaiting Design Pick | — |
| 4 | Spec Agent | 1 | Turns the final PRD into the spec, recording the design reference | Finalizing Requirements | `write-spec` |
| 4A | Blueprint Agent | 2 | Picks the base reference component all development follows. **Runs once, in parallel**, refreshed when the repo changes; not tied to a story | No story state | Sitecore (read), Sitecore Docs MCP · `pick-blueprint` |
| 5 | Task Planner Agent 🤖 | 2 | (Ticketing + Decision agents merged.) Splits the spec into tasks, recording files touched and dependencies; parallel / sequential / mixed. **Dependencies decide, not the task count** | Finalizing Requirements | `plan-tasks` |
| 6 | Contract Agent 🤖 | 2 | **After the Task Planner.** Agrees the base model (template fields, rendering name, props) so **FE and BE work in parallel without FE waiting for BE**. Can send the plan back to 5 (**max 3**) | Finalizing Requirements | Sitecore Docs MCP · `define-contract` |
| 7 | FE Agent ‖ BE Agent (🔁 develop) | 3 | FE: Next.js component. BE: **Sitecore items on dev + YML ready to commit (doesn't commit)** | In Development | FE: Figma, Sitecore Docs MCP, Next.js DevTools MCP, Storybook MCP* · `build-component` · 🪝 auto-format + lint. BE: Sitecore dev, Sitecore Docs MCP · `create-sitecore-items` · 🪝 dev only |
| 8 | Code Quality Agent ‖ Security Agent (🔁 review) | 4 | Local Sonar rules (complexity, size, no `any`) + vulnerability scan, before the PR. One **combined fix list** → back to 7 (**max 3**). Lessons learned → app memory | Quality Checks | `check-code-quality`, `scan-security` |
| 9 | Test & Storybook Agent (🔁 test) | 3 | Unit tests (the 4 required cases) + Storybook stories for the reviewed code; **checks the stories in a browser**; **70% coverage** (moved here from step 8 in v6). **Failing tests → back to 7 (max 3)** | Testing | Playwright MCP, Storybook MCP* · `write-tests-and-stories` · 🪝 run tests on change |
| 10 | Authoring Guide Agent | 3 | Guide for content authors as a **Markdown (.md) file**, written before the PR and shipped in it; the **scripted orchestrator attaches it to the ADO story** (replaced after each fix round). Steps 12, 17 and 18 read it from the story | Preparing Authoring Guide | Sitecore Docs MCP · `write-authoring-guide` |
| 11 | PR Agent | 6 | Commits **code + YML + guide**, raises the GitHub PR; checks the Vercel preview build | Preview Testing | GitHub, Vercel MCP · `raise-pr` · 🪝 pre-commit checks |
| 12 | Test Page Agent | 5 | Builds a test page on dev **by following only the authoring guide**, which also tests the guide | Preview Testing | Marketer MCP (dev) · `build-test-page` |
| 13 | Preview QA Agent ‖ Performance Agent | 5 | On the preview URL. QA checks the acceptance criteria **and the design reference**, then **adds Playwright end-to-end tests to the PR**; Performance checks speed | Preview Testing | QA: Playwright MCP, Vercel MCP · `test-preview`. Perf: Chrome DevTools MCP, Vercel MCP · `check-performance` |
| 14 | Bug Raising Agent (only if issues) | 5 | Logs bugs as **ADO child items of the story**, with Vercel logs → back to 7 (**max 3**) | Preview Testing | Azure DevOps, Vercel MCP (logs) · `raise-bugs` |
| 15 | 👤 You approve the PR | 6 | In GitHub; the flow pauses here. SonarQube + CodeQL stay the final gate | 👤 Awaiting PR Approval | — |
| 16 | Merge & Deploy Agent | 6 | Merges; deploys to Vercel + Sitecore dev; confirms the deploy | Deployment in Progress | GitHub, Vercel MCP, Sitecore dev · `merge-and-deploy` |
| 17 | QA Expert | 5 | Full test on dev against the final spec, using the guide | Under QA Review → Done if no content phase | Playwright MCP, Marketer MCP (read) · `run-full-qa` |
| 18 | Content Authoring Agent (optional) | 7 | Uses the **Sitecore Brand Kit** and the guide from the story; prepares pages (**dev first**, real authoring environment later) | Content Authoring | Marketer MCP · `author-content` |
| 19 | Content Review Agent | 7 | Brand and standards check; feedback to 18 (**max 3**) | Content Authoring | Marketer MCP (read) · `review-content` |
| 20 | Final Page Report | 7 | **A script, not AI.** Preview URL, item path and GUID per page | Content Authoring | Sitecore (query) |
| 21 | 👤 Approve, then publish | 7 | | 👤 Awaiting Publish Approval → Done | — |

\* Storybook MCP needs Storybook 10; the project is on 8.6.

**Rules across the flow**
- **Every agent loop is capped at 3 rounds, then escalates to a human** (ADO state 👤 **Needs Human**). The only uncapped loop is 2A → 2B, where a person is answering.
- Fix rounds return the story to In Development.
- An agent never reviews or tests its own work.
- **Build triad per task (v6):** develop (7) → review (8) → test (9), run by the orchestrator for each task in the Task Planner's order.
- **Spec-driven development (SDD):** steps 2–6 shape the spec, 7 builds from it, and 13 and 17 check against it. Marked 📐 SDD on the diagram; the triad is marked 🔁.
- **Done per component:** code + business logic + unit tests + Storybook story, kept together.
- Agents hand work over through **story memory**.
- SonarQube + CodeQL in GitHub remain the final gate on the PR.
- The design reference (Figma or the chosen prototype) is the single source of truth for how the result looks.
- No agent merges or publishes without a person's approval.

**ADO states: 19, one per stage**
- In order: New → Drafting Requirements → 👤 Awaiting Story Details* → Reviewing Requirements → Design* → 👤 Awaiting Design Pick* → Finalizing Requirements → In Development → Quality Checks → Testing → Preparing Authoring Guide → Preview Testing → 👤 Awaiting PR Approval → Deployment in Progress → Under QA Review → Content Authoring* → 👤 Awaiting Publish Approval* → Done. Plus 👤 Needs Human at any time. *Only when that step is used.
- **The scripted orchestrator sets the state** (no AI cost). Per-agent detail goes in as a **comment on the story**.
- The state is for **people**. Agents coordinate through the orchestrator and story memory, not the state, to avoid race conditions.
- These are custom states: an ADO admin must add them to the work item process before implementation.

---

## 5. Agentic concepts layer (decided)

- **Subagents:** every agent box is a subagent with its own context and tools that hands back only its result (context isolation; covers the user's pointer "all agents run in separate sessions").
- **Orchestration: scripted by default.** Fixed order, starts parallel steps together, counts loops (max 3), pauses at gates, sets ADO states, attaches the authoring guide to the story, runs the triad per task.
  - **Runs phase by phase, triggered by events:** story marked ready → Plan + Build; PR approved → Release; publish approved → Content/publish.
  - **AI orchestration only where judgement is needed (🤖):** the Task Planner deciding the order (5), and the Contract Agent sending the plan back (6).
  - **Rule of thumb (taught):** AI for decisions that need judgement; scripts for repeatable mechanics (cheaper, predictable, auditable). Example: the scripted orchestrator, not AI, attaches the guide to the ADO story.
- **Memory**
  - **App memory:** shared, in the repo. **`AGENTS.md`** (conventions + shared rules that skills point to: Sonar, security, Sitecore template and serialization rules, component and test patterns, design tokens, brand voice and standards), the blueprint, lessons learned.
  - **Story memory:** one folder per story. PRD, design reference, spec, task list, contract, fix and bug lists, loop counts.
  - **Developer memory:** **`CLAUDE.md`**, personal and **git-ignored**; not used by the automated flow.
  - **Teacher note given:** check that Claude Code reads `AGENTS.md`. If it doesn't, each personal `CLAUDE.md` needs `@AGENTS.md`. The current repo `CLAUDE.md` content would move to `AGENTS.md` (on hold).
- **AI harness:** Claude Code (interactive) + Claude Agent SDK / Claude Code in CI (automated). Don't build a custom harness.
- **Plugin:** the whole flow is packaged as one team plugin (side indicator only). Plugin skills are auto-prefixed with the plugin name.
- **Skills (📘): one per agent** (user's choice, so people can build them independently). Names: kebab-case, verb-object — `write-prd`, `review-prd`, `build-prototypes`, `write-spec`, `pick-blueprint`, `plan-tasks`, `define-contract`, `build-component`, `create-sitecore-items`, `check-code-quality`, `scan-security`, `write-tests-and-stories`, `write-authoring-guide`, `raise-pr`, `build-test-page`, `test-preview`, `check-performance`, `raise-bugs`, `merge-and-deploy`, `run-full-qa`, `author-content`, `review-content` (22). Step 20 is a script, so no skill. Agent-specific templates and checklists live inside the skill; shared rules live in `AGENTS.md`.
- **Hooks (🪝):** auto-format + lint (FE), dev-only lock (BE), run tests on change (Test & Storybook), pre-commit checks (PR Agent: existing secretlint and lint-staged). Names are **suggestions**, not existing repo files.
- **Connectors (🔌 via MCP), 11 in total:**
  - Azure DevOps (1, 2, 14) · GitHub (11, 16) · Figma (2, 3, 3A, 7) · Sitecore dev / CLI (4A, 7, 16, 20).
  - **Sitecore Marketer MCP** (official): 12 (dev), 17 (read), 18, 19 (read). Pages, content, components, Brand Kit. **Can't create templates or renderings**, so the BE Agent keeps the Sitecore CLI.
  - **Sitecore Docs MCP** (official): 4A, 6, 7 FE + BE, 10. Its address couldn't be verified (an old blog URL `doc.sitecore.com/mcp` returned 404); confirm at implementation.
  - **Vercel MCP** (official): 11, 13 (both agents), 14 (logs), 16. **Not on the FE Agent** — nothing is deployed while it codes.
  - **Next.js DevTools MCP** (official, built into Next.js 16): 7 FE.
  - **Playwright MCP** (official, Microsoft): 9, 13 QA, 17. Replaced the generic "Browser" connector.
  - **Storybook MCP** (official, preview): 7 FE (find existing components to reuse) and 9 (write/test stories). **Needs Storybook 10**.
  - **Chrome DevTools MCP** (official, Google): 13 Performance.
  - A **community Sitecore MCP** (open source, not supported by Sitecore) was considered and **not added**.
- **Development approach:** SDD + build triad. **Deliberately not called TDD** (TDD = tests written before the code). The user first asked for TDD, then chose develop → review → test after the teacher note; a "Test-First Agent 6A" design was proposed and **not** adopted.
- **Later (in the diagram footer, not active):** worktrees for parallel agents, model choice per agent, observability, evals, Sitecore and Vercel MCP setup details, Storybook 8.6 → 10 upgrade.
- **On hold:** `.claudeignore`-style exclusions, and writing the shared `AGENTS.md`.
  - **Teacher note given:** `.claudeignore` isn't an official Claude Code feature; the equivalent is deny rules in `.claude/settings.json`.

---

## 6. Version history in brief (details in `CHANGELOG.md`)

| Version | What changed |
|---|---|
| v1–v1.2 | First flow (21 steps, 7 lanes, 4 phases); loop caps; floating lane headers |
| v2–v2.1 | Agentic concepts layer (orchestration, memory, skills, hooks, connectors, subagents) |
| v3–v3.1 | Design step 3A–3C (Figma or 3 prototypes); Figma used by the PRD and review agents |
| v4–v4.1 | Azure DevOps state layer (one state per stage) |
| v4.2 | Sitecore Marketer MCP + Docs MCP added |
| v4.3 | "Later" note on MCP implementation details |
| v4.4 | Drafting / Reviewing Requirements states; guide as .md attached to the story |
| v5 | Step 2A (missing story details); Playwright, Storybook, Next.js DevTools, Chrome DevTools, Vercel MCPs |
| v6 | Build triad (review 8 before test 9), "Testing" state, SDD + triad markers, one skill name per agent, "Created by Archana Singh" |
| v7 | Step 0 Intake Agent (ServiceNow + ADO); model size per agent; service identities; runs on Azure |
| v8 | 2A Clarifier Agent drafts answers with sources; the human gate moves to 2B (owner confirms) |

Client and project names were removed from every version's title (all PNGs re-exported). There is also an **uncommitted** note in `CHANGELOG.md` saying v4.2's files were overwritten by mistake and restored; the user said they'll handle it (see open items).

---

## 7. Documents

- **`docs/reference-guide.md`** (in the repo): the shareable, self-explanatory reference guide to v6, with `docs/images/flow-at-a-glance.png`. Sections: about, why, flow at a glance, how to read the diagram, 8 agentic concepts, SDD + triad, phases 1–4 as tables, 19 ADO states, 11 connectors, 22 skill names, 8 guardrails, not-yet-in-scope + open items, proposed pilot, FAQ, glossary.
- **The same guide as a Claude doc** ("Agentic Delivery Flow: Reference Guide") is the editable original; its link is in the private `CLAUDE.md`. It started as a presenter script and was converted to a reference guide at the user's request (the presenter checklist was removed). One comment there, asking the user to confirm the pilot assumption, is still unanswered.
- **Proposed pilot (not agreed):** one simple component story, steps 1–10, a developer watching each step. Needs: implementation time, ADO admin for states, Marketer + Vercel MCP access, Storybook upgrade decision. Measures: time ready → PR, loop rounds per step, how often 2A is needed, defects at preview vs after merge.

---

## 8. Git and GitHub workflow for this folder

- **Repo:** `archanasingh1605/agentic-sitecoreai` (**private**). This folder is the working copy.
- **Branching:** `feature/*` → PR into `develop` → release PR `develop` → `main`. GitHub shows `main` by default, so work only appears on the front page after a release PR. **The user merges every PR manually; never merge.**
- **Commit identity (repo-local):** the user's GitHub username with their GitHub **noreply** email.
- **Commit messages** end with the Claude co-author line; PR bodies end with the Claude Code line.
- **Before every commit:** check no `.env`, `CLAUDE.md`, `inputs/` or `.DS_Store` is staged; scan for token-like strings; grep committed text for client terms.
- **Pushing:** there's no `gh` CLI on this Mac. Push with a one-off credential helper that reads `GITHUB_PAT` from `.env` at run time (never printed, never stored in git config or the keychain). `http.postBuffer` is raised for this repo because the PNGs are large. Repos and PRs are created through the GitHub REST API with `curl`.
- **GitHub connector for Claude:** a local-scope `github` MCP server for this folder reads the PAT from `.env` (details in `CLAUDE.md`).
- **PR history:**

| PR | Branch → base | Content | State |
|---|---|---|---|
| #1 | develop → main | Diagrams v1–v4.3, changelog, handoff | Merged |
| #2 | develop → main | v4.4 | Closed unmerged (replaced by #3 after the feature-branch workflow was adopted; develop was force-pushed back to main with `--force-with-lease`) |
| #3 | feature/diagram-v4.4 → develop | v4.4 | Merged |
| #4 | feature/diagram-v5 → develop | v5 | Merged |
| #5 | feature/diagram-v6 → develop | v6 | Merged |
| #6 | feature/reference-guide → develop | Reference guide | Merged |
| #7 | develop → main | Release of v4.4–v6 + guide | Merged |

- **State at handoff:** `main` and `develop` have the same content (main has one extra merge commit). The merged feature branches still exist on GitHub and can be deleted when the user wants. Local checkout is on `feature/reference-guide` with only the uncommitted CHANGELOG note; for new work, branch from `origin/develop`.

---

## 9. Files in this folder

| Path | What it is |
|---|---|
| `HANDOFF.md` | This file (generic, committed) |
| `CLAUDE.md` | **Private, git-ignored.** Working preferences + client-specific project details + private session notes |
| `docs/reference-guide.md` | Shareable reference guide to v6, with `docs/images/flow-at-a-glance.png` |
| `diagram-versions/agentic-flow-v6.html` / `.png` | **Current diagram** |
| `diagram-versions/agentic-flow-v1…v5.html/.png` | All earlier versions (v1, v1.1, v1.2, v2, v2.1, v3, v3.1, v4, v4.1, v4.2, v4.3, v4.4, v5). Never overwritten, except the one-off client-name removal (and the v4.2 restore) |
| `diagram-versions/CHANGELOG.md` | What changed in every version |
| `side-notes.md` | The user's parked side notes (see section 10) |
| `reference-agentic-team-flow-video.md` | Paraphrased summary of the YouTube reference video (reference only) |
| `.gitignore` | Keeps `CLAUDE.md`, `CLAUDE.local.md`, `inputs/`, `.env`, `.env.*` and `.DS_Store` out of git |
| `.env` | **Private, git-ignored.** Holds `GITHUB_PAT` |
| `inputs/image-references/claude-md-1…7_*.jpg` | Screenshots of the client repo's `CLAUDE.md` (git-ignored) |
| `inputs/image-references/flow-page1…4_*.jpg` | The original handwritten flow sketch (git-ignored) |

**HTML diagram internals (v6)**
- A single self-contained file with a CSS grid. Column 1 is the phase rail; columns 2–8 are lanes 1–7.
- Header: title "Agentic Delivery Flow", badges (version, "Created by Archana Singh", draft, subagent note), plugin aside; panels: Orchestration, Memory, Concept key; then the "Development approach" strip (SDD + triad panels); legend; toggles.
- Node ids and grid rows (v6): n1=2, n2=3, n2a=4, n3=5, n3a=6, n3b=7, n3c=8, n4=9, n4a=3/6 (lane 2), n5=10, n6=11, n7=12, **n8=13 (lane 4, review)**, **n9=14 (lane 3, test)**, n10=15, n11=16, n12=17, n13=18, n14=19, n15=20, n16=21, n17=22, n18=23, n19=24, n20=25, n21=26. Lane backgrounds span `1 / 27`. Phase rail: Plan 2/12, Build 12/16, Verify & ship 16/23, Content 23/27.
- **Arrows** are drawn by JS from the `EDGES` array. Route types: `down` (default), `hv`, `loopRight`, `loopLeft`, `gutter`. `loopLeft`/`loopRight` accept `off` (distance from the boxes) and `sy`/`ty` (vertical offsets at source/target) so parallel loops don't overlap.
- **Each node** carries a `.state` badge (ADO), `.concepts` tags (`.c.conn`/`.skill`/`.hook`/`.mem`/`.ai`) and `.chip` tags (incl. `.chip.sdd`, `.chip.triad`). Footer boxes: rules, ADO states, connectors, skill names, later.
- **Toggles:** "Show agentic concepts" (also hides the approach strip and SDD/triad chips) and "Show ADO states". URL params: `?theme=light|dark`, `?concepts=off`, `?states=off`.
- **Editing approach:** copy the latest version to a new file with a small Python script that does exact string replacements (each asserted to match once), then re-export and visually check slices.

**PNG export** (macOS, headless Chrome)

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --disable-gpu --hide-scrollbars --force-device-scale-factor=2 --window-size=1600,<PAGE_HEIGHT> --virtual-time-budget=3000 --screenshot="diagram-versions/agentic-flow-vX.png" "file://$PWD/diagram-versions/agentic-flow-vX.html?theme=light"
```

- **Page height:** v6 is 8251px.
- **How to measure:** copy the HTML to the scratchpad with a script before `</body>` that writes `documentElement.scrollHeight` into a `data-h` attribute on `<body>`, then run headless Chrome with `--dump-dom` and read `data-h`. Wrap Chrome calls in `perl -e 'alarm 90; exec @ARGV' …` because macOS has no `timeout` command and Chrome sometimes hangs. (The older iframe wrapper hangs; don't use it.)
- **Visual check:** render slices of the PNG through a small HTML page with a negative `margin-top`, screenshot them, and read the images (no PIL or ImageMagick on this Mac).

---

## 10. Open items and things to remember

**`side-notes.md`: left as is, by the user's choice** (only a ticket-ID placeholder was changed, at their request). It's partly out of date:
- Its section 1 topics (orchestration, memory, harness, plugins, subagents, other concepts) were **covered in v2**.
- Its chunk 1 entries still say "Decision Agent kept separate". **That was later merged into the Task Planner Agent.**
- It has two items Claude added without being asked (shared blueprint vs per-story pick; Jira → ADO in `CLAUDE.md`).
- **Don't edit it unless the user explicitly asks.**

**Still open or unconfirmed**
1. **"Pending notes section"** in `side-notes.md`: the user hasn't confirmed which section they meant. Possibly the page-4 "pointers". Ask, don't assume.
2. Does each story also need its own base-component pick, or does the one shared blueprint cover every story? *(parked)*
3. Update the client repo's `CLAUDE.md` from Jira to Azure DevOps *(parked)*; ties into the AGENTS.md / CLAUDE.md split.
4. **Coverage:** 70% agreed for now. The real target (SonarQube 95%? Jest 80/70?) is still to confirm.
5. **Serialization tooling details** *(parked)*.
6. Writing `AGENTS.md` and `.claudeignore`-style exclusions: **on hold**.
7. **Sitecore and Vercel MCP setup, at implementation:** sign-in for automated runs (Sitecore authentication API exists; Vercel MCP uses a browser sign-in), pointing the Marketer MCP at dev, and the Docs MCP address.
8. **Storybook upgrade 8.6 → 10** before the Storybook MCP can be used.
9. **Playwright details to confirm:** where the existing Playwright tests live and run (repo folder, CI job).
10. **ADO admin** to add the 19 custom states.
11. **Pilot scope** (section 7) is a proposal; the user hasn't confirmed it (unanswered comment in the Claude doc).
12. **Uncommitted `CHANGELOG.md` note** about restoring v4.2: the user said "leave it, I'll handle it". Don't commit it unless asked.
13. **This `HANDOFF.md` rewrite** is not committed yet (needs a feature branch + PR into develop when the user asks).
14. Optional housekeeping: delete the merged feature branches on GitHub; remove the extra `admin:org` scope from the GitHub token (least privilege).

**Resolved; don't re-ask**
- **Names:** PRD Review Agent; Task Planner Agent (merged Ticketing + Decision); Test & Storybook Agent; Test Page Agent; Merge & Deploy Agent; Final Page Report (script).
- **Ordering:** Blueprint runs once, in parallel · Contract after the Task Planner, with a feedback arrow · review (8) before test (9), both locally before the PR, with the CI gate after · authoring guide before the PR · Performance runs before the merge, alongside Preview QA.
- **Gates:** 2B story details (confirms the 2A Clarifier's suggestions), 3C prototype pick, 15 PR approval (an agent then merges), 21 publish.
- **Requirements states:** Drafting and Reviewing are separate (v4.4).
- **Authoring guide:** Markdown, attached to the story by the scripted orchestrator, replaced after fix rounds; read by 12, 17, 18.
- **E2E tests:** written by the Preview QA Agent at step 13 (not test-first).
- **2A → 2B loop:** no cap, reminder after 2 working days, resumes when the owner changes the state back.
- **Who answers the PRD questions (v8):** an agent drafts suggested answers with sources (2A Clarifier), a person confirms (2B). Not an agent on its own (it would guess requirements), and not inside the Intake Agent (it runs before the questions exist).
- **Content:** dev first; Brand Kit; Final Page Report is a script.
- **ADO:** states per stage, set by the orchestrator. Bugs are ADO child items + a report to the agents.
- **Original photos:** IMG_8881 / 8882 and the page-3 numbering were parked as irrelevant.
- **Repo setup:** private repo, split of private `CLAUDE.md` vs generic `HANDOFF.md`, feature → develop → main workflow.

**Teaching already given** (don't repeat unless asked)
- PRD / ADR / quality gate / fresh-context reviewer / idempotent / worktree.
- Contract-first development; static vs dynamic testing; DevSecOps; shift left.
- Plan-and-execute ("planner"); handoff contract; race conditions.
- `ser pull` vs `ser push` vs `git push`.
- Hooks vs instructions; connectors = MCP ("USB for AI tools"); official vs community MCP servers.
- Not every step needs an agent (scripts for deterministic work); why the scripted orchestrator attaches the guide.
- Content vs code environments.
- Definition of Ready; "human in the loop"; evals and observability.
- SDD vs TDD vs the develop–review–test triad; ATDD; why a separate test agent (agents testing their own code confirm their own mistakes).
- Why Vercel MCP fits deploy steps and Next.js DevTools MCP fits the FE Agent.
- Git: feature → develop → main; release PRs; why a closed PR can't take new commits; force push with lease; least-privilege tokens.

---

## 11. How to start the new session

1. Open a new Claude Code session in this folder (the `agentic-sitecoreai` repo). `CLAUDE.md` loads automatically, and the local GitHub connector starts.
2. Optionally run `/idea-session` first.
3. Paste this prompt:

> Read `HANDOFF.md` in this folder in full, then `diagram-versions/CHANGELOG.md`, look at `diagram-versions/agentic-flow-v6.png`, and skim `docs/reference-guide.md`. We're continuing the agentic architecture work for my PM. The current diagram is v6. Follow the working preferences in `CLAUDE.md`. Confirm in a few lines that you have the context, list the open items, and then wait for my next instruction.
