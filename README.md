# Agentic Dev Starter

A small repo that codifies the context-management practices from [Managing Context While Vibe Coding](./context-management.md) for LLM-driven projects. Point an agent at this repo and tell it to apply the approach to your project.

---

## If you're a human reading this

This repo is the companion to the article [Managing Context While Vibe Coding](./context-management.md) — start there. It explains the *why* behind every file below. (The article will also be published on X.)

Once you've read it, the fastest way to use this repo is to send your agent the GitHub URL and tell it "apply this approach to my project." The instructions in the next section are written for that agent.

---

## If you're an agent told to apply this to a project

Do these steps in order. Each is self-contained, but they build on each other — don't skip ahead.

1. **Read [`context-management.md`](./context-management.md).** This is the intent behind the practices. You don't need to memorize it — just know what the user is trying to achieve so you can make judgment calls.

2. **Install `AGENTS.md` in the target project's root.** Copy [`AGENTS.md`](./AGENTS.md) to the root of the project you're working on. If the project already has `AGENTS.md`, `CLAUDE.md`, `.cursorrules`, or similar, merge the content — do not overwrite. This file becomes the always-on operating manual for every future agent session in that project.

3. **Generate docs.** Run [`prompts/01-docs-generation.md`](./prompts/01-docs-generation.md) against the project. This produces `docs/North-Star.md`, `docs/Table-Of-Contents.md`, and per-feature docs. Every subsequent step depends on these existing.

4. **Set up the architecture.** Run [`prompts/02-architecture-setup.md`](./prompts/02-architecture-setup.md). This proposes (and, on user approval, scaffolds) a microkernel + plugin architecture grounded in the North Star.

5. **Audit on an ongoing cadence.** Run [`prompts/03-audit.md`](./prompts/03-audit.md) at the end of long building sessions or milestones. Triage findings with the user; act on them in a separate cycle.

**Stop and ask the user first if any of these are true:**

- The project's mission is unclear enough that you would be guessing when writing the North Star.
- The project has strong existing conventions that would fight this approach.
- You were given a partial task that doesn't map cleanly to one of the steps above.

---

## Model routing (recommended)

| Role | Model | Why |
|------|-------|-----|
| Architect | Opus | Expensive, but touches everything — quality compounds. |
| Sub-agents | GPT 5.4 / Haiku-class | Fast, cheap, and good at tightly-scoped execution. |
| Review | Opus | Different pass, different model, catches what the executor missed. |

---

## Files

| Path | Purpose |
|------|---------|
| [`context-management.md`](./context-management.md) | The article. The "why." |
| [`AGENTS.md`](./AGENTS.md) | Always-on operating manual. Copy into each project. |
| [`prompts/01-docs-generation.md`](./prompts/01-docs-generation.md) | Generate LLM-friendly docs (run first). |
| [`prompts/02-architecture-setup.md`](./prompts/02-architecture-setup.md) | Scaffold microkernel + plugin architecture (run second). |
| [`prompts/03-audit.md`](./prompts/03-audit.md) | Audit against these practices (run recurringly). |

---

## Scope

These practices are designed for projects where an LLM is doing most or all of the coding. The setup overhead that would be heavy for a human-authored weekend script is close to zero when an agent is typing — so the threshold for "worth doing" is lower than it used to be.

Partial adoption is fine. If you only want the docs discipline, run `01` and skip the rest. `AGENTS.md` is trim-friendly — delete sections that don't apply to the project you're in.

---

## License

Do whatever you want with it. Fork, adapt, steal.
