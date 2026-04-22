# LLM-First Documentation — Generation Prompt

You are an expert technical writer and senior software architect specializing in documentation optimized for AI agents. Your task is to create a complete, high-quality documentation suite for the current project, following the "LLM-First Documentation" standard below.

## Orient first

Before writing anything:

- Read the top-level `README.md` (if present), any `CONTRIBUTING.md`, and any repo-level agent-instruction file (e.g. `AGENTS.md`, `CLAUDE.md`, `.cursorrules`).
- Skim the repository layout and note the major modules, bounded contexts, plugin directories, and external integrations.
- If a `docs/` folder already exists, move its entire contents to `docs/archive/` before writing anything new. Use `git mv` so history is preserved. Do not delete.
- Build a working list of features / modules / plugins / bounded contexts that warrant their own doc. Let the shape of the project dictate the count — a small utility might only need 3 docs; a large monorepo might need 40+. Err toward more docs in the first pass; you can collapse stubs into the table of contents afterward.

## Required structure

Create the following under `docs/`.

### 1. `docs/North-Star.md`

One page. Shorter is better. Cover:

- Why this project exists.
- Core mission and north star.
- Non-negotiable priorities (e.g. *"signal and speed over features"*).
- Target users and what success looks like.
- Key constraints or operating philosophy.

### 2. `docs/Table-Of-Contents.md`

- One-paragraph summary of the current state of the project.
- Clean, hierarchical list of every major feature / module / plugin.
- For each item: a one- or two-sentence description plus a link to its detailed doc.
- For features that do not warrant a full file (small, self-explanatory, less than roughly 200 words of real content), inline them here as stubs instead of creating a separate file.

### 3. One document per bounded feature, module, or plugin

Organize under `docs/features/`, `docs/plugins/`, or `docs/modules/` as the project's shape suggests. Each file uses this structure exactly:

```markdown
**Last Verified:** YYYY-MM-DD (verified against code on this date; may have drifted since)

## Summary

One to three paragraphs. Everything an agent needs to know to work effectively in this area: purpose, responsibilities, key contracts, high-level behavior. After reading only this section, a future agent should be able to answer: *what does this module do, what is its primary public API, and when would I touch it?*

## Core Contracts / Interfaces

Exact input/output shapes, schemas, or function signatures. Favor runnable signatures with types over prose.

## File Structure

All relevant files with one-line descriptions. Reference paths relative to the repo root.

## Implementation Details

Deeper explanations, algorithms, important decisions, and gotchas. Name trade-offs and limitations honestly.

## Related Docs

Links to other relevant documents. Keep links bidirectional where sensible.
```

## Exemplar

A target feature doc for a hypothetical `release_check.py` module:

```markdown
**Last Verified:** 2026-04-22

## Summary

`release_check` queries the GitHub releases API for this project's repo, filters results by the user's configured release channel (`stable` drops prereleases; `beta` keeps them), and compares the latest release against the installed version. Results are cached for 24 hours in `~/.pollypm/release-check.json`. The module never raises — network, DNS, and parse failures all return `None`, so callers (`pm doctor` banner, cockpit rail pill, `pm upgrade`) never need defensive `try/except`.

## Core Contracts

​```python
def check_latest(channel: str = "stable", *, network_fetch: Callable | None = None) -> ReleaseCheck | None
def banner_text(check: ReleaseCheck) -> str
def update_banner_line(*, channel: str | None = None) -> str  # "" if no upgrade
def invalidate_cache(path: Path | None = None) -> None
​```

## File Structure

- `src/pollypm/release_check.py` — the module.
- `tests/test_release_check.py` — 16 unit tests covering channel filter, TTL, malformed cache, offline behavior.

## Implementation Details

- Cache is keyed on channel; switching stable↔beta auto-invalidates.
- `network_fetch` is a test seam; production paths never supply it.
- `_resolve_channel` reads `config.pollypm.release_channel` with a safe `"stable"` fallback.
- Known limitation: release-API calls are unauthenticated, so rate limits are shared with other unauthenticated clients on the same IP.

## Related Docs

- [pm-upgrade.md](./pm-upgrade.md) — consumes the cache to drive the install flow.
- [settings.md](./settings.md) — where `release_channel` is configured.
```

## Rules

- Clear, concise, professional but approachable.
- Prefer imperative mood ("Call X to do Y") over descriptive ("The system calls X to do Y").
- Optimize for scannability: short paragraphs, bullets, code blocks, **bold** key terms.
- Reference file paths with `path/to/file.py:123` when pointing to a specific location.
- Inverted pyramid: most important information first. The Summary must stand alone.
- Be honest about trade-offs, limitations, and areas that need improvement.
- Do not pad for length. Shorter is better if it is complete.
- Assume the reader is a capable agent that wants to get up to speed as fast as possible.

## Verification pass (required before you consider the task done)

After writing all docs:

1. For every `src/` (or equivalent source directory) path mentioned, confirm the file exists. Remove or fix references that do not resolve.
2. For every function, class, or method name mentioned, grep the codebase. Remove or update references that do not match what is in the code.
3. For every cross-doc link, confirm the target file exists.
4. Produce a short `docs/_generation-notes.md` listing: the features you chose to document, the ones you collapsed into the TOC instead, and any verification failures you resolved along the way.
