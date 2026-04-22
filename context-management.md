# Managing Context While Vibe Coding

![The Nuggies Arc: Vibe Coding in 2026](./nuggie-meme.png)

*Don't care about any of this and just want to make your agent coding better? Have your agents go read this: https://github.com/samhotchkiss/agentic-dev-starter*

---

## The Wendy's arc



You start a project. Claude is a genius. He makes *you* feel like a genius. You can't wait to keep going. You get to a first usable state and it's awesome. Then you think of a hundred features you want to add, and you just start going for it. You're adding. You're growing. You're so excited about what this is going to be. Claude works all night and all day. You go in to test it... and... everything is fucking broken.

Welcome to the Wendy's arc. One minute you're building a rocketship, the next you're back in the drive-thru making Nuggies.

## Why it happens

The problem with every frontier model in 2026 is that they are fucking brilliant when they're fresh. When the context window is small, it feels like they can do damn near anything.

Then the project grows. The amount they need to hold in context grows rapidly. Older parts of the conversation get compacted. Detail and nuance are lost, and rather than cop to it, they start to hallucinate. Push further and they've gone fully smooth-brained.

Your failure modes compound:

- **Hallucination** — confident answers grounded in nothing
- **Instruction drift** — the rules you set up 50 messages ago quietly stop mattering
- **Recency bias** — whatever you said last dominates; the plan from the top of the session evaporates
- **Needle-in-haystack collapse** — the critical fact *is* in context, but the model can't find it

You start a new session. It works a little better — until your codebase itself gets big enough that even fresh sessions are dumb.

At this point most people give up their dreams of wealth, fortune, and fame and go back to Wendy's to make some more Nuggies.

## The secret

The secret isn't bigger context windows. It's ruthless context management.

It's the most important skill you can learn for building vibe coding projects. Honestly, the most valuable skill for LLM users, period.

Here's the system.

---

## Start here: the tactical stuff

Before you rearchitect your whole project, most people get 80% of the benefit from five moves they can do today:

1. **Start new sessions early and often.** If you're hitting smooth-brain symptoms, don't fight through. Reset.
2. **Keep a scratchpad file.** Before any long break, have the model dump its current mental state (what's done, what's next, open questions) to a file. Your next session reads this first.
3. **Kill bad sessions fast.** If messages start to smell off, don't spend an hour trying to steer. Reset.
4. **Write short, living TODO docs.** Not big planning docs. A `WORKING_ON.md` that is current or deleted.
5. **Stay in the loop.** You are the taste. Run the product — not the code. Use what the agent built. When something feels off, stop and say so before approving another round. The moment you stop being the taste maker, you're headed back to Wendy's.

If you're doing all five and still hitting the wall, then you're ready for the heavier stuff below.

---

## 1. Build with a microkernel + plugin architecture

The most important thing you can do to build a maintainable, working vibe-coded app is to keep the core of your app as tiny as possible. Everything lives in self-contained modules/plugins. Some plugins are core/required. Others are optional. Plugins can have sub-plugins.

![Traditional Monolith vs Microkernel / Plugin Architecture](./microkernel-architecture.png)

Why this wins for agentic coding:

- **Tiny surface area per task.** When something breaks, you point a fresh agent at *one* plugin, not your whole project. They don't have to hold the whole thing in context because they don't need to.
- **Refactors get cheap.** Our ability to change direction with agentic coding is 10x what it used to be. Plugin architecture lets you swap one piece at a time without detonating the rest.
- **Testing actually works.** You've felt this: Claude "practices TDD," writes 200 tests, they all pass, the product is still fucking broken. Plugin-based architecture gives you firm, testable bounds on what went in and what came out — contract tests, not vibe tests.

This approach may have been too heavy for small projects a few years ago, but when you're vibe coding, the overhead of properly architecting is very close to zero. 

### Pro move for distributed products

Make it dead simple for users to replace any plugin without forking your core. This turns users into co-builders and creates super sticky products. (Not strictly a context-management play — but plugin architecture is what makes it possible.)  Also, make sure that the pathway to replace these plugins is documented so an agent can jump in and do it the right way. 

