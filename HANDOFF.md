# Handoff: Agentic Delivery Flow diagram

**Purpose:** carry this work into a new Claude Code session with nothing lost.
**Written:** 2026-10-25, updated at the end of the second session.
**Current diagram version:** **v4.4** → `diagram-versions/agentic-flow-v4.4.html` (+ `.png`)

> **For the new session (Claude):** read this whole file before replying. Then read `diagram-versions/CHANGELOG.md` and open `diagram-versions/agentic-flow-v4.4.html`. Follow the working preferences in `CLAUDE.md` strictly. Treat everything here as decided unless it's listed under "Open items".

---

## 1. How to work with this user

The user's working preferences, and all **client-specific project details**, are in **`CLAUDE.md`**. It is private and git-ignored, so it isn't on GitHub, and Claude Code loads it automatically in every session in this folder. This file stays generic so it can be committed and shared.

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

**Testing:** Jest + RTL. The repo `CLAUDE.md` says coverage thresholds of 80% lines / 70% branches. Every component needs 4 test cases: valid data, missing fields, editing mode, error state.

**SonarQube (blocks merges)**
- Complexity ≤15; functions ≤75 lines; files ≤300 lines.
- No `any`, no `!`, no `console.log`, no silent `catch`.
- `UPPER_SNAKE_CASE` constants; TODOs must reference a ticket.

**Git and CI/CD**
- Branches: `feature/<TICKET-ID>-…`, `bugfix/…`, `chore/…`, `release/s-XX-main`.
- Main branches: `develop` and `main`, plus regional variants of both.
- Pre-commit: secretlint + lint-staged.
- GitHub Actions run the PR checks. CodeQL runs weekly.
- Deploys: XM Cloud (CM) and Vercel (Next.js).

**Facts the user confirmed in session**
- **Tracker is Azure DevOps (ADO)**, not Jira. The repo `CLAUDE.md` still says Jira; updating it is parked.
- **One shared Sitecore dev environment** for all developers. There are no per-developer environments. The user accepts the risks: items are visible to all, which is fine because they're unique per story.
- The **BE Agent creates items directly on dev**, then serializes them (`ser pull`) to YML ready to commit. The Vercel preview reads from dev.
- **Code is hosted on GitHub.** The FE deploys to Vercel (preview URL per PR); the BE deploys to the connected Sitecore AI environment (dev).
- **SonarQube** in GitHub; the user believes it expects 95% coverage. **Agreed target in the flow: 70% for now.**
- **User stories may include a Figma URL**, but not always.
- **Brand info lives in the Sitecore Brand Kit.**
- `.github/skills/` and `.github/instructions/` exist in the repo but are **excluded from this brainstorm** for now.

---

## 3. Origin of the work

- The user's **product manager** wants the project agentified as much as possible, and asked the user for an **architecture diagram** showing where and how.
- **Source:** a handwritten 4-page flow sketch (`inputs/image-references/flow-page*.jpg`, git-ignored), walked through chunk by chunk and then refined.
- **Reference only, not a template:** the YouTube video "The Agentic Team (and flow)" by Christian Hahn, summarized in `reference-agentic-team-flow-video.md`. **Don't copy that flow.** Keep everything specific to this project.

---

## 4. Current flow (v4.4): the full, decided design

**Swimlanes (7 categories, by function):**
1. Product & Requirements
2. Architecture & Planning
3. Development
4. Code Quality & Security
5. Testing & Validation
6. Release & Deployment
7. Content Operations

Human gates are drawn as 👤 markers.

**Phases:** Plan (steps 1–6) · Build (7–10) · Verify & ship (11–17) · Content (18–21, optional).

