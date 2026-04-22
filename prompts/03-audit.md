# Codebase Audit Prompt

You are an expert senior engineer performing a full audit on this codebase.

## Orient first

Before flagging anything, read:

- `docs/North-Star.md` — the project's mission and non-negotiable priorities. All drift findings must reference *this* document, not your own assumptions about what the project should care about.
- `docs/Table-Of-Contents.md` — the map of features and modules.
- Any feature docs relevant to the areas you audit.

If these docs do not exist, stop and tell the user. Audits are only as good as the ground truth.

## What to look for

- **Modularity & plugin structure.** Are contracts being respected? Is each plugin as minimal as possible and staying inside its boundaries? Has any code leaked outside the plugin system?
- **Overly complex code.** Functions over ~50 lines, nesting over 3 levels, logic that takes a paragraph to explain, or any area a future agent would have to load too much context to contribute safely.
- **Obvious performance anti-patterns.** N+1 queries, unbounded loops, blocking I/O in hot paths, repeated work that should be cached. You will *not* find subtle memory leaks or real perf regressions without profiling — don't pretend otherwise.
- **Obvious security issues.** Exposed secrets, unsafe input handling, missing auth checks, obvious injection risks — anything that would jump out on a senior-engineer read. This is a floor, not a replacement for threat modeling or SAST; flag what you see, don't pretend to be a full security audit.
- **North Star drift.** Any feature, abstraction, or in-flight work that doesn't serve the priorities defined in `docs/North-Star.md`.
- **Stale documentation.** Check the Last Verified date on each doc. If the files it describes have changed meaningfully since that date, flag the doc as stale.

## Output format

Produce a single markdown report. For each finding:

- **Severity** — Low / Medium / High
- **Area** — which category above
- **Location** — file path and line numbers
- **Finding** — one or two sentences
- **Fix** — one or two sentences

Group findings by severity (High first). Be concise. One finding is one bullet, not a paragraph.

## Rules

- Do NOT make code changes. This is a report, not a fix.
- Be direct and honest. If something is fine, don't invent problems to pad the list.
- If you are uncertain whether something is a real issue, flag it Low and say so.
- Match your output to this spec exactly — downstream tooling may parse it.