---

## 2. Architect + sub-agents

This workflow gets peak performance from frontier models.

**One Architect agent** holds the big picture — North Star, overview, current state. It breaks work into small atomic tasks with clear acceptance criteria.

**For every task, spin up a fresh sub-agent** with *only* the context it needs:

- The relevant plugin/folder (nothing else)
- The exact contract it must follow
- The task + acceptance criteria
- The relevant docs

Sub-agent does the work. Architect reviews and integrates.

Every model stays in the genius zone. Nothing ever sees the whole codebase at once.

### Model routing

Don't run everything on one model. My current setup:

- **Architect: Opus** — expensive, but it touches everything, so the quality compounds.
- **Sub-agents: GPT 5.4** — fast, genuinely good at executing a tightly-scoped task.
- **Review: Opus** — the architect reviews sub-agent output before integrating. Different pass, different model, catches stuff the executor missed.

Matching the model to the job is one of the biggest practical levers in multi-agent systems. Most people leave it unused and wonder why they're broke.

### The catch (because there is one)

Sub-agent isolation has a cost. Every fresh sub-agent re-loads context — cache miss, tokens, latency. Sub-agents can't learn from each other's in-session work, so it's important that all of these agents are primed to self-document their work within the issue/PR and that you have well-written documentation.

---

## 3. Force agents to write LLM-friendly docs

Good docs let agents understand the scope and shape of your project without pulling thousands of lines of code into context.

**One short North Star.** Why this project exists. What the top 2-3 things that matter most to you are. For my news ingester, my priorities are signal and speed. That's it. Every agent who touches the project reads this first.

**A strong table of contents.** This is the map so agents don't start loading context willy-nilly. It includes:
- a summary of the state of the project
- every feature with 1-2 sentences about what it does
- a path to the detailed doc for that feature

**Every feature doc starts with a summary + file references.** Agents read top-down and stop when they've learned enough. Use an inverted-pyramid structure, like a well-written newspaper article, so they don't read more than they need to. Name the files (and line numbers where you can) that comprise the feature up front.

**Last-modified date at the top of every doc.** So you can spot staleness at a glance.

Put them all in `/docs`. Every new agent session starts by reading the North Star + relevant docs.

Here's a prompt you can use to generate these docs: [prompts/01-docs-generation.md](https://github.com/samhotchkiss/agentic-dev-starter/blob/main/prompts/01-docs-generation.md)

---

## 4. Run regular audits

I always run these at the end of every prolonged building session. The cadence should match your rate of change.

Have your agents look for:

- Areas where your modular/plugin structure has broken down
- Overly complex code
- Non-performant code
- Areas where the product has diverged from the North Star
- Docs out of alignment with the actual product
- Obvious security issues (exposed secrets, unsafe input handling, missing auth checks, anything that would jump out on a senior-engineer read)

One caveat on that last one: this sweep catches glaring stuff but isn't a real security program. For a proper posture you still want threat modeling, SAST, and dependency scanning. Treat the audit as the floor, not the ceiling.

**Audits produce a list, not action.** I will review the audit with the agent and have them add GitHub issues for every finding that we agree should be acted on, and then I have Codex go out and generate PRs, and then I have Opus come back and review them and merge them.

Here's a prompt you can use to run the audit: [prompts/03-audit.md](https://github.com/samhotchkiss/agentic-dev-starter/blob/main/prompts/03-audit.md)

---

## TL;DR

Frontier models are geniuses when fresh and smooth-brained when full. The whole game is keeping them fresh. You do that by:

1. Doing the tactical stuff (reset early, scratchpad, stay in the loop)
2. Giving each agent the smallest possible slice of your project (microkernel + plugins)
3. Splitting "plan the work" from "do the work" (architect + sub-agents, with the right model on each)
4. Making your docs do the heavy lifting so agents don't have to read code
5. Auditing on a real cadence with a real response protocol

Do these and you just might stay out of Wendy's.