| # | Step / Agent | Lane | What it does | ADO state |
|---|---|---|---|---|
| 1 | User story arrives | 1 | Story marked ready in ADO; may include a Figma URL | New |
| 2 | PRD Agent | 1 | Writes the PRD from the story **+ Figma design if a URL exists** | Drafting Requirements |
| 3 | PRD Review Agent | 1 | Challenges the PRD, **checks it matches Figma**, sends gaps back to 2 (**max 3 rounds**) | Reviewing Requirements |
| 3A | Decision: Figma URL in the story? | 1 | Yes → straight to 4 (Figma is the design reference). No → 3B | Reviewing Requirements |
| 3B | Prototype Agent | 1 | Only if no Figma URL: builds **3 clickable prototype variants from the final, reviewed PRD** | Design |
| 3C | 👤 Human gate: a person picks one prototype | 1 | The chosen prototype becomes the design reference | 👤 Awaiting Design Pick |
| 4 | Spec Agent | 1 | Turns the final PRD into the spec, recording the design reference | Finalizing Requirements |
| 4A | Blueprint Agent | 2 | Picks the base reference component all development follows. **Runs once, in parallel**, refreshed when the repo changes; not tied to a story | No story state |
| 5 | Task Planner Agent | 2 | (Ticketing + Decision agents merged.) Splits the spec into tasks, recording files touched and dependencies; plans parallel / sequential / mixed. **Dependencies decide, not the task count** | Finalizing Requirements |
| 6 | Contract Agent | 2 | **After the Task Planner.** Agrees the base model (template fields, rendering name, component props). Can send the plan back to 5 if it finds a new dependency (**max 3**) | Finalizing Requirements |
| 7 | FE Agent ‖ BE Agent (parallel) | 3 | FE: Next.js component. BE: **Sitecore items on dev + YML ready to commit (doesn't commit)** | In Development |
| 8 | Test & Storybook Agent | 3 | Unit tests + Storybook stories (separate from the FE Agent; an agent never tests its own work) | In Development |
| 9 | Code Quality Agent ‖ Security Agent (parallel) | 4 | **Local checks before the PR** (Sonar rules, 70% coverage, vulnerability scan). One **combined fix list** → back to 7 (**max 3**) | Quality Checks |
| 10 | Authoring Guide Agent | 3 | Guide for content authors, as a **Markdown (.md) file**. **Written before the PR** and shipped in it; the orchestrator also **attaches it to the ADO story** (replaced after each fix round). Steps 12, 17 and 18 read it from the story | Preparing Authoring Guide |
| 11 | PR Agent | 6 | Commits **code + YML + guide**, raises the GitHub PR; Vercel builds a preview | Preview Testing |
| 12 | Test Page Agent | 5 | Builds a test page on dev **by following the authoring guide**, which also tests the guide | Preview Testing |
| 13 | Preview QA Agent ‖ Performance Agent (parallel) | 5 | On the preview URL. QA checks the PRD's acceptance criteria **and the design reference**; Performance checks speed | Preview Testing |
| 14 | Bug Raising Agent (only if issues) | 5 | Logs bugs as **ADO child items of the story** + reports to the FE/BE agent → back to 7 (**max 3**) | Preview Testing |
| 15 | 👤 Human gate: you approve the PR | 6 | In GitHub; the flow pauses here | 👤 Awaiting PR Approval |
| 16 | Merge & Deploy Agent | 6 | Merges; deploys to Vercel + Sitecore dev | Deployment in Progress |
| 17 | QA Expert | 5 | Full test on dev against the final spec, using the guide | Under QA Review → Done if no content phase |
| 18 | Content Authoring Agent (optional) | 7 | Uses the **Sitecore Brand Kit**; prepares pages (**dev first**, real authoring environment later) | Content Authoring |
| 19 | Content Review Agent | 7 | Brand and standards check; feedback to 18 (**max 3**) | Content Authoring |
| 20 | Final Page Report | 7 | **A script, not AI.** Preview URL, item path and GUID per page | Content Authoring |
| 21 | 👤 Human gate: approve, then publish | 7 | | 👤 Awaiting Publish Approval → Done |

**Rules across the flow**
- **Every loop is capped at 3 rounds, then escalates to a human.** ADO state becomes 👤 **Needs Human**.
- Fix rounds return the story to In Development.
- An agent never reviews its own work.
- Agents hand work over through **story memory**.
- SonarQube + CodeQL in GitHub remain the final gate on the PR.
- The design reference (Figma or the chosen prototype) is the single source of truth for how the result looks.

**ADO states: 17, one per stage**
- In order: New → Drafting Requirements → Reviewing Requirements → Design* → 👤 Awaiting Design Pick* → Finalizing Requirements → In Development → Quality Checks → Preparing Authoring Guide → Preview Testing → 👤 Awaiting PR Approval → Deployment in Progress → Under QA Review → Content Authoring* → 👤 Awaiting Publish Approval* → Done.
- Plus 👤 Needs Human at any time. *Only when that step is used.
- **The scripted orchestrator sets the state** (no AI cost). Per-agent detail goes in as a **comment on the story**.
- The state is for **people**. Agents coordinate through the orchestrator and story memory, not the state, to avoid race conditions.

---

## 5. Agentic concepts layer (decided, shown in v2 onwards)

- **Subagents:** every agent box is a subagent with its own context and tools that hands back only its result. This covers the user's pointer "all agents run in separate sessions" (context isolation).
- **Orchestration: scripted by default.**
  - Runs in a fixed order, starts parallel steps together, counts loops (max 3) and pauses at gates.
  - **Runs phase by phase, triggered by events:** story marked ready in ADO → Plan + Build; PR approved → Release; publish approved → Publish.
  - **AI orchestration only where judgment is needed (🤖):** the Task Planner deciding the order, and the Contract Agent sending the plan back.
- **Memory**
  - **App memory:** shared, in the repo. **`AGENTS.md`** (project conventions), the blueprint, lessons learned.
  - **Story memory:** one folder per story. PRD, design reference, spec, task list, contract, fix and bug lists, loop counts.
  - **Developer memory:** **`CLAUDE.md`**, personal and **git-ignored**; not used by the automated flow.
  - **Teacher note given:** check that Claude Code reads `AGENTS.md`. If it doesn't, each personal `CLAUDE.md` needs `@AGENTS.md`. The current repo `CLAUDE.md` content would move to `AGENTS.md` (on hold).
- **AI harness:** Claude Code (interactive) + Claude Agent SDK / Claude Code in CI (automated). Don't build a custom harness.
- **Plugin:** the whole flow is packaged as one team plugin. Shown only as a side indicator.
- **Skills (📘), hooks (🪝) and connectors (🔌 via MCP)** are tagged on each step.
  - **Connectors:** Azure DevOps, GitHub, Sitecore dev, Vercel, Figma, Browser, plus (from v4.2) Sitecore's two official MCP servers:
    - **Sitecore Docs MCP** (developer docs) on 4A Blueprint, 6 Contract, 7 FE + BE, 10 Authoring Guide.
    - **Marketer MCP** (pages, content, components, Brand Kit) on 12 Test Page, 17 QA Expert (read), 18 Content Authoring, 19 Content Review (read). It can't create templates or renderings, so the BE Agent keeps the Sitecore CLI.
  - **Hooks:** auto-format + lint (FE), dev-only safety lock (BE), run tests on change (Test & Storybook), pre-commit checks (PR Agent: existing secretlint and lint-staged).
  - The skill and hook names are **suggestions**, not existing repo files.
- **Later (in the footer, not active):** worktrees for parallel agents, model choice per agent, observability, evals, and the Sitecore MCP setup details (v4.3).
- **On hold:** `.claudeignore`-style exclusions, and writing the shared `AGENTS.md`.
  - **Teacher note given:** `.claudeignore` isn't an official Claude Code feature; the equivalent is deny rules in `.claude/settings.json`.

---

## 6. Files in this folder

| Path | What it is |
|---|---|
| `HANDOFF.md` | This file (generic, committed) |
| `CLAUDE.md` | **Private, git-ignored.** Working preferences + client-specific project details |
| `diagram-versions/agentic-flow-v4.4.html` / `.png` | **Current diagram** |
| `diagram-versions/agentic-flow-v1…v4.3.html/.png` | All earlier versions (v1, v1.1, v1.2, v2, v2.1, v3, v3.1, v4, v4.1, v4.2, v4.3). Never overwritten, except the one-off removal of client names from every title |
| `diagram-versions/CHANGELOG.md` | What changed in every version |
| `side-notes.md` | The user's parked side notes (see section 7) |
| `reference-agentic-team-flow-video.md` | Paraphrased summary of the YouTube reference video (reference only) |
| `.gitignore` | Keeps `CLAUDE.md`, `inputs/` (client input documents), `.env` (secrets) and `.DS_Store` out of git |
| `.env` | **Private, git-ignored.** Holds `GITHUB_PAT` |
| `inputs/image-references/claude-md-1…7_*.jpg` | Screenshots of the repo's `CLAUDE.md` (line ranges in the names) |
| `inputs/image-references/flow-page1…4_*.jpg` | The original handwritten flow sketch |

**HTML diagram internals** (for editing)
- A single self-contained file with a CSS grid. Column 1 is the phase rail; columns 2–8 are lanes 1–7.
- Node ids are `n1, n2, n3, n3a, n3b, n3c, n4, n4a, n5 … n21`, each placed with `grid-column` / `grid-row` inline styles.
- Grid rows: n1=2, n2=3, n3=4, n3a=5, n3b=6, n3c=7, n4=8, n4a=3/5 (lane 2), n5=9, n6=10, n7=11, n8=12, n9=13, n10=14, n11=15, n12=16, n13=17, n14=18, n15=19, n16=20, n17=21, n18=22, n19=23, n20=24, n21=25. Lane backgrounds span `1 / 26`.
- **Arrows** are drawn by JS from the `EDGES` array. Route types: `down` (default), `hv`, `loopRight`, `loopLeft`, `gutter`.
- **Each node** carries a `.state` badge (ADO), `.concepts` tags (`.c.conn`/`.skill`/`.hook`/`.mem`/`.ai`) and `.chip` tags.
- **Toggles:** "Show agentic concepts" and "Show ADO states".
- **URL params:** `?theme=light|dark`, `?concepts=off`, `?states=off`.
- **Floating category headers:** a fixed bar appears when you scroll down (from v1.2).
- **Editing approach used so far:** copy the latest version to a new file with a small Python script that does exact string replacements (each asserted to match once), then re-export.

**PNG export** (macOS, headless Chrome)

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --disable-gpu --hide-scrollbars --force-device-scale-factor=2 --window-size=1600,<PAGE_HEIGHT> --virtual-time-budget=3000 --screenshot="diagram-versions/agentic-flow-vX.png" "file://$PWD/diagram-versions/agentic-flow-vX.html?theme=light"
```

- **Page height:** v4.4 is 6604px. Measuring is slow; reuse the previous PNG's height (pixels ÷ 2) when the layout height hasn't changed.
- **How to measure:** copy the HTML to the scratchpad with a script before `</body>` that writes `documentElement.scrollHeight` into a `data-h` attribute on `<body>`, then run headless Chrome with `--dump-dom` and read `data-h`. (The older iframe wrapper started hanging.)

---

## 7. Open items and things to remember

**`side-notes.md`: left as is, by the user's choice** (only a ticket-ID placeholder was changed, at their request). It's now partly out of date:
- Its section 1 topics (orchestration, memory, harness, plugins, subagents, other concepts) were **covered in v2**.
- Its chunk 1 entries still say "Decision Agent kept separate". **That was later merged into the Task Planner Agent.**
- It has two items Claude added without being asked (shared blueprint vs per-story pick; Jira → ADO in `CLAUDE.md`).
- **Don't edit it unless the user explicitly asks.**

**Still open or unconfirmed**
1. **"Pending notes section"** in `side-notes.md`: the user hasn't confirmed which section they meant. Possibly the page-4 "pointers" (separate sessions, `.claudeignore`, `CLAUDE.md`). Ask, don't assume.
2. Does each story also need its own base-component pick, or does the one shared blueprint cover every story? *(parked)*
3. Update the repo `CLAUDE.md` from Jira to Azure DevOps *(parked)*. This now ties into the AGENTS.md / CLAUDE.md split.
4. **Coverage:** 70% agreed for now. The real target (SonarQube 95%? Jest 80/70?) is still to confirm.
5. **Serialization tooling details** *(parked)*.
6. Writing `AGENTS.md` and `.claudeignore`-style exclusions: **on hold**.
7. Possibly publishing the diagram as a shareable artifact link for the PM. This was offered; the user should check client-confidentiality first.
8. **Sitecore MCP setup, at implementation:** sign-in for automated runs (a Sitecore authentication API exists), pointing the Marketer MCP at dev, and the Docs MCP address.
9. A community Sitecore MCP (open source, not supported by Sitecore) was offered for the BE Agent and **not added**. The BE Agent keeps the Sitecore CLI.

**Resolved; don't re-ask**
- **Names:** PRD Review Agent; Task Planner Agent (merged Ticketing + Decision); Test & Storybook Agent; Test Page Agent; Merge & Deploy Agent; Final Page Report (script).
- **Ordering:**
  - Blueprint runs once, in parallel.
  - Contract after the Task Planner, with a feedback arrow.
  - 8/9 checks in parallel, locally before the PR, with the CI gate after.
  - Authoring guide before the PR.
  - Performance runs before the merge, alongside Preview QA.
- **Gates:** humans approve the PR merge (an agent then merges), pick the prototype at 3C, and approve publish.
- **Content:** dev first; Brand Kit; Final Page Report is a script.
- **ADO:** states per stage, set by the orchestrator. Bugs are ADO child items + a report to the agents.
- **Original photos:** IMG_8881 / 8882 and the page-3 numbering were parked as irrelevant; the flow has since been renumbered by design.

**Teaching already given** (don't repeat unless asked)
- PRD / ADR / quality gate / fresh-context reviewer / idempotent / worktree.
- Contract-first development; static vs dynamic testing; DevSecOps.
- Plan-and-execute ("planner"); handoff contract; race conditions.
- `ser pull` vs `ser push` vs `git push`.
- Hooks vs instructions; connectors = MCP ("USB for AI tools").
- Not every step needs an agent (scripts for deterministic work).
- Content vs code environments.

---

## 8. How to start the new session

1. Open a new Claude Code session in this folder (the `agentic-sitecoreai` repo). `CLAUDE.md` loads automatically.
2. Optionally run `/idea-session` first.
3. Paste this prompt:

> Read `HANDOFF.md` in this folder in full, then `diagram-versions/CHANGELOG.md`, and look at `diagram-versions/agentic-flow-v4.4.png`. We're continuing the agentic architecture diagram for my PM. The current version is v4.4. Follow the working preferences in `CLAUDE.md`. Confirm in a few lines that you have the context, list the open items, and then wait for my next instruction.
