# Side notes

Parked thoughts to revisit **once the diagram is partially (or fully) ready**.

## 1. Topics to consider adding to the diagram

Can any or all of these be added to optimize the diagram and the agentification of the whole flow?

- [ ] Orchestration
- [ ] Memory management
- [ ] AI harness
- [ ] Plugins
- [ ] Subagents

### Other agentic concepts I may not know about yet

I'm not an AI specialist, so there may be useful agentic topics or terms I'm not aware of (for example skills or hooks). When we go through these side notes, Claude should:

- [ ] Suggest any other agentic concepts that would improve the diagram or the flow.
- [ ] Explain each one simply, and say where it would fit in the flow.
- [ ] Recommend which ones are worth adding, and why.

## 2. Pending notes section

- [ ] There is a pending notes section still to address. *(Which section this is has not been confirmed yet.)*

## 3. Parked questions from chunk 1 (steps 1–6)

Raised during the chunk 1 walkthrough and not yet answered:

- [x] **Review agent name:** ✅ decided: **PRD Review Agent** (step 3).
- [x] **Blueprint Agent timing:** ✅ decided: runs **in parallel** while the PRD is reviewed and the spec is prepared. It's an independent task, and its output is reused across all flows and future agentic development.
- [x] **Sequential vs parallel:** ✅ decided: not always parallel. It depends on the tasks: run in parallel where possible, in sequence otherwise, or a mix of both.
- [x] **Decision Agent:** ✅ decided: **kept separate**. The Ticketing Agent only lists the tasks; the Decision Agent decides sequential, parallel or both.
- [x] **Tracker:** ✅ decided: **Azure DevOps**.
- [ ] *(new)* Does each story also need its own base-component pick, or does the one shared blueprint cover every story?
- [ ] *(new)* The repo's CLAUDE.md says Jira (<TICKET-ID>) but the project uses Azure DevOps. Update CLAUDE.md later?
- [ ] **Step numbering:** Blueprint Agent 4A / Spec ready 4B (tentative), and where the Ticketing Agent sits.
