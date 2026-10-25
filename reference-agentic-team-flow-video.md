# Reference: "The Agentic Team (and flow)"

| | |
|---|---|
| **Source** | https://www.youtube.com/watch?v=n8ELPP5rfwg |
| **Author** | Christian Hahn |
| **Length** | ~9 min |
| **Captured** | 2026-10-24, from YouTube's auto-generated English captions |

> **How to use this file:** plain reference point only. Whatever we plan and build must be specific to our own project, not a copy of this flow. The video's author says the same: it is an example, not a copy-paste blueprint.

This file is a paraphrased summary, not the verbatim transcript.

---

## The team

One human plus a set of specialist agents, each with a single role.

| Role | Responsibility |
|---|---|
| Manager | The human operator. Gives the initial instruction and approves at the gates. |
| Team Lead | Orchestrator. |
| Rubber Ducky | Idea facilitator. |
| Product Manager | Writes the spec (PRD). |
| Software Architect | Blueprint and architecture decision records (ADRs). |
| UI Designer | Design directions and clickable prototypes. |
| Lead Developer | Breaks the spec into tasks. |
| DevOps Engineer | Provisions infrastructure and publishes. |

## The flow, phase by phase

1. **Intake and spec.**
   - The operator and an authoring agent go back and forth until there is a PRD.
   - **Quality gate:** a second, fresh agent challenges the PRD. They repeat until the operator and the agents agree.
2. **Discovery.**
   - The DevOps agent connects to the existing SitecoreAI tenant.
   - It reads the content models and builds an exact component inventory, which later phases build on instead of guessing.
3. **Architecture and design.**
   - The architect writes decision records, one decision at a time, each with its context and consequences.
   - The UI designer builds up to 3 genuinely different working, clickable prototypes.
   - **Gate 2:** the chosen prototype becomes the strict implementation contract.
4. **Task breakdown.**
   - The lead developer splits the spec into self-contained tasks.
   - Dependency analysis groups them into independent lanes, and one agent owns a lane end to end.
   - Each task carries everything it needs, so nothing blocks mid-build. The lanes converge only at the end, in the codebase.
5. **Provisioning.**
   - The DevOps agent creates the SitecoreAI resources before any feature code exists.
   - It uses create-or-skip logic, so it is idempotent: running it again changes nothing.
6. **Build.**
   - For each task, a fresh developer, reviewer and tester run as a triad, and issues loop back until the task passes.
   - The default runs one lane at a time, so each task starts clean and nothing accumulates between tasks.
   - An experimental mode runs strictly independent tasks in parallel, each in its own git worktree.
7. **Ship.**
   - A doc-writer agent makes sure the documentation is complete.
   - A fresh product manager agent checks the delivered work against the original requirements.
   - The developer agent commits, pushes and opens a PR. The human keeps control of the merge and deployment.
8. **Content and publish.**
   - One agent learns which components are available, both visually and in the data model.
   - A second agent uses that, plus a brand kit, to generate content and manage pages.
   - A content tester agent reviews everything and sends off-brand work back for a rewrite.
   - Everything publishes at once after the human's final approval.

## Principles the video stresses

- A fresh, independent agent reviews each stage; the author never grades their own work.
- Human approval at key gates: spec, prototype, merge and publish.
- Read the real system first, and don't guess.
- The infrastructure is real before the first line of code.
- Tasks are self-contained, so they can't block each other mid-build.

## Author's closing points

- No silver bullet. The pipeline improved through monitoring and repeated changes to how agents are guided, how work is handed off, and how resources are used.
- It is tuned for SitecoreAI projects, with custom steps, commands and skills.
- Build your own flow. The methods and learnings matter more than this exact setup.

## Vocabulary

| Term | Meaning |
|---|---|
| PRD | Product requirements document: a written spec of what to build and why. |
| ADR | Architecture decision record: a short note on one technical decision, its context and consequences. |
| Quality gate | A checkpoint that work must pass before moving on, either a human approval or an automated check. |
| Fresh-context reviewer | A new agent, with none of the author's conversation history, reviews the work so it doesn't inherit the author's bias. |
| Idempotent | Safe to run again, because a repeat run changes nothing. |
| Worktree | A separate working copy of the same git repo, so parallel agents don't edit the same files. |
