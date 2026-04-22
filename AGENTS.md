# Agent Operating Manual

You are an agent working in a project that follows the practices from the `agentic-dev-starter` repo. These rules apply to **every session you run in this project**. Read them in full before you start.

---

## On session start — read before loading any code

1. Read `docs/North-Star.md`. This defines the project's mission and non-negotiable priorities. Every decision must serve these.
2. Read `docs/Table-Of-Contents.md`. Use it as your map. Do not grep or open files that aren't referenced by a TOC entry or a feature doc you've deliberately chosen to read.
3. Identify which feature(s) your task touches. Read the relevant feature docs in `docs/` first. Code comes last, and only the code those docs point to.

If `docs/North-Star.md` is missing, stop and tell the user. Do not infer the mission.

---

## Session hygiene

- **Scratchpad early, scratchpad often.** Before any pause, compaction, or handoff, write a `WORKING_ON.md` at the project root with: what's done, what's next, open questions, files touched. Your next self reads this first.
- **`WORKING_ON.md` is current or deleted.** Stale state is worse than no state.
- **Self-document in PRs and issues.** Include enough context that a fresh agent can understand what was done and why without replaying your session.
- **If a session goes smooth-brained, stop.** Tell the user you need a reset. Do not soldier through.

---

## Plugin boundaries

If the project uses a microkernel + plugin architecture (look for a `plugins/` directory and `contract.*` files):

- **Read only what you need.** The plugin's `contract.*` file, the plugin's own folder, and the relevant feature doc. Nothing else.
- **The contract is the entire shared surface.** If a change would require the kernel or another plugin to know something not in your contract, the contract is wrong — stop and raise it.
- **No cross-plugin dependencies.** If your plugin needs data from another, route through the kernel or a named kernel service. Never import another plugin directly.
- **If you find yourself reaching outside the plugin, stop.** The boundary may be wrong, or your scope may be wrong. Ask the user.

If the project does not yet use this pattern, and is growing enough to need it, suggest running `prompts/02-architecture-setup.md` from the `agentic-dev-starter` repo.

---

## Architect vs. sub-agent — pick one per session

Never blur the roles.

**When you are the architect:**
- Hold the big picture: North Star, current state, in-flight work.
- Break work into atomic tasks with acceptance criteria.
- For each task, spawn a fresh sub-agent with *only* the context it needs: the relevant plugin/folder, the contract, the task, the acceptance criteria, and the relevant feature doc.
- Never dump the whole project into a sub-agent's context.
- Review sub-agent output before integrating.

**When you are a sub-agent:**
- Work strictly within the scope you were given.
- If you need context outside that scope, ask the architect. Do not load the whole project yourself.
- Self-document your work in the PR or issue so the architect can integrate without replaying your session.

---

## Keep docs in sync with code

If your change alters a feature's summary, contract, file list, or observable behavior, update that feature's doc **in the same PR**. Update the `Last Verified:` date at the top of any doc you touched.

Stale docs are a silent failure mode — they mislead the next agent into loading the wrong context.

---

## Audits

At the end of any long building session or milestone, run `prompts/03-audit.md` from the `agentic-dev-starter` repo. Produce the findings report. Review it with the user. Open issues for the findings you both agree to act on.

Do not fix findings mid-audit — that's a separate cycle. The audit is for producing the list; acting on it is a distinct workflow.

---

## Stop and escalate when any of these are true

- `docs/North-Star.md` does not exist.
- The plugin boundary for your task is unclear.
- The task is ambiguous enough that you would be guessing at intent.
- You are hitting the context wall partway through the task.
- You want to do something this manual forbids.

Pausing to realign costs a few minutes. Shipping half-right work costs the rest of the project.
