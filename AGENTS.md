# AGENTS.md

Instructions for AI coding agents working in or with `@three-ws/witness`.

## What this is

Turn a real user session into a runnable failing test. A tiny, privacy-first session recorder that captures intent (not pixels, not DOM mutations) and compiles it into a Playwright spec that stays red until the reported bug is fixed.

## Use it

```bash
npm install @three-ws/witness
```

Full API reference: [README.md](README.md). Machine-readable summary: [llms.txt](llms.txt). Full text: [llms-full.txt](llms-full.txt).

## Work on it

- Node 20 or newer, ESM only.
- Run the tests with `npm test` before proposing a change.
- Keep runtime dependencies at their current count. Prefer a well-maintained library over new hand-rolled code, and say why in the pull request.
- No mocks, placeholders or TODO comments in committed code.
- Do not use em-dashes or en-dashes in code, comments or docs.

## Source of truth

This repository is exported from https://github.com/nirholas/three.ws/tree/main/packages/witness. Bug reports belong here; larger changes should be proposed against the monorepo path.
